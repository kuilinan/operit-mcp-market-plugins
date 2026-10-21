# TROUBLESHOOTING — 安装与连接排错手册

覆盖 `luckin-coffee` 与 `mt-manager` 两个插件的常见故障。**按顺序往下查，绝大多数问题在前三步就能解决。**

---

## 第 0 步：先确认三件事

| 检查项 | 怎么查 |
|:--|:--|
| Operit 版本 ≥ 1.12.2 | 设置 → 关于 → 版本号 |
| `type` 写的是 `streamable_http`（带下划线）| 逐字看，别凭印象 |
| 配置里没有 `connectionType` 字段 | 搜索一下，有就删掉 |

这三项任一不满足，下面的排查都不用做。

---

## 一、报错：`不支持的 transport: xxx`

**这是导入阶段被解析器拒收，不是网络问题。**

Operit v1.12.2 的 `McpConfigImportParser` 只读 `type` 字段，仅接受 `streamable_http` 或 `sse`。

| 报错里的值 | 来源 | 修法 |
|:--|:--|:--|
| `http` | v1.0.0 旧配置 | 改成 `streamable_http` |
| `httpStream` | v1.0.1 旧配置 | 改成 `streamable_http` |
| `streamablehttp` | 抄了瑞幸官网示例（无下划线）| 补下划线 → `streamable_http` |
| `connectionType` | 用了内部字段当导入字段 | 删掉，改用 `type` |

**最容易踩的坑**：瑞幸官方开放平台 `https://open.lkcoffee.com/mcp` 的示例代码写的是 `"type": "streamablehttp"`（不带下划线），和 Operit 要求的 `streamable_http`（带下划线）**差一个字符**。照抄官网必失败。

---

## 二、报错：`Connection refused` / 连接被拒绝

**这是连不上服务端，配置本身是对的。**

| 插件 | 原因 | 解决 |
|:--|:--|:--|
| `mt-manager` | MT 管理器的 MCP 服务没启动 | 打开 MT → 启动 MCP 服务 |
| `mt-manager` | 息屏后服务被系统杀掉 | 关闭 MT 的电池优化，允许后台长驻 |
| `luckin-coffee` | 端点域名不可达 | 检查网络；代理环境下确认 MCP 域名走直连 |
| 两者 | 地址写错 | `mt-manager` 是 `http://127.0.0.1:8787/mcp`；`luckin-coffee` 是 `https://gwmcp.lkcoffee.com/order/user/mcp` |

**关于 `127.0.0.1`**：它永远指"当前这台机器"。MT 的 MCP 服务跑在手机上，所以只能在手机上连；换台设备连 `127.0.0.1` 连的是那台设备自己。

---

## 三、报错：`401` / `Unauthorized` / 鉴权失败

| 插件 | 原因 | 解决 |
|:--|:--|:--|
| 两者 | Token 失效 / 过期 | 重新申请或重新生成 |
| 两者 | `Bearer` 后面漏了空格 | 必须是 `Bearer <token>`，中间一个空格 |
| 两者 | Token 前后带空格或引号 | 去掉 |
| `mt-manager` | MT 重启后 Token 变了 | 回 MT 重新复制 |

---

## 四、升级到 1.12.2 后仍然报旧错

**缓存残留。** 按这个顺序做：

1. 清除 Operit 应用缓存（不是数据）
2. 卸载已装的旧插件
3. 重启 Operit
4. 重新导入 v1.0.2 配置

仍有问题就清数据重装，但会丢失本地会话记录，先备份。

---

## 五、能连上但拉不到工具列表

| 可能 | 排查 |
|:--|:--|
| 服务端返回空列表 | 用浏览器 / curl 直接请求端点，看返回体 |
| 客户端版本过旧，工具协议不匹配 | 升级 Operit |
| `luckin-coffee` 的 Token 无对应权限 | 回开放平台看授权范围 |

---

## 六、插件装上了但对话里调不动

1. 确认插件在 MCP 列表里处于**启用**状态
2. 确认已拉到工具列表（不是空）
3. 明确说清动作，例如「查一下我附近 2 公里内的瑞幸门店」，不要只说「点咖啡」

---

## 七、终极兜底

如果以上全部无效：

1. 到仓库 https://github.com/kuilinan/operit-mcp-market-plugins 对应目录取最新 README 与 `mcp_config.example.json`
2. 用最新文件逐字对照你的配置，逐字符比对 `type` 值
3. 仍不通，把**完整报错原文 + Operit 版本号 + 你粘贴的配置（Token 打码）**发到 kuilinan@qq.com

⚠️ 发邮件时**不要贴真实 Token**，用 `***` 代替。