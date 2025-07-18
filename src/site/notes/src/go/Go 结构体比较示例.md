---
{"dg-publish":true,"dg-permalink":"Go 结构体比较示例","permalink":"/Go 结构体比较示例/"}
---


## 可比较的结构体

```go
type User struct {
	Name string
}

func main() {
	u1 := User{Name: "a"}
	u2 := User{Name: "a"}
	// u1 < u2 与 u1 > u2 都是不合法的
	fmt.Println(u1 == u2)
}

// true
```

但是如果是比较指针类型，则比较的是指针的地址，结果不相等：

```go
type User struct {
	Name string
}

func main() {
	u1 := &User{Name: "a"}
	u2 := &User{Name: "a"}
	fmt.Println(u1 == u2)
}
// false
```

## 不可比较的结构体

```go
type User struct {
	Name string
	Say  func()
}

func main() {
	u1 := User{Name: "a"}
	u2 := User{Name: "a"}
	fmt.Println(u1 == u2)
}
```

编译错误：

![Pasted image 20250717173312.png](/img/user/attachments/images/Pasted%20image%2020250717173312.png)

但是如果是指针类型，则不会编译错误，但是结果仍然是不相等：

```go
type User struct {
	Name string
	Say  func()
}

func main() {
	u1 := &User{Name: "a"}
	u2 := &User{Name: "a"}
	fmt.Println(u1 == u2)
}

// false
```

如果出现不可比较的情况下，可以尝试使用 [[src/go/Go 反射#reflect.DeepEqual 和 == 有什么区别？\|reflect.DeepEqual]] 来比较，同时也会递归比较 Data 中 Map 所有的键值对：

```go
type User struct {
	Name string
	Data map[string]string
}

func main() {
	u1 := User{Name: "a", Data: map[string]string{
		"a": "B",
	}}
	u2 := User{Name: "a", Data: map[string]string{
		"a": "B",
	}}

	fmt.Println(reflect.DeepEqual(u1, u2))
}

// true
```