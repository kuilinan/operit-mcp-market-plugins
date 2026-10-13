# 瑞幸咖啡 MCP（luckin-coffee）

一个接入瑞幸点单能力的远程 MCP，注册进 Operit 后可以用对话直接
查门店、搜商品、看详情、预览订单、下单。（需要本人扫码支付，AI 不代付。）

## 一、需要什么

| 项 | 说明 |
|:--|:--|
| MCP 类型 | remote |
| 接入方式 | streamablehttp |
| 端点 | `https://gwmcp.lkcoffee.com/order/user/mcp` |
| 认证方式 | HTTP Header **Bearer** TOKEN |
| TOKEN 从哪来 | 由品牌渠道发放，**各人自有，请自行获取**，本仓不提供 |
| 附加说明 | 端点中的域名/路径以品牌最新文档为准 |

## 二、在 Operit 里怎么装

1. 打开 **MCP 插件（mcp_plugins）** 配置
2. 新增一个 MCP（remote 类型）
3. 填：
   - 名称（自取）：`瑞幸咖啡`
   - 端点 URL：`https://gwmcp.lkcoffee.com/order/user/mcp`
   - 传输：`streamablehttp`
   - 头：`Authorization: Bearer <换成你自己的 TOKEN>`
4. 保存 → 点**校验 / 拉取工具列表**
5. 装成功即可在对话里通过本工具的 `queryShopList`、
   `searchProductForMcp`、`previewOrder`、`createOrder` 等使用

## 三、能力（八个工具）

| 工具 | 用途 |
|:--|:--|
| `queryShopList` | 查附近门店 |
| `searchProductForMcp` | 按关键词搜商品 |
| `switchProduct` | 切换商品规格 |
| `queryProductDetailInfo` | 查单个商品详情 |
| `previewOrder` | **算现货价**（不生成订单、不扣钱）|
| `createOrder` | 生成订单（产生支付二维码，待你本人支付）|
| `queryOrderDetailInfo` | 查订单状态 / 取餐码 |
| `cancelOrder` | 取消订单 |

## 四、硬边界（照实说清，别当它全能）

1. ❌ **没有**销量排行接口
2. ❌ **没有**任何选券 / 换券 / 手工用某一特定张券的能力；唯一优惠是服务端自己算
3. ❌ **没有**核销码接口；核销手续只能在品牌 App 的「我的订单」里出示完成
4. ⚠️ 最终**支付确认是个人身份动作**，扫码/输密码必须真人来做

## 五、示例流程

```json
// createOrder 请求示例（productList 由 searchProductForMcp 得到）
{
  "deptId": "<门店ID>",
  "productList": [
    { "productId": "<商品ID>", "skuCode": "<SKU>", "amount": 1 }
  ],
  "longitude": "<经度>",
  "latitude": "<纬度>"
}
```

## 六、安全声明

- 本仓**不含**任何真实 token，不发凭据。
- Token 是**你个人**的门票，泄露后请尽快去对应处作废重发。
- 不要把 token 提交进你 fork 的仓库里。
