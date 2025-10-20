---
{"dg-publish":true,"dg-permalink":"ExGraph 自动注入实现","permalink":"/ExGraph 自动注入实现/","tags":["项目"]}
---


## 实现方式

ExGraph 的自动注入是**基于脚本**生成代码的，而不是通过反射来实现。

| 类型         | 优点             | 缺点                       |
| ------------ | ---------------- | -------------------------- |
| 手动注入     | 简单             | 不适合复杂场景             |
| 反射         | 灵活             | 但性能开销大，不够类型安全 |
| 代码生成注入 | 类型安全，性能好 | 调试复杂，且多一个编译步骤 |

通过代码生成，对结构体的标签进行解析，从Container容器中取出对象或注入对象。

## 实现原理

### Container 容器

Container 是对象容器，用于存储所有对象的实例，相当于对象的注册中心。

```go
type container struct {  
    // instances 保存整个请求中的需要传递的对象  
    instances *sync.Map  
}
```

注册对象：
1. 通过反射获取到对象的指针类型（`reflect.ValueOf(val).Type()`）
2. 获取到指针指向的实际类型（`typPtr.Elem()`）
3. 判断是否重复，如果没有重复的话则注入到 instances 中

加载对象：
1. 获取传入参数的反射值（`reflect.ValueOf`），期望是个指针
2. 从容器中尝试读取这个类型对应的实例（`valr.Type().Elem()`）
3. 如果从容器找到该对象的话，则调用 Elem 的 `Set` 方法，将实例设置给该指针引用

### 注入&导出

定义注入&导出的标签属性：
- **inject**：注入
	- canLost：会校验注入的对象是否不在容器中
	- canNil：会校验注入的对象是否为 nil
- **extract**：导出
	- canLost：会校验是否未导出该类型对象
	- canNil：会校验导出的对象是否为 nil

如下所示：

```go
type OperatorClient struct {
	Adapt   *types.Adapt  `inject:"canLost=true"`
	Request ghttp.Request `inject:""`

	Client `extract:""`
}
```

### 包装代码

通过 exg-cli 命令行工具，读取算子配置加载所有的算子文件，通过**模板机制**为每个算子生成包装代码，主要做了以下的几件事：
1. init：在算子文件的初始化方法中，调用 engine.RegisterOperator 将算子注册到注册中心
2. 对算子本身的 DoImpl 方法执行 AOP：
	- 注册 defer 方法，将算子的所有属性都**设置为 nil**，便于回收
	- 对于携带 inject 标签的属性，从容器中加载对象
	- 对于携带 extract 标签的属性，在算子的 DoImpl 方法完后，将导出的对象注册到容器中

初始化代码：

```go
func init() {
	err := engine.RegisterNormalOperator("OperatorAiChat", 808, "", func() engine.Job {
		return &OperatorAiChatWrap{}
	})
	if err != nil {
		log.Fatal(err)
	}
}
```