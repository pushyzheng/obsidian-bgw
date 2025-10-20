---
{"dg-publish":true,"dg-permalink":"Go 基础","permalink":"/Go 基础/","tags":["Go"]}
---


#Go 

## go 都有哪些数据类型？

1. 值类型
	- string
		- 默认编码都是 UTF-8
	- [[src/go/Go String#rune 类型的定义？\|rune]]
	- Array
	- struct
		- 聚合的数据类型
		- 由**多个任意**类型的值聚合成的实体
	- interface
		- 用来定义行为
		- 结构体实现了接口定义的行为（方法），则称实现了该接口
2. 引用类型
	- Slice
	- map
		- 键值对的无序集合

## slice 和数组的区别？

1. ==切片（slice）==
	- 底层数据是数组，对数组的**封装**，用来描述一个数组的片段
	- 是数组的一个引用，会生成一个**指向数组的指针**
	- 非常灵活，可以**动态扩容**
	- 底层结构
		- array unsafe.Pointer
		- len
		- cap
2. ==数组（Array）==
	- 就是一片连续的内存，是**定长**的

## 切片中的 len 和 cap 有什么区别?

- len（长度）
	- 表示当前切片中**实际包含**的元素个数
- cap（容量）
	- 表示从切片的第一个元素开始，到其底层数组末尾之间**可以容纳的最大元素**个数

![Pasted image 20250722171141.png](/img/user/attachments/images/Pasted%20image%2020250722171141.png)

```go
s := []int{2, 3, 5, 7, 11, 13} // len=6, cap=6

s = s[:0]         // len=0, cap=6，切片变为空，但底层数组容量依然为6
s = s[:4]         // len=4, cap=6，切片长度4，但底层数组还可存6个元素
s = s[2:]         // len=2, cap=4，切片从第3个元素开始，容量变为4（数组剩余元素）
```

## make([]int, 0, 10) 和 make([]int, 10) 的区别？

- `make([]int, 0, 10)`
	- cap = 10，len = 0
	- 为底层的切片提前分配好容量，但是没有元素
	- 不可以直接访问和修改
- `make([]int, 10)` 
	- cap = 10，len = 10
	- 创建一个长度为 10 的切片，同时分配默认元素 0
	- 可以直接访问和修改

## slice 的扩容机制？

- 1.7 及以前
	1. 如果 cap < 1024，进行**双倍**扩容
	2. 如果 cap >= 1024，每次增长大约 1.25 倍
- 1.8+
	1. 如果 cap < 256，进行**双倍**扩容
	2. 如果 cap >= 256
		- 开始进行缓慢的增长（小于 2.0）
		- 并随着切片容量的变大，增长**比例降低**，逐渐向着 **1.25** 进行靠拢

数据迁移：
- 会为切片分配一个**新的底层数组**，将旧数组的数据复制到新数组中
- 扩容后的切片会指向新的数组，而旧数组则被垃圾回收（如果没有其他引用）

## nil slice 和 empty slice 的区别？

```go
// nil slice
var ns []int
// empty slice
es := make([]int, 0)
```

- nil slice 地址**无实际指向**
- 所有的 empty slice 都指向**固定地址**，为共享的 zero 数组

## new 和 make 的区别?

1. make
	- 仅用来分配 *slice、map、chan* 的数据
	- 返回**引用**
2. new
	- 可分配**任意**类型的数据，如 *map、chan*
	- 返回指向这块内存的**指针**

## select 关键字有什么用？

select 是多路复用 channel 的监听器：
- 同时等**多个** Channel，哪个先准备好就执行那个 Case
- 常用于**超时的处理**：select + time.After
- 并可以通过 default 默认分支来**防止阻塞**

例如：

```go
func main() {
	ch := make(chan string)
	go func() {
		// 模拟一个耗时操作
		time.Sleep(2 * time.Second)
		ch <- "完成任务"
	}()
	select {
	case res := <-ch:
		fmt.Println("收到：", res)
	// time.After 内部创建了一个定时器 NewTimer
	// 当定时其到点后，将会往方法返回的 Channel 设置值
	case <-time.After(1 * time.Second):
		fmt.Println("超时了，放弃等了")
	}
}
```