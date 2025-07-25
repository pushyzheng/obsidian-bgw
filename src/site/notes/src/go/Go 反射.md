---
{"dg-publish":true,"dg-permalink":"Go 反射","permalink":"/Go 反射/"}
---


## Go 反射的三大定律？

1. 定律一：反射是从**接口变量**到**反射对象**的转换（interface{} → reflect.Value / reflect.Type）。
2. 定律二：反射对象可以转换回接口变量（reflect.Value.Interface()）。
3. 定律三：要修改反射对象的值，必须保证该值是**可设置的**（CanSet()为true），通常需要传入指针类型变量

## 常用反射操作有哪些？

| 功能               | 用法                                                              |
| ------------------ | ----------------------------------------------------------------- |
| 获取类型           | `reflect.TypeOf(x)`                                               |
| 获取值             | `reflect.ValueOf(x)`                                              |
| 获取指针的真实类型 | `v.Type().Elem()`                                                                  |
| 判断类型种类       | `v.Kind()`                                                        |
| 访问结构体字段     | `val.Field(i)` 、`val.FieldByName("FieldName")`                   |
| 读取字段 Tag       | `reflect.Type.Field(i).Tag.Get("tagName")`                        |
| 修改字段值         | `reflect.Value.Field(i).SetXXX()`（前提：字段可设置且变量是指针） |
| 调用方法           | `v.MethodByName("MethodName").Call(args)`                         |

## Go 反射中的 TypeOf 和 ValueOf 的区别?

- ==reflect.TypeOf==
	- 返回的是变量的**动态类型**信息，不包含变量的具体值
	- 效率稍高
- ==reflect.ValueOf==
	- 返回的是变量的**具体值**的封装，不仅包含变量的类型，还能**访问和操作**变量的实际数据
	- 可以通过它进一步操作值（例如获取字段、调用方法等）

## reflect.DeepEqual 和 == 有什么区别？


- == 运算符：只能比较基本类型，但 map、slice、func 不支持
- reflect.DeepEqual: 可以比较任意两个值，能够递归地进行深度比较；例如 Map：比较所有的键值对