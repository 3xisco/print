# 环洋OIS API说明

## 修订历史

| 版本号 | 修订时间   | 修订人 | 修订内容                                                      |
| ------ | ---------- | ------ | ------------------------------------------------------------- |
| 1.0    | 2020-08-13 | 杜旭波 | 创建                                                          |
| 1.1    | 2020-12-17 | 杜旭波 | 增加测试对接说明                                              |
| 1.2    | 2020-07-05 | 杜旭波 | 增加税号列表处理栏位                                          |
| 1.3    | 2020-07-05 | 杜旭波 | 增加运单轨迹 对外转单 客户账单明细 报价对外查询接口           |
| 1.4    | 2021-09-16 | 杜旭波 | 增加get请求时，需要把？后的参数参与签名计算否则报非法请求     |
| 1.5    | 2021-10-16 | 杜旭波 | 增加单件信息相关节点                                          |
| 1.6    | 2021-10-25 | 杜旭波 | 签名计算参考demo文档url                                       |
| 1.7    | 2021-10-25 | 杜旭波 | 调用url 参考文档中的{{projectUrl }} 不要直接使用demo 代码中的 |
| 1.8    | 2021-12-23 | 杜旭波 | 增加单件信息查询接口以及更新tms接口栏位说明                   |
| 1.9    | 2021-12-31 | 杜旭波 | 统一说明重量单位KG  尺寸单位CM                                |
| 2.0    | 2022-05-30 | 杜旭波 | 优化对接说明，针对账号信息配置提供默认指导说明                |
| 2.1    | 2022-06-29 | 杜旭波 | 更新报价查询接口文档说明                                      |
| 2.2    | 2022-07-08 | 王阳   | 优化OIS文档描述                                               |
| 2.3    | 2022-11-08 | 涂轩垚 | 更新OIS API文档描述说明，优化排版                             |
| 2.4    | 2023-03-11 | 王阳   | 新增获取OIS,POS获取面单接口,轨迹接口                          |
| 2.5    | 2023-03-30 | 王阳   | 新增进口海关编码                                              |

## 1.对接说明

### 注意： 表单方式请求

### Content-Type : application/x-www-form-urlencoded

| 参数名称     | 是否必填 | 参数值                                                                                                   | 备注                                                                                                     |
| ------------ | -------- | -------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| projectUrl   | 必填     | 正式环境地址：https://xxxxxx/ <br> 测试环境地址：https://xxxxxx/ | 【通用】接口地址                                                                                         |

| appKey       | 必填     | WAIT_HYECN（测试账号）                                                                                   | 【通用】账号（正式秘钥请在TMS客户对接授权中获取）                                                        |
| appSecret    | 必填     | cda26d9311074549ba3ae8e832b66a02（测试秘钥）                                                             | 【通用】秘钥（正式秘钥请在TMS客户对接授权中获取）                                                        |
| nonce        | 必填     | slnkda                                                                                                   | 【通用】默认为：slnkda                                                                                   |
| version      | 必填     | 1.0                                                                                                      | 【通用】版本号默认：1.0                                                                                  |
|              |          |                                                                                                          |                                                                                                          |
| coid         | 必填     | appKey下划线分隔后面部分如：HYECN                                                                        | 【创建订单】物流公司编码                                                                                 |
| platformType | 必填     | ZYXT                                                                                                     | 【创建订单】平台类型ZYXT 表示客户对接                                                                    |
| hubInCode    | 必填     | POS-HUBIN（测试路线）正式路线由物流公司提供                                                              | 【创建订单】指定路线编码                                                                                 |
| goodsType    | 必填     | 由物流公司提供                                                                                           | 【创建订单】货物类型                                                                                     |
| ccPayment    |          |                                                                                                          | 【创建订单】付款方式（为空时，默认为"PP";  选项值： PP_寄付，CC_到付， TP_第三方付）                     |
|              |          |                                                                                                          |                                                                                                          |
| printType    | 必填     | LABEL                                                                                                    | 【获取订单Lable】打印类型 值可为LABEL，A4                                                                |
| queryType    | 必填     | 99                                                                                                       | 【运单轨迹追踪】查询类型<br/>（1：运单号码、3：参考编号、5：转单号码、99：支持运单号、转单号、参考编号） |

## 2.签名计算规则

## [代码示例地址](http://git.intelink.net:19550/posdoc/print)

#### 2.1. 获取token

> 2.1.1.调用服务接口 （{{projectUrl}}ois/order/getAuth），传入分配好的appKey和appSecret，验证通过后返回access token
>
> 2.1.2. 调用其他接口时需要此token 参与签名计算
>
> 2.1.3 token 有效时间不固定【推荐根据响应code判断 不要每次调用都获取token】： 当code= 1004或1005 就需要获取一次token

#### 2.2. 计算签名

> 2.2.1. 拼接所有参数（表单参数和http header原始值） ，将所有参数自然排序，然后做拼接（参数无值的无需拼接）。如： a=1&b=2&c=3&d=4
>
> 2.2.2. 将已经拼接好的参数字符串Base64. 如：Base64(str)
>
> 2.2.3. 将Base64后的字符串+appSecret做MD5。 如：MD5（Base64(str)+appSecret）
>
> 2.2.4. 将MD5后的字符串转换成大写

#### 2.3. token解析

> 2.3.1. http header 中存在三个参数 token，sign，version；token 并非真正的token，只是在原始token基础之上做了层伪装。
>
> 2.3.2.  token组成：
> {"timestamp":1584953245407,"nonce":"slnkda","token":"b535e97f-2293-4b8c-8bdd-b2e9fa96aa77"}
> timestamp 为当前时间戳;   nonce 可以固定slnkda;  token取2.1返回
>
> 2.3.3. 将组装token进行Base64. 如：Base64(token）
>
> 2.3.4. 将 "a"->"-","c"->"#","x"->"^","M"->"$"字符替换

#### 2.4. http header 组成

> http header 组成  （获取token以外的接口需要添加以下头信息）
>
> 2.4.1. token 伪装后的值
>
> 2.4.2. sign 签名
>
> 2.4.3. version 客户端版本

#### 2.5. 签名生成代码

> 特别注意使用get请求时，?后的参数也要参数签名计算，否则会导致非法请求

#### 2.6测试对接要求

> 2.6.1 测试单号要求 Test开头

## 3.验证授权

> 请求地址： {{projectUrl}}ois/order/getAuth
>
> 切记当前接口的返回值要解析token处理【demo 中的接口和此接口返回结果不一致】
>
> 一定要查看修订历史
>
> 请求方式：GET/POST

### 3.1请求参数

| 字段名    | 类型   | 长度 | 校验 | 说明 |
| --------- | ------ | ---- | ---- | ---- |
| appKey    | String |      | 必填 | 账号 |
| appSecret | String |      | 必填 | 密码 |

### 3.2响应参数

| 字段名 | 类型   | 说明         |
| ------ | ------ | ------------ |
| ack    | String | 验证结果     |
| coid   | String | 物流公司编码 |
| clno   | String | 客户编码     |
| token  | String | token        |

### 3.3接口示例

请求示例demo

> {{projectUrl}}ois/order/getAuth?appKey=WAIT_HYECN&appSecret=cda26d9311074549ba3ae8e832b66a02

成功响应demo   提供的代码demo 中的getToken 方法使用该接口返回的token 需要解析token

```json
{
  "body": {
    "clno": "WAIT",
    "ack": "true",
    "coid": "HYECN",
    "hash": "0141044852861",
    "token": "c2e5de27-8cd3-4384-a468-8fceac7e2db9"
  },
  "message": "请求成功",
  "result_code": 0,
  "solution": "请求成功",
  "traceId": "1682584401390lHyQvUMi"
}
```

失败响应demo

```json
{
  "body": {
    "clno": "",
    "ack": "false",
    "coid": "",
    "hash": "6436064852861",
    "token": ""
  },
  "message": "账号或秘钥错误",
  "result_code": 401091,
  "solution": "账号或秘钥错误",
  "traceId": "1682584606336QGktHjMU"
}
```

## 4.获取渠道列表

> 请求地址：{{projectUrl}}ois/order/getProductList
>
> 请求方式：POST
>
> Content-Type=application/x-www-form-urlencoded


### 4.1请求参数

| 字段名 | 类型   | 长度 | 校验 | 说明         |
| ------ | ------ | ---- | ---- | ------------ |
| coid   | String |      |      | 物流公司编号 |

### 4.2响应参数

| 字段名  | 类型   | 说明     |
| ------- | ------ | -------- |
| hubInId | String | 路线ID   |
| name    | String | 路线名称 |
| code    | String | 路线编码 |
| hubType | String | 路线分类 |

### 4.3请求示例

请求demo

> {{projectUrl}}ois/order/getProductList
>
> coid:KERRYCN

成功响应demo

```json
{
  "body":[
    {
      "code": "POSIN-01",
      "hubInId": 552,
      "hubType": "OIS-API",
      "name": "POSIN-01"
    },
    {
      "code":"ID-M",
      "hubInId":21,
      "hubType":"ID",
      "name":"印度尼西亚敏感货"
    },
    {
      "code": "AY-1",
      "hubInId": 382,
      "hubType": "OIS-API",
      "name": "ss-1"
    }
  ],
  "message":"请求成功",
  "result_code":0,
  "solution":"请求成功",
  "traceId":"1667874670280slnkda"
}
```

失败响应demo

```json
{
	"body": [],
	"message": "请求成功",
	"result_code": 0,
	"solution": "请求成功",
	"traceId": "1667874988291slnkda"
}
```

## 5.获取label类型列表

> 请求地址：{{projectUrl}}ois/order/getLabelType
>
> 请求方式：POST
>
> Content-Type=application/x-www-form-urlencoded

### 5.1请求参数

| 字段名 | 类型   | 长度 | 校验 | 说明         |
| ------ | ------ | ---- | ---- | ------------ |
| coid   | String |      |      | 物流公司编号 |

### 5.2响应参数

| 字段名 | 类型   | 说明                   |
| ------ | ------ | ---------------------- |
| id     | String |                        |
| name   | String | Lable                  |
| tplUrl | String | 文件下载地址（预览）   |
| type   | String | Lable类型（A4，Lable） |

### 5.3接口示例

请求demo

> {{projectUrl}}ois/order/getLabelType
>
> coid:KERRYCN

成功响应demo

```json
{
  "body": [
    {
      "id": "",
      "name": "一键打印LABEL",
      "tplUrl": "",
      "type": "LABEL"
    },
    {
      "id": "",
      "name": "一键打印A4",
      "tplUrl": "",
      "type": "A4"
    }
  ],
  "message": "请求成功",
  "result_code": 0
}
```

失败响应demo

```json
{
  "body": [],
  "message": "请求成功",
  "result_code": 0
}
```

## 6.创建订单
> 请求地址：{{projectUrl}}ois/order/createOrder
>
> 请求方式：POST
>
> Content-Type=application/x-www-form-urlencoded

### 6.1请求参数-快递小包

| 字段名                      | 校验 | 类型       | 长度  | 说明                                                                                                                                |
| --------------------------- | ---- | ---------- | ----- | ----------------------------------------------------------------------------------------------------------------------------------- |
| platformType                | 必填 | String     | 10    | 平台类型  （默认填写：ZYXT    表示客户对接）                                                                                        |
| referenceno                 | 必填 | String     | 40    | 订单号码                                                                                                                            |
| refno                       |      | String     | 40    | 转单号码                                                                                                                            |
| hubInCode                   | 必填 | String     | 20    | 指定路线                                                                                                                            |
| pcs                         |      | Integer    | 11    | 件数（为空时，默认为1;不为空时，必须>0并且<=999）                                                                                   |
| weig                        | 必填 | BigDecimal | 18，3 | 重量（必须>0并且<=999999）                                                                                                          |
| goodsType                   |      | String     | 100   | 货物类型（为空时，默认为"普货"）带电类型传值，请联系物流公司获取                                                                    |
| contents                    |      | String     |       | 英文品名（仅用于四大快递和其他存在主体英文品名接口，传电池金句、英文品名信息），<br/>若为空，则使用invoice.eDecsName 拼接传入此字段 |
| packType                    |      | String     | 50    | 包装类型（WPX：包裹；DOC：文件；PAK：袋。为空时，默认为"WPX"）                                                                      |
| codCharge                   |      | BigDecimal | 18，2 | 代收款                                                                                                                              |
| codChargeCur                |      | String     | 10    | 代收币别（代收款>0时，必填；Ex：USD，CNY，EUR，AUD...）                                                                             |
| remark                      |      | String     | 200   | 备注     （长度>200时，自动截取前200个字符）                                                                                        |
| reName                      | 必填 | String     | 100   | 收件人                                                                                                                              |
| reCompany                   |      | String     | 90    | 收件公司（默认公司为空时使用收件人字段的数据填充）                                                                                  |
| reTel                       | 必填 | String     | 40    | 收件电话                                                                                                                            |
| retel2                      |      | String     | 40    | 收件手机                                                                                                                            |
| reAddr                      | 必填 | String     | 500   | 收件地址                                                                                                                            |
| reAddr2                     |      | String     | 60    | 收件地址2                                                                                                                           |
| reAddr3                     |      | String     | 60    | 收件地址3                                                                                                                           |
| reCountryCode               | 必填 | String     | 20    | 收件国家二字码                                                                                                                      |
| reCountry                   |      | String     | 60    | 收件国家                                                                                                                            |
| reState                     |      | String     | 80    | 收件州/省                                                                                                                           |
| reCity                      |      | String     | 80    | 收件城市                                                                                                                            |
| reDistrict                  |      | String     | 50    | 收件区县                                                                                                                            |
| reHouseNo                   |      | String     | 50    | 收件门牌号                                                                                                                          |
| reZip                       |      | String     | 10    | 收件邮编                                                                                                                            |
| reEmail                     |      | String     | 100   | 收件邮箱                                                                                                                            |
| decValueCur                 | 必填 | String     | 10    | 申报币别  （Ex：USD，CNY，EUR，AUD...）                                                                                             |
| sdName                      |      | String     | 100   | 寄件人         （长度>100时，自动截取前100个字符）                                                                                  |
| sdCompany                   |      | String     | 90    | 寄件公司       （长度>90时，自动截取前90个字符）                                                                                    |
| sdTel                       |      | String     | 40    | 寄件电话       （长度>40时，自动截取前40个字符）                                                                                    |
| sdAddr                      |      | String     | 120   | 寄件地址       （长度>120时，自动截取前120个字符）                                                                                  |
| sdAddr2                     |      | String     | 60    | 寄件地址       （长度>60时，自动截取前60个字符）                                                                                    |
| sdAddr3                     |      | String     | 60    | 寄件地址       （长度>60时，自动截取前60个字符）                                                                                    |
| sdCountry                   |      | String     | 60    | 寄件国家       （长度>60时，自动截取前60个字符）                                                                                    |
| sdState                     |      | String     | 80    | 寄件州/省      （长度>80时，自动截取前80个字符）                                                                                    |
| sdCity                      |      | String     | 80    | 寄件城市       （长度>80时，自动截取前80个字符）                                                                                    |
| sdZip                       |      | String     | 10    | 寄件邮编       （长度>10时，自动截取前10个字符）                                                                                    |
| sdEmail                     |      | String     | 100   | 寄件邮箱       （长度>100时，自动截取前100个字符）                                                                                  |
| buyerId                     |      | String     | 100   | 买家ID         （长度>100时，自动截取前100个字符）                                                                                  |
| sellerNick                  |      | String     | 50    | 卖家昵称       （长度>50时，自动截取前50个字符）                                                                                    |
| isReturnsign                |      | String     | 10    | 是否退回（退回标志，N表示不退回，Y标表示退回，为空时默认不退回）                                                                    |
| labelUrl                    |      | String     | 300   | LABEL地址                                                                                                                           |
| insurance                   |      | BigDecimal | 18，2 | 保险金额                                                                                                                            |
| insuranceCur                |      | String     | 10    | 保险币别 （保险金额>0时，必填；Ex：USD，CNY，EUR，AUD...）                                                                          |
| ccPayment                   |      | String     | 10    | 付款方式 （为空时，默认为"PP";  选项值： PP_寄付，CC_到付， TP_第三方付）                                                           |
| cctaxPayment                |      | String     | 10    | 税金支付方式 （选项值： PP_寄付，CC_到付， TP_第三方付）                                                                            |
| ccCharge                    |      | BigDecimal | 18，2 | 到付款                                                                                                                              |
| ccChargeCur                 |      | String     | 10    | 到付币别 （Ex：USD，CNY，EUR，AUD...）                                                                                              |
| extra1                      |      | String     | 100   | 扩展字段1     （长度>100时，自动截取前100个字符）                                                                                   |
| extra2                      |      | String     | 100   | 扩展字段2     （长度>100时，自动截取前100个字符）                                                                                   |
| extra3                      |      | String     | 100   | 扩展字段3     （长度>100时，自动截取前100个字符）                                                                                   |
| extra4                      |      | String     | 200   | 扩展字段4     （长度>200时，自动截取前200个字符）                                                                                   |
| extra5                      |      | String     | 200   | 扩展字段5     （长度>200时，自动截取前200个字符）                                                                                   |
| oisInvoices.descrName       |      | String     | 200   | 中文品名 （长度>200时，自动截取前200个字符）                                                                                        |
| oisInvoices.eDescrNamee     |      | String     | 200   | 英文品名 （有invoice数据时必填，长度>200时，自动截取前200个字符）                                                                   |
| oisInvoices.qty             |      | Integer    | 10    | 申报数量 （有invoice数据时必填，需>0并且<=99999999）                                                                                |
| oisInvoices.price           |      | BigDecimal | 18，4 | 申报单价 （有invoice数据时必填，需>0并且<=99999999）                                                                                |
| oisInvoices.unit            |      | String     | 100   | 单位     （Ex：PCS，件，套，捆...）                                                                                                 |
| oisInvoices.hsCode          |      | String     | 100   | 海关编码                                                                                                                            |
| oisInvoices.importHsCode    |      | String     | 100   | 进口海关编码                                                                                                                        |
| oisInvoices.origin          |      | String     | 100   | 原产地   （Ex：CN，TW，US...）                                                                                                      |
| oisInvoices.uses            |      | String     | 100   | 用途                                                                                                                                |
| oisInvoices.material        |      | String     | 100   | 材质                                                                                                                                |
| oisInvoices.brand           |      | String     | 100   | 品牌                                                                                                                                |
| oisInvoices.sku             |      | String     | 100   | SKU                                                                                                                                 |
| oisInvoices.transactionUrl  |      | String     | 500   | 商品链接地址 （长度>500时，自动截取前500个字符）                                                                                    |
| oisInvoices.remark          |      | String     | 500   | 配货信息    （长度>500时，自动截取前500个字符）                                                                                     |
| oisInvoices.nWeig           |      | BigDecimal | 18，3 | 毛重        （一条invoice信息的重量，需>=0并且<=999999）                                                                            |
| oisInvoices.unitWeig        |      | BigDecimal | 24，9 | 单个重量    （需>=0并且<=999999）                                                                                                   |
| oisInvoices.include         |      | String     | 100   | 货物包含                                                                                                                            |
| oisInvoices.pcs             |      | Integer    | 10    | 包装件数       （需>=0并且<=9999）                                                                                                  |
| pieces.actual               |      | BigDecimal | 18，3 | 单件信息.实重[KG]  （有单件数据时必填，需>0并且<=999）                                                                              |
| pieces.length               |      | BigDecimal | 18，3 | 单件信息.长[CM]    （有单件数据时必填，需>0并且<=999）                                                                              |
| pieces.width                |      | BigDecimal | 18，3 | 单件信息.宽[CM]    （有单件数据时必填，需>0并且<=999）                                                                              |
| pieces.height               |      | BigDecimal | 18，3 | 单件信息.高[CM]    （有单件数据时必填，需>0并且<=999）                                                                              |
| pieces.remark               |      | String     | 200   | 单件信息.备注                                                                                                                       |
| taxNoList.no                |      | String     | 30    | 税号                        （有税号数据时必填）                                                                                    |
| taxNoList.owner             |      | String     | 5     | 税号所属；寄件：sd，收件：re    （有税号数据时必填）                                                                                |
| taxNoList.type              |      | String     | 30    | 税号类型EOR/VAT/SDT/IOSS ...（有税号数据时必填）                                                                                    |
| taxNoList.issuerCountryCode |      | String     | 30    | 发行国家简码                                                                                                                        |

### 6.2请求参数-专线订单

| 字段名                      | 校验 | 类型       | 长度  | 说明                                                                                                        |
| --------------------------- | ---- | ---------- | ----- | ----------------------------------------------------------------------------------------------------------- |
| platformType                | 必填 | String     | 10    | 平台类型  （默认填写：ZYXT    表示客户对接）                                                                |
| businessBigType             | 必填 | String     | 10    | 系统流程（1：快递小包；3专线订单；为空时,默认为快递小包业务）                                               |
| referenceno                 | 必填 | String     | 40    | 订单号码                                                                                                    |
| refno                       |      | String     | 40    | 转单号码                                                                                                    |
| hubInCode                   | 必填 | String     | 20    | 指定路线                                                                                                    |
| cocustomType                | 必填 | String     | 10    | 报关方式   1:自备、2:买单                                                                                   |
| remark                      |      | String     | 200   | 备注     （长度>200时，自动截取前200个字符）                                                                |
| reWarehouseCode             |      | String     | 20    | 仓库编码                                                                                                    |
| reName                      | 必填 | String     | 100   | 收件人                                                                                                      |
| reCompany                   |      | String     | 90    | 收件公司（默认公司为空时使用收件人字段的数据填充）                                                          |
| reTel                       | 必填 | String     | 40    | 收件电话                                                                                                    |
| retel2                      |      | String     | 40    | 收件手机                                                                                                    |
| reAddr                      | 必填 | String     | 500   | 收件地址                                                                                                    |
| reAddr2                     |      | String     | 60    | 收件地址2                                                                                                   |
| reAddr3                     |      | String     | 60    | 收件地址3                                                                                                   |
| reCountryCode               | 必填 | String     | 20    | 收件国家二字码                                                                                              |
| reCountry                   |      | String     | 60    | 收件国家                                                                                                    |
| reState                     |      | String     | 80    | 收件州/省                                                                                                   |
| reCity                      |      | String     | 80    | 收件城市                                                                                                    |
| reDistrict                  |      | String     | 50    | 收件区县                                                                                                    |
| reHouseNo                   |      | String     | 50    | 收件门牌号                                                                                                  |
| reZip                       |      | String     | 10    | 收件邮编                                                                                                    |
| reEmail                     |      | String     | 100   | 收件邮箱                                                                                                    |
| decValueCur                 | 必填 | String     | 10    | 申报币别  （Ex：USD，CNY，EUR，AUD...）                                                                     |
| sdName                      |      | String     | 100   | 寄件人         （长度>100时，自动截取前100个字符）                                                          |
| sdCompany                   |      | String     | 90    | 寄件公司       （长度>90时，自动截取前90个字符）                                                            |
| sdTel                       |      | String     | 40    | 寄件电话       （长度>40时，自动截取前40个字符）                                                            |
| sdAddr                      |      | String     | 120   | 寄件地址       （长度>120时，自动截取前120个字符）                                                          |
| sdAddr2                     |      | String     | 60    | 寄件地址       （长度>60时，自动截取前60个字符）                                                            |
| sdAddr3                     |      | String     | 60    | 寄件地址       （长度>60时，自动截取前60个字符）                                                            |
| sdCountry                   |      | String     | 60    | 寄件国家       （长度>60时，自动截取前60个字符）                                                            |
| sdState                     |      | String     | 80    | 寄件州/省      （长度>80时，自动截取前80个字符）                                                            |
| sdCity                      |      | String     | 80    | 寄件城市       （长度>80时，自动截取前80个字符）                                                            |
| sdZip                       |      | String     | 10    | 寄件邮编       （长度>10时，自动截取前10个字符）                                                            |
| sdEmail                     |      | String     | 100   | 寄件邮箱       （长度>100时，自动截取前100个字符）                                                          |
| insurance                   |      | BigDecimal | 18，2 | 保险金额                                                                                                    |
| insuranceCur                |      | String     | 10    | 保险币别 （保险金额>0时，必填；Ex：USD，CNY，EUR，AUD...）                                                  |
| ccPayment                   |      | String     | 10    | 付款方式 （为空时，默认为"PP";  选项值： PP_寄付，CC_到付， TP_第三方付）                                   |
| poNo                        |      | String     | 40    | PO NO         （长度>40时，自动截取前40个字符）                                                             |
| isHsCode                    |      | Integer    | 10    | 是否指定海关编码       （默认为0(有效值为0,1)）                                                             |
| extra1                      |      | String     | 100   | 扩展字段1     （长度>100时，自动截取前100个字符）                                                           |
| extra2                      |      | String     | 100   | 扩展字段2     （长度>100时，自动截取前100个字符）                                                           |
| extra3                      |      | String     | 100   | 扩展字段3     （长度>100时，自动截取前100个字符）                                                           |
| extra4                      |      | String     | 200   | 扩展字段4     （长度>200时，自动截取前200个字符）                                                           |
| extra5                      |      | String     | 200   | 扩展字段5     （长度>200时，自动截取前200个字符）                                                           |
| taxNoList.no                |      | String     | 30    | 税号                        （有税号数据时必填）                                                            |
| taxNoList.owner             |      | String     | 5     | 税号所属；寄件：sd，收件：re  （有税号数据时必填）                                                          |
| taxNoList.type              |      | String     | 30    | 税号类型EOR/VAT/SDT/IOSS ...（有税号数据时必填）                                                            |
| taxNoList.issuerCountryCode |      | String     | 30    | 发行国家简码                                                                                                |
| taxNoList.receiptCompany    |      | String     | 200   | VAT公司名称                                                                                                 |
| taxNoList.receiptAddress    |      | String     | 200   | VAT公司地址                                                                                                 |
| taxNoList.shipAddress       |      | String     | 200   | 发货地址                                                                                                    |
| taxNoList.issuerCountryCode |      | String     | 200   | 发货公司                                                                                                    |
| oisInvoices. descrName      |      | String     | 200   | 中文品名 （长度>200时，自动截取前200个字符）                                                                |
| oisInvoices. eDescrNamee    | 必填 | String     | 200   | 英文品名 （有invoice数据时必填，长度>200时，自动截取前200个字符）                                           |
| oisInvoices.qty             | 必填 | Integer    | 10    | 申报数量 （有invoice数据时必填，需>0并且<=99999999）                                                        |
| oisInvoices.price           | 必填 | BigDecimal | 18，4 | 申报单价 （有invoice数据时必填，需>0并且<=99999999）                                                        |
| oisInvoices.unit            |      | String     | 100   | 单位     （Ex：PCS，件，套，捆...）                                                                         |
| oisInvoices.purchasePrice   |      | BigDecimal | 10,3  | 采购单价     （Ex：PCS，件，套，捆...）                                                                     |
| oisInvoices.hsCode          |      | String     | 100   | 海关编码                                                                                                    |
| oisInvoices.uses            |      | String     | 100   | 用途                                                                                                        |
| oisInvoices.specs           |      | String     | 100   | 规格                                                                                                        |
| oisInvoices.material        |      | String     | 100   | 材质                                                                                                        |
| oisInvoices.materialEn      |      | String     | 100   | 中文材质                                                                                                    |
| oisInvoices.goodsType       |      | String     | 100   | 货物类型（为空时，默认为"PH"）带电类型传值，请联系物流公司获取                                              |
| oisInvoices.brand           |      | String     | 100   | 品牌                                                                                                        |
| oisInvoices.sku             |      | String     | 100   | SKU                                                                                                         |
| oisInvoices.transactionUrl  |      | String     | 500   | 商品链接地址 （长度>500时，自动截取前500个字符）                                                            |
| oisinvoices.picbase64       |      | base64     | max   | 商品图片 （需将图片转Base64传入）                                                                           |
| oisInvoices.pcs             |      | Integer    | 10    | 包装件数       （需>=0并且<=9999）                                                                          |
| oisinvoices.childJobno      | 必填 | String     | max   | 箱号 （多个箱号之间用逗号（半角）隔开，单个箱号长度不能大于40；<br/>单个箱号长度小于等于4位时，必须为数字） |
| oisinvoices.pcsActual       | 必填 | BigDecimal | 18，3 | 单件信息.实重[KG]   （需>0并且<=999）                                                                       |
| oisinvoices.pcsgweig        |      | BigDecimal | 18，3 | 单件信息.净重[KG]  （需>0并且<=999）                                                                        |
| oisinvoices.pcslength       | 必填 | BigDecimal | 18，3 | 单件信息.长[CM]    （需>0并且<=999）                                                                        |
| oisinvoices.pcswidth        | 必填 | BigDecimal | 18，3 | 单件信息.宽[CM]    （需>0并且<=999）                                                                        |
| oisinvoices.pcsheight       | 必填 | BigDecimal | 18，3 | 单件信息.高[CM]    （需>0并且<=999）                                                                        |


### 6.3响应参数

| 字段名      | 类型     | 说明                                                                                         |
| ----------- | -------- | -------------------------------------------------------------------------------------------- |
| referenceno | String   | 订单号码                                                                                     |
| refno       | String   | 转单号码                                                                                     |
| labelUrl    | String   | Lable PDF打印地址 创建订单接口返回的label，具有场景局限，要求使用【获取订单Lable】接口获取   |
| invoiceUrl  | String   | Invoice PDF打印地址 创建订单接口返回的label，具有场景局限，要求使用【获取订单Lable】接口获取 |
| copyLabel   | String[] | 备份联URL                                                                                    |
| orderId     | String   | 订单id                                                                                       |
| oddNo       | String   | 运单号码                                                                                     |

### 6.4请求示例

> （注意：oisInvoices，pieces，taxNoList为数组，taxNoList和reCode 两者只能选择一个，推荐使用taxNoList，reCode 只做兼容）

请求体json demo：
```json
body1={
  "platformType":"ZYXT",
  "referenceno":"test201807081805",
  "refno":"",
  "hubInCode":"AY-1",
  "pcs":1,
  "weig":32,
  "remark":"11111",
  "reName":"AYAO",
  "reTel":"1305588581",
  "reTel2":"",
  "reAddr":"广东省深圳市宝安区西乡街道宝安智谷",
  "reAddr2":"11F,16, Toegye-ro 73-gil",
  "reCountryCode":"CN",
  "reState":"广东",
  "reCity":"深圳",
  "reZip":"518000",
  "reCode":"",
  "decValueCur":"USD",
  "oisInvoices":[
    {
      "descrName":"T恤",
      "eDescrNamee":"T-Shirts",
      "qty":1,
      "price":100,
      "hsCode":"123123",
      "sku":"001NewbornCode---01",
      "remark":"我是买家备注，请查看这：请包装安全再发货，谢谢"
    }
  ],
  "pieces":[
    {
      "actual":12,
      "length":99,
      "width":99,
      "height":4,
      "remark":"备注"
    }
  ],
  "sdName":"WAIT",
  "sdTel":"82353586865867",
  "sdAddr":"XXX街道",
  "sdCountry":"CN",
  "sdState":"广东",
  "sdCity":"深圳",
  "sdZip":"46465",
  "isReturnsign":"N",
  "ccPayment":"PP",
  "cctaxPayment":"PP",
  "codCharge":0,
  "codChargeCur":"USD",
  "taxNoList":[
    {
      "no":"001",
      "owner":"re",
      "source":"print",
      "type":"VAT"
    },
    {
      "no":"001",
      "owner":"sd",
      "source":"print",
      "type":"EORI"
    }
  ]
}
```
成功响应demo
```json
{
  "body": {
    "ack": "true",
    "exceptionSource": "pos",
    "isfaraway": 0,
    "labelUrl": "http://183.36.5.219:22000/group1/M00/5E/AA/xxxxxxxxxxxxxx.pdf",
    "oddNo": "YD202211010001",
    "orderId": "2817805",
    "otherNo": "",
    "pieceMarks": [],
    "printSuccessCallbackFuns": [],
    "proxyOrderId": "",
    "referenceno": "XL9105620",
    "refno": "XL9105620",
    "resultReturnMode": 1
  },
  "message": "请求成功",
  "result_code": 0,
  "solution": "请求成功",
  "traceId": "1667877228953slnkda"
}
```

失败响应demo
```json
{
  "body": "",
  "message": "平台类型<>必填",
  "result_code": 1,
  "traceId": "1667877355966slnkda"
}
```
## 7.获取订单Lable

> 请求地址：{{projectUrl}}ois/order/getLabel
>
> 请求方式：POST/GET
>
> Content-Type=application/x-www-form-urlencoded

### 7.1请求参数

| 字段名    | 类型   | 长度 | 校验 | 说明                                                                                  |
| --------- | ------ | ---- | ---- | ------------------------------------------------------------------------------------- |
| coid      | String |      |      | 物流公司编号                                                                          |
| printType | String |      |      | 打印类型，值可为LABEL、A4                                                             |
| orderId   | String |      |      | 订单id（creatOrder接口下单成功后返回的orderId）                                       |
| Format    | String |      |      | pdf模板地址，用于生成Label的PDF模板URL地址，当Format参数有值时，PrintType参数无需给值 |

### 7.2响应参数

| 字段名   | 类型   | 说明                           |
| -------- | ------ | ------------------------------ |
| ack      | String | 是否正常生成PDF文件            |
| labelUrl | String | Label和Invoice的PDF文件URL地址 |

### 7.3接口示例
请求demo
> printType:LABEL
>
> orderId:2817804
>
> coid:2817804


成功响应demo

```json
{
  "body": {
    "ack": "true",
    "exceptionSource": "pos",
    "isfaraway": 0,
    "labelUrl": "http://183.36.5.219:22000/group1/M00/5E/AD/xxxxxxxxxxxxxxxxxx.pdf",
    "otherNo": "",
    "pieceMarks": [],
    "printSuccessCallbackFuns": [],
    "proxyOrderId": "",
    "resultReturnMode": 1
  },
  "message": "请求成功",
  "result_code": 0,
  "solution": "请求成功",
  "traceId": "1667877579319slnkda"
}
```

失败响应demo

```json
{
  "body": "",
  "message": "order_id无效！",
  "result_code": 1,
  "traceId": "1667877642762slnkda"
}
```

## 8.运单轨迹追踪

> 请求地址：{{projectUrl}}/tms/expose/queryTraceoutList
>
> 请求方式：POST
>
> Content-Type=application/x-www-form-urlencoded

### 8.1请求参数

| 字段名    | 类型   | 长度 | 校验 | 说明                                                                                |
| --------- | ------ | ---- | ---- | ----------------------------------------------------------------------------------- |
| noList    | String |      | 必填 | 单号集合（多个单号使用回车检分割）多个单号请将回车符转成URL编码，否则无法识别回车符 |
| queryType | String |      | 必填 | 查询类型（1：运单号码、3：参考编号、5：转单号码、99：支持运单号、转单号、参考编号） |

### 8.2响应参数

| 字段名       | 类型   | 说明         |
| ------------ | ------ | ------------ |
| scanDatetime | Date   | 扫描时间     |
| scanStation  | String | 位置         |
| remark       | String | 轨迹描述     |
| scanCode     | String | 扫描节点编码 |
| scanName     | String | 扫描节点名称 |

### 8.3接口示例

请求demo

```json
body1={
  "queryType":"99",
  "noList":"TEST22102500028"
}
```

成功响应demo

```json
{
  "traceId":"3bff38784c4a4826b547c3ee5f53a8e1.271.16669408146233759",
  "result_code":0,
  "body":[
    {
      "refno":"test201314001",
      "referenceno":"test201314002",
      "jobno":"test201314003",
      "podInfoDTOList":[
        {
          "scanDatetime":1666344305000,
          "scanStation":"CN",
          "remark":"Shipment Received",
          "scanCode":"PU",
          "scanName":""
        }
      ]
    }
  ],
  "message":"请求成功"
}
```

## 9.对外转单接口
> 请求地址：{{projectUrl}}tms/expose/queryRefnoList
>
> 请求方式：POST
>
> Content-Type=application/x-www-form-urlencoded

### 9.1请求参数

| 字段名   | 类型   | 长度 | 校验 | 说明                                                                                 |
| -------- | ------ | ---- | ---- | ------------------------------------------------------------------------------------ |
| jobnoStr | String |      | 必填 | 单号集合 （多个单号使用回车检分割）多个单号请将回车符转成URL编码，否则无法识别回车符 |
| codeType | String |      | 必填 | 查询类型（1：运单号码、3：参考编号）                                                 |

### 9.2响应参数

| 字段名      | 类型   | 说明     |
| ----------- | ------ | -------- |
| jobno       | String | 运单编号 |
| referenceno | String | 参考编号 |
| refno       | String | 转单号码 |

### 9.3接口示例

请求demo

```json
body1={
  "codeType":"1",
  "jobnoStr":"401230701L"
}
```

成功响应demo

```json
{
  "result_code": 0,
  "body": [
    {
      "refno": "8995651313",
      "referenceno": "5385338954",
      "jobno": "401230701L"
    }
  ],
  "message": "请求成功"
}
```

## 10.客户账单明细
> 请求地址：{{projectUrl}}tms/expose/queryInvoiceDetailList
>
> 请求方式：POST
>
> Content-Type=application/x-www-form-urlencoded


### 10.1请求参数

| 字段名    | 类型 | 长度 | 校验 | 说明     |
| --------- | ---- | ---- | ---- | -------- |
| startDate | Date |      | 必填 | 起始时间 |
| endDate   | Date |      | 必填 | 终止时间 |

### 10.2响应参数

| 字段名      | 类型       | 说明     |
| ----------- | ---------- | -------- |
| jobno       | String     | 运单编号 |
| referenceno | String     | 参考编号 |
| ardedName   | String     | 科目名称 |
| formula     | String     | 计算公式 |
| charge      | BigDecimal | 金额     |
| chargeCur   | String     | 币别     |
| sdDate      | Date       | 寄件日期 |
| remark      | String     | 账单备注 |

### 10.3接口示例

请求demo

```json
body1={
  "startDate":"1626710400000",
  "endDate":"1626948358735"
}
```

成功响应demo

```json
{
  "result_code": 0,
  "body": [
    {
      "sdDate": 1626751223000,
      "charge": 822.96,
      "referenceno": "401243154L",
      "jobno": "401243154L",
      "formula": "(698.9)*1.1775",
      "remark": "",
      "chargeCur": "CNY",
      "ardedName": "应收运费"
    },
    {
      "sdDate": 1626751223000,
      "charge": 161.50,
      "referenceno": "401243154L",
      "jobno": "401243154L",
      "formula": "19*8.5",
      "remark": "",
      "chargeCur": "CNY",
      "ardedName": "应收HKDHL旺季附加费"
    },
    {
      "sdDate": 1626929222000,
      "charge": 26.00,
      "referenceno": "D4215064",
      "jobno": "401246163L",
      "formula": "",
      "remark": "顺丰到付运费26元",
      "chargeCur": "CNY",
      "ardedName": "应收到付手续费"
    }
  ],
  "message": "请求成功"
}
```

## 11.报价对外查询
> 请求地址：{{projectUrl}}tms/expose/queryQuoteList
>
> 请求方式：POST
>
> Content-Type=application/x-www-form-urlencoded

### 11.1请求参数

| 字段名          | 类型       | 长度 | 校验 | 说明                     |
| --------------- | ---------- | ---- | ---- | ------------------------ |
| hubinCodes      | String[]   |      |      | 指定路线编码（允许多选） |
| destNo          | String     |      | 必填 | 目的地国家简码           |
| reWarehouseCode | String     |      |      | 仓库代码                 |
| reZip           | String     |      |      | 收件邮编                 |
| reState         | String     |      |      | 收件州省                 |
| reCity          | String     |      |      | 收件城市                 |
| reCounty        | String     |      |      | 收件区县                 |
| weig            | BigDecimal |      | 必填 | 重量（KG）               |
| volumn          | String     |      |      | 体积（cm³）              |
| packing         | String     |      | 必填 | 包装类型（WPX_）         |
| goodsTypes      | String[]   |      |      | 货物类型（允许多选）     |
| cocustomType    | String     |      |      | 报关方式                 |
| cctaxPayment    | String     |      |      | 税金付款方式             |

### 11.2响应参数

| 字段名                                           | 字段类型   | 备注                         |
| ------------------------------------------------ | ---------- | ---------------------------- |
| code                                             | String     | 指定路线编码                 |
| name                                             | String     | 指定路线名称                 |
| weig                                             | BigDecimal | 重量                         |
| divsize                                          | BigDecimal | 材积除以数                   |
| volume                                           | BigDecimal | 体积                         |
| chargingUnit                                     | String     | 计费单位（KG_重量/CBM_体积） |
| standardFeeSum                                   | BigDecimal | 总费用（本币）               |
| otherFeeUnitSum                                  | BigDecimal | 单价（本币）                 |
| standardCharge                                   | BigDecimal | 运费（本币）                 |
| otherStandardFeeSum                              | BigDecimal | 附加费（本币）               |
| standardCur                                      | String     | 本币                         |
| quoteFee                                         | BigDecimal | 运费（原币）                 |
| currency                                         | String     | 原币                         |
| formulaView                                      | String     | 运费计算公式                 |
| disableGoodsType                                 | String     | 禁运货物类型                 |
| zoneNo                                           | String     | 分区编码                     |
| zoneName                                         | String     | 分区名称                     |
| aging                                            | String     | 分区时效                     |
| remark                                           | String     | 价格描述                     |
| otherStandardFeeList                             | Array      | 附加费列表                   |
| otherStandardFeeList.ardedCode                   | String     | 科目编码                     |
| otherStandardFeeList.ardedName                   | String     | 科目名称                     |
| otherStandardFeeList.chargestandard              | BigDecimal | 金额（本币）                 |
| otherStandardFeeList.charge                      | BigDecimal | 金额（原币）                 |
| otherStandardFeeList.chargeCur                   | String     | 原币                         |
| otherStandardFeeList.formulaotherStandardFeeList | String     | 计算公式                     |

### 11.3接口示例

请求demo

```json
body1={
  "weig":10,
  "custNo":"",
  "destNo":"US",
  "packing":"WPX",
  "reZip":"10470",
  "statNo":"",
  "hubinCodes":["AY-1"],
  "reWarehouseCode":"",
  "reState":"",
  "reCity":"",
  "reCounty":"",
  "volumn":"",
  "goodsTypes":[""],
  "cocustomType":"",
  "cctaxPayment":""
}
```

成功响应demo

```json
{
  "body":[
    {
      "aging":"666",
      "code":"02-SSLY",
      "currency":"CNY",
      "disableGoodsType":"dd-test,CD",
      "divsize":5000.0,
      "formulaView":"44*22",
      "name":"SSLY-TEST-2号",
      "otherFeeUnitSum":50.82,
      "otherStandardFeeList":[
        {
          "ardedCode":"04Q",
          "ardedName":"应收其它费用",
          "charge":150.00,
          "chargeCur":"CNY",
          "chargestandard":150.00,
          "formula":"100+50"
        }
      ],
      "otherStandardFeeSum":150.00,
      "quoteFee":968.000,
      "remark":"test remark",
      "standardCharge":968.000,
      "standardCur":"CNY",
      "standardFeeSum":1118.00,
      "weig":22,
      "zoneNo":"04"
    }
  ],
  "message":"请求成功",
  "result_code":0,
  "solution":"请求成功",
  "traceId":"1665468517296slnkda"
}
```

## 12.单件信息查询
> 请求地址：{{projectUrl}}tms/expose/queryPcsData
>
> 请求方式：POST
>
> Content-Type=application/x-www-form-urlencoded

### 12.1请求参数

| 字段名   | 类型   | 长度 | 必填 | 单号                                 |
| -------- | ------ | ---- | ---- | ------------------------------------ |
| jobno    | String |      | 必填 | 单号                                 |
| codeType | String |      | 必填 | 查询类型（1：运单号码、3：参考编号） |

### 12.2响应参数

| 字段名 | 类型   | 说明       |
| ------ | ------ | ---------- |
| length | Double | 长（cm）   |
| width  | Double | 宽（cm）   |
| height | Double | 高（cm）   |
| actual | Double | 实重（kg） |

### 12.3接口示例

请求demo

```json
body1=
{
  "jobno":"test123",
  "codeType":"1"
}
```

成功响应demo

```json
{
  "body": [
    {
      "actual": 10,
      "height": 10,
      "length": 10,
      "width": 10
    }
  ],
  "message": "请求成功",
  "result_code": 0,
  "traceId": "1640154701712MePHwxDG"
}
```

## 13.报价备注查询
> 请求地址：{{projectUrl}}tms/expose/queryQuoteHubRemark
>
> 请求方式：POST
>
> Content-Type=application/x-www-form-urlencoded

### 13.1请求参数

| 字段名  | 类型 | 长度 | 必填 | 单号   |
| ------- | ---- | ---- | ---- | ------ |
| quoteId | int  |      | 必填 | 报价id |

### 13.2响应参数

| 字段名 | 类型   | 说明 |
| ------ | ------ | ---- |
| body   | String | 备注 |

### 13.3接口示例

请求demo

```json
body1=
{
  "quoteId":"1"
}
```

成功响应demo

```json
{
  "body":"sssssss",
  "message":"请求成功",
  "result_code":0
}
```



## 其他注意事项

**1、 签名相关code**

**INVALID_TOKEN（432， "无效TOKEN"）**

**APP_AUTH_ERROR（1001， "AppKey或AppSecret错误，请检查！"）**

**INVALID_TOKEN1（1002， "无效的 Access Token！"）**

**ILLEGAL_REQ（1003， "非法请求！"）**

**EXPIRED_REQ（1004， "请求已过期！"）**

**SIGN_ERROR（1005， "非法请求！"）**

**2、 当code= 1004或1005 就需要获取一次token**

**3、 hubInCode 优先取值渠道列表中的hubInId**
