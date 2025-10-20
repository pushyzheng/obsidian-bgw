---
{"dg-publish":true,"dg-permalink":"Go 算法技巧","permalink":"/Go 算法技巧/"}
---



## 1. 字符串

### 字符串操作

字符串底层是 UTF-8 编码的字节序列，可以直接进行索引操作：

```go
s := "Hello World"

fmt.Println(s[0]) // 72

// 如果需要进行字符的话，则可以重新转换为 string 类型
fmt.Println(string(s[0]))
```

如果需要处理**多字节字符**（中文）时，则使用 `range` 关键字来处理：

```go
s := "Hello World"

for _, ch := range s {
	if ch == 'H' {
		// 在判断 ch 是否为某个字符时，可以使用单引号字面量
		...
	}
}
```

或者直接转换为 rune 数组来处理：

```go
s := "Hello World"
runes := []rune(s)

fmt.Println(string(runes[0])) // H
```

### 拼接

使用 bytes.Buffer 或 strings.Builder 拼接字符串

```go
var sb strings.Builder
sb.WriteString("Hello")
sb.WriteString("World")
fmt.Println(sb.String())
```

### 头尾双指针

可以用来反转字符串、判断回文数操作等等：

```go
r := []rune(s)
for i, j := 0, len(r)-1; i < j; i, j = i+1, j-1 {
	// r[i] 和 r[j] 分别对应头尾两个 rune 字符
}
```

## 2. Array/Slice

数组的深拷贝

```go
arr := []int{1, 2, 3}

newArr := append([]int(nil), arr...)
```

### 排序

数组的排序：

```go
sort.Ints(nums)
```

自定义排序：

```go
sort.Slice(nums, func(i, j int) bool {
	...
})
```

## 3. Map

## 4. Set

由于 Go 里边没有内置的集合，可以使用 Map 来代替集合，并且可以使用 `struct{}` 类型来优化内存占用（[[src/go/Go Struct#空结构体struct{} 的空间占用情况和用途？\|空结构体struct{} 的空间占用情况和用途]]）

```go
set := make(map[int]struct{})
// 添加元素
set[1] = struct{}{}
// 判断是否存在
if _, exists := set[1]; exists {
	...
}
```

如果不考虑空间占用情况，使用 bool 的类型更简洁：

```go
set := map[int]bool{}
set[3] = true
if set[3] {
	... // 存在
}
```

## 5. Stack 栈

在 Go 中没有内置的 stack 类型，但可以用 切片来实现一个高效、简洁的栈：

```go
stack := []int{}

stack = append(stack, 1)          // push
stack = stack[:len(stack)-1]      // pop

top := stack[len(stack)-1]        // top
```

## 6. Queue 队列

```go
queue := []int{}
queue = append(queue, 1)        // Enqueue
queue = queue[1:]               // Dequeue

front := queue[0]               // Peek
```

## 7. 双向链表


```go
import (
	"container/list"
)

func main() {
	// 初始化
	var l *list.List = list.New()
	
	// 添加到头部
	var first *list.Element = l.PushFront(1)
	
	// 添加到尾部
	var second *list.Element = l.PushBack(2)
	
	// 头部
	head := l.Front()
	// 尾部
	tail := l.Back()
}
```

移动元素（传入的是 `list.Element` 类型）：

```go
// 将 1 移动到尾部
l.MoveToBack(first)

// 将 2 移动到头部
l.MoveToFront(second)
```

删除元素：

```go
// 返回被删除的元素
element := l.Remove(first)
```

需要注意的是，由于 list 不支持泛型，所以最终读取值需要进行强转：

```go
val, _ := l.Front().Value.(int)
fmt.Println(val)
```

## 8. 闭包

通过闭包，可以“捕获”并引用了外部函数作用域中的变量，让每次递归调用都**共享**一个变量。

同时，如果闭包的匿名函数需要调用自己，需要：
1. 先**声明**函数变量
2. 再给它**赋值**为一个函数

```go
func main() {
	var vals []int

	var preorder func(root *TreeNode)
	preorder = func(root *TreeNode) { 
		// 这样，闭包内部才能自己调用自己 preorder 方法
		// 同时，访问 vals 变量都是共享的
	}

	// 调用匿名函数
	preorder(root)
}
```

参考：[[leetcode/二叉树的前序遍历\|二叉树的前序遍历]]