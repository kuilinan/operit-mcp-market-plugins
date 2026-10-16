## 0. 安装前必读（v1.0.1）

本插件提供的是**配置示例**。它不含任何 Token。
Token 的申请、身份绑定必须由使用者本人前往瑞幸官方开放平台完成。

- 误以为“装完即能跑”：无 Token 时，你在 Operit 里的校验会当场失败。
- 本插件无法帮你、AI 也无法帮你注册账号或领取 Token。
   绑定的是真人账号，这一层只能由你自走。

### 你需要自己做的两件事

1. 访问瑞幸开放平台，登录申请你自己的 MCP Token：
   https://open.lkcoffee.com/mcp
2. 在 Operit 的 MCP 插件设置里新建 remote 类型，粘入下方示例配置，
   并把 Authorization 里的 <TOKEN> 换成你申请到的那串。

### 安装配置模板（只需改一处）

```json
{
  "mcpServers": {
    "luckin-coffee": {
      "connectionType": "httpStream",
      "url": "https://gwmcp.lkcoffee.com/order/user/mcp",
      "headers": {
        "Authorization": "Bearer <换成你自己申请的 TOKEN>"
      }
    }
  }
}
```

### 版本规范

在 Operit 1.12.2 实测中，“type”: “streamablehttp” 会被解析器拒绝
（报 unsupported transport）；请统一使用上方“connectionType”: “httpStream”。
（1.11.x 实测状态未知，写入方式若不相同以本体为准）

### 反馈与联系

发布者邮箱：kuilinan@qq.com

（请勿在 Issue / 评论里贴 Token 或账号页。凡与身份绑定的串一经公开，
请立即去开放平台作废重发。）

---

# 瑞幸咖啡 MCP（luckin-coffee）

一个接入瑞幸点单能力的远程 MCP，注册进 Operit 后可以用对话直接
查门店、搜商品、看详情、预览订单、下单。（需要本人扫码支付，AI 不代付。）

## 一、需要什么

| 项 | 说明 |
|:--|:--|
| MCP 类型 | remote |
| 接入方式 | `connectionType: httpStream`（**不要用 `type: streamablehttp`**）|
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
   - connectionType：`httpStream`（**不要写 `type: streamablehttp`**）
   - 头：`Authorization: Bearer <换成你自己的 TOKEN>`
4. 保存 → 点**校验 / 拉取工具列表**
5. 装成功即可在对话里通过本工具的 `queryShopList`、
   `searchProductForMcp`、`previewOrder`、`createOrder` 等使用

> ⚠️ **版本兼容提示**
> 在 **Operit 1.12.2** 上，配置里写 `"type": "streamablehttp"`
> 会被解析器拒绝（报 `unsupported transport`）。
> 正确写法是用 `"connectionType": "httpStream"`。
> 老版本（1.11.x）行为未经实测，与此不一致的可能存在；
> 如果你的版本发生同类拒绝，请改用上面这套写法。

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

## 七、联系

- 邮箱：kuilinan@qq.com