---
{"dg-publish":true,"dg-permalink":"Go 反射","permalink":"/Go 反射/"}
---


## Go 反射的三大定律？

1. 定律一：反射是从**接口变量**到**反射对象**的转换（interface{} → reflect.Value / reflect.Type）。
2. 定律二：反射对象可以转换回接口变量（reflect.Value.Interface()）。
3. 定律三：要修改反射对象的值，必须保证该值是**可设置的**（CanSet()为true），通常需要传入指针类型变量

## 常用反射操作有哪些？

| 功能             | 用法                                                                                     |
|------------------|------------------------------------------------------------------------------------------|
| 获取类型         | `reflect.TypeOf(x)`                                                                      |
| 获取值           | `reflect.ValueOf(x)`                                                                     |
| 判断类型种类     | `v.Kind()`                                                                               |
| 访问结构体字段   | `val.Field(i)` 、`val.FieldByName("FieldName")`                                           |
| 读取字段 Tag     | `reflect.Type.Field(i).Tag.Get("tagName")`                                               |
| 修改字段值       | `reflect.Value.Field(i).SetXXX()`（前提：字段可设置且变量是指针）                           |
| 调用方法         | `v.MethodByName("MethodName").Call(args)`                                                |
