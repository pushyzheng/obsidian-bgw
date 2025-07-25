---
{"dg-publish":true,"dg-permalink":"Go 内存管理与分配机制","permalink":"/Go 内存管理与分配机制/","tags":["Go"]}
---


#Go

## Go的堆和栈的区别？

1. ==栈（Stack）==
	- 每个 Goroutine 都有自己的栈，存**局部变量、函数调用信息**
	- 函数结束后会自动回收
	- 访问快，开销小
2. ==堆（Heap）==
	- 存储动态分配的内存，如切片扩容、返回引用等
	- 需要靠 GC 回收
	- 开销比较大

## 逃逸分析如何决定变量分配在栈还是堆？

逃逸分析：编译器在编译时会判断：这个变量是不是会“逃出”当前**函数的作用域**

例如：变量的地址被返回（返回指针），传给外部协程，闭包引用

```go
// 变量的地址被返回
func foo() *int {
    a := 10
    return &a
}

// x 变量被传给外部协程
func main() {
    x := 42
    go func() {
        fmt.Println(x) 
    }()
}

// y 被闭包引用，逃逸到堆
func makeAdder() func() int {
    y := 100
    return func() int {
        y++
        return y 
    }
}
```


## 如何避免内存泄漏


1. 关闭资源
	- 比如文件、网络、Channel 需要调用 close 函数，**GC 不会回收**
	- 可以通过 **defer** 来实现
2. 避免全局变量被乱引用
3. sync.Pool 用完放回去

## Golang中内存分配管理的对象

| 类型   | 大小        |
|:------ |:----------- |
| 微对象 | (0, 16B)    |
| 小对象 | [16B, 32KB] |
| 大对象 | (32KB, +∞)  |

## Go 内存的三级缓存架构

Go内存分配器采用三级缓存体系：

mspan：golang中内存分配管理的**基本单位**

1. ==mcache==
	- [[src/go/Go 协程#GMP 模型\|逻辑处理器 P]] 拥有独立的 mcache，作为协程的本地缓存，无锁操作，**减少锁竞争**
	- 快速分配**微对象**（<16k）
2. ==mcentral==
	- mcache 无空闲内存，会向 mcentral 请求内存块
3. ==mheap==
	- mcentral 从 mheap 申请更大的内存块，直接管理 arena 的大块内存
	- **大对象**直接在此分配

## Go GC 的三色标记清除算法

Go 支持写屏障，所以GC过程和程序可以并发运行。

- ==白色==
	- 尚未被访问的对象，**初始时**所有对象均为白色
	- 如果**标记结束后**，仍为白色即为垃圾对象，**待回收**
- ==灰色==
	- 已被发现但其引用的对象**尚未被全部**扫描
	- 处于**中间状态**
- ==黑色==
	- 已被扫描且其**所有**引用对象也被访问过的对象
	- 表示该对象及其引用**都是存活的**


