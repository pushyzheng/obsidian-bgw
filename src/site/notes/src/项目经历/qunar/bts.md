---
{"dg-publish":true,"dg-permalink":"bts","permalink":"/bts/"}
---


#项目 

报价包装业务主要依赖策略实现

## 背景

发现一些代理商的报价低于直采的，比直采低很5-10元，而高于直采的报价只比直采高1-2元。

所以我们将利用代理商的低价深度优势做一个资金池，调配代理商的追价范围，以有效提升代理商产单。

## 整体模块

- 货架：虚拟代理商酒店、报价
- 策略：容差计算，**报价复制**，**策略定价**
- 订单：虚拟代理商**订单处理**

![](/img/user/attachments/images/Pasted image 20220331133239.png)

## 代理商报价包装

代理商阶梯报价是：通过**虚拟代理**的方式对代理商投放的酒店进行**重新定价**。

名词解释

| Name              | Desc                                                                         |
| ----------------- | ---------------------------------------------------------------------------- |
| 小松鼠旅行        | 平台的虚拟代理商                                                             |
| 虚拟代理商        | Q内部维护的虚拟存在的代理商<br>由Q自己进行维护和运营，用于**代理外部代理商** |
| 真实代理商/供应商 |   目前接入平台的第三方代理商，适配的实际代理商                                                                           |

适配层（将外部的代理商都**适配成标准的报价模型**，利于后序追价的逻辑处理）

![](/img/user/attachments/images/image2021-10-30_16-40-56.png)

### 代理商

**一个**虚拟代理商马甲，允许去适配**多个**实际代理商：

![bts-bts.png|400](/img/user/attachments/images/bts-bts.png)

平台虚拟代理商存在的主要目的是：
- 通过平台虚拟代理商，代理真实的代理商
- 让真实代理商成为平台虚拟代理商的供货商

### 酒店

由于平台虚拟代理商可能代理多个真实代理商，然而：
1. 真实的多个代理商可能存在**相同酒店**的情况
2. Q 侧限制了一个 *hotelSeq* 只能映射一个代理商的酒店

需要对代理商的酒店数据做聚合映射：

![image2021-11-5_21-30-25.png|500](/img/user/attachments/images/image2021-11-5_21-30-25.png)

聚合方案选型：

![](/img/user/attachments/images/Pasted image 20220331133339.png)

缓存数据映射：
1. *hotelId*（平台虚拟代理商的酒店 ID）+ *btsSupplier*：真实代理商的 *partnerHotelId*

### 报价策略

小松鼠报价的策略流程：
1. 贵宾厅策略
2. 追价策略：追原始代理商报价的 0.1

策略分类：

![](/img/user/attachments/images/Pasted image 20220331133414.png)

#### 追加策略

![](/img/user/attachments/images/Pasted image 20220331133437.png)

效果：

![](/img/user/attachments/images/Pasted image 20220331133923.png)

#### 复制策略

![](/img/user/attachments/images/Pasted image 20220331133812.png)

### 订单

订单的 order 报价会携带客单转派商单的信息。

当用户下单后，由**交易**来完成商客单的转派。

```json
{
	"purchase": false,
	"userId": "111", //采购userID，为空的时候，从客单取客单的userId值
	"userName": "", //采购username，为空的时候，从客单取客单的userName值
	"roomId": "", //商单报价id，这里传MappingRoomID
	"supplierId": "", //商单真实供应商id
	"wrapperId": "", //商单真实供应商的马甲id
	"partnerHotelId": , //商单报价的供应商酒店id
	"partnerProductId": , //商单报价的供应商报价id
	"roomNightLossAmount": , //间夜允许亏损底价范围
	"contactPhone", //加密手机号为空的时候，从客单取客单的userId值
	"payAccount": , //采购单PPM账号，这里传入的是客单的WrapperID的PPM账号信息
	"forceUseRoomId": true, //报价实时计算查询
	"businessOrder": true, //报价商单逻辑查询
	"multiAmoutPromote": false //多间夜促销
}
```

为代理商业务赋能，突破供应链限制，给用户提供更多创新低价玩法。

代理商系统设置商单采购信息（商单的代理商标识、酒店 ID、房型 ID 等），由交易的商客单系统来完成商单的采购，最终生成订单。

![](/img/user/attachments/images/Pasted image 20220331133606.png)

https://wiki.corp.qunar.com/confluence/pages/viewpage.action?pageId=524631198

## HC直采报价的包装

策略系统获取到 HC 直采的原始报价后，通过策略【一系列规则】进行**包装和底价计算**生成新的报价。

流程如下：

![](/img/user/attachments/images/61f30eeb-0af4-47e3-866f-d9c7e77ffcab.png)