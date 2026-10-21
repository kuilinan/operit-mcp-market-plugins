# 瑞幸咖啡 MCP（luckin-coffee）

> ⚠️ **醒目警告**：本插件已适配 Operit **v1.12.2** 的 MCP 导入规范（`streamable_http`）。**旧版客户端安装会直接报 `unsupported transport`，请先把 Operit 升级至 v1.12.2 及以上。**

**当前版本：v1.0.2**

接入瑞幸点单能力的远程 MCP，注册进 Operit 后可以用对话直接查门店、搜商品、看详情、预览订单、下单。（需本人扫码支付，AI 不代付。）

---

## 一、兼容性说明

本插件配置格式遵循 Operit v1.12.2 及以上的 MCP 导入规范，使用 `type: streamable_http` 声明传输方式。若你使用更早版本，请先升级客户端。

- 完整版本变更记录：[CHANGELOG.md](../CHANGELOG.md)
- 安装失败 / 连接报错排错手册：[TROUBLESHOOTING.md](../TROUBLESHOOTING.md)

---

## 二、配置与使用

### 第一步：申请你自己的 Token

访问瑞幸开放平台，登录后申请 MCP Token：

```
https://open.lkcoffee.com/mcp
```

> 🔴 **重要**：瑞幸官网示例代码用的是 `"type": "streamablehttp"`（**没有下划线**），**在 Operit 里会直接报错**。
> 必须改成 `"type": "streamable_http"`（**带下划线**）。这是两套不同命名，不要照抄官网。

### 第二步：在 Operit 里填入配置

打开 **MCP 插件（mcp_plugins）** → 新增 MCP（remote 类型）→ 粘贴：

```json
{
  "mcpServers": {
    "luckin-coffee": {
      "type": "streamable_http",
      "url": "https://gwmcp.lkcoffee.com/order/user/mcp",
      "headers": {
        "Authorization": "Bearer <换成你自己申请的 TOKEN>"
      }
    }
  }
}
```

| 字段 | 值 |
|:--|:--|
| `type` | `streamable_http`（带下划线，固定不变）|
| `url` | `https://gwmcp.lkcoffee.com/order/user/mcp` |
| `headers.Authorization` | `Bearer <你的 Token>`（注意 Bearer 后面有空格）|

### 第三步：校验与调用

1. 保存 → 点 **校验 / 拉取工具列表**
2. 能拉到 8 个工具就算通
3. 在对话里直接说：「帮我来一杯冰美式」「找一下附近的瑞幸门店」

### 可用工具（八个）

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

### 硬边界（别当它全能）

1. ❌ **没有**销量排行接口
2. ❌ **没有**选券 / 换券 / 指定用某张券的能力；唯一优惠是服务端自己算
3. ❌ **没有**核销码接口；核销只能在品牌 App 的「我的订单」里出示
4. ⚠️ 最终**支付确认是个人身份动作**，扫码/输密码必须真人来做

---

## 三、安装警示

> ⚠️ **醒目警告：装不上先看这里**

### 合法传输类型

Operit v1.12.2 的 `McpConfigImportParser` 导入时**只读 `type` 字段**，仅接受两种值：

| 合法值 | 说明 |
|:--|:--|
| `streamable_http` | 本插件使用（**带下划线**）|
| `sse` | 服务端推送模式 |

除此之外的写法不被识别，包括：`http`、`httpStream`、`streamablehttp`（无下划线）、`connectionType` 字段。

### 常见失败原因

| # | 原因 | 判断方法 | 解决 |
|:--|:--|:--|:--|
| 1 | **Operit 未升级至 1.12.2** | 设置 → 关于 → 看版本号 | 升级到 1.12.2+ |
| 2 | **照抄了瑞幸官网示例**（`streamablehttp` 无下划线）| 报错 `不支持的 transport: streamablehttp` | 改成 `streamable_http` |
| 3 | **沿用了旧版配置**（`connectionType` 或 `type: http`）| 报错 `不支持的 transport: http` | 换成 `type: streamable_http` |
| 4 | **底层缓存残留** | 升级了仍报错，重启无效 | 清除 Operit 缓存 → 重装本插件 → 重新导入 |
| 5 | **Token 无效或过期** | 报错 401 / Unauthorized | 回开放平台重新申请 |

### 报错对照表

```
安装失败：mcpServers.luckin-coffee 使用了不支持的 transport: http
  → 旧版配置，改成 type: streamable_http

安装失败：mcpServers.luckin-coffee 使用了不支持的 transport: streamablehttp
  → 抄了瑞幸官网示例，补上下划线：streamable_http

安装失败：mcpServers.luckin-coffee 使用了不支持的 transport: httpStream
  → v1.0.1 旧配置，改成 type: streamable_http
```

更多场景见 [TROUBLESHOOTING.md](../TROUBLESHOOTING.md)。

---

## 四、链接

| 用途 | 链接 |
|:--|:--|
| **GitHub 仓库（本插件源码）** | https://github.com/kuilinan/operit-mcp-market-plugins |
| **版本变更记录** | [CHANGELOG.md](../CHANGELOG.md) |
| **排错手册** | [TROUBLESHOOTING.md](../TROUBLESHOOTING.md) |
| **瑞幸官方 MCP 开放平台** | https://open.lkcoffee.com/mcp |
| **瑞幸 MCP 接口端点** | https://gwmcp.lkcoffee.com/order/user/mcp |
| **联系邮箱** | kuilinan@qq.com |

---

## 五、安全声明

- 本仓**不含**任何真实 token，不发凭据。
- Token 是**你个人**的门票，泄露后请尽快去开放平台作废重发。
- 不要把 token 提交进你 fork 的仓库里。

## 六、联系

邮箱：kuilinan@qq.com