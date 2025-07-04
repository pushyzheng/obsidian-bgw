---
{"dg-publish":true,"dg-permalink":"Go 基础","permalink":"/Go 基础/"}
---


#Go 

## go 都有哪些数据类型？

1. 值类型
	- string
		- 默认编码都是 UTF-8
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

1. slice 的底层数据是数组
	- 对**数组的封装**，用来描述一个数组的片段
	- 是数组的一个引用，会生成一个**指向数组的指针**
	- 非常灵活，可以动态地扩容
	- 底层结构
		- array unsafe.Pointer
		- len
		- cap
2. Array
	- 就是一片连续的内存，是定长的

## slice 的扩容机制？

- 1.7 及以前
	1. 如果 cap < 1024，进行**双倍**扩容
	2. 如果 cap >= 1024，每次增长大约 1.25 倍
- 1.8+
	1. 如果 cap < 256，进行**双倍**扩容
	2. 如果 cap >= 256
		- 开始进行缓慢的增长（小于 2.0）
		- 并随着切片容量的变大，增长**比例降低**，逐渐向着 **1.25** 进行靠拢

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