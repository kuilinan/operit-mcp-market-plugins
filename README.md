# Operit MCP Market Plugins

这是给 Operit「MCP 市场」用的发布仓。内含两份 MCP 接入说明卡。

> ⚠️ 本仓库不包含任何真实令牌。所有 token 请使用者自行去对应品牌官方渠道获取。

## 目录

| 插件 | 目录 | 端点 | 类型 |
|:--|:--|:--|:--|
| 瑞幸咖啡 | [`luckin-coffee/`](./luckin-coffee/README.md) | `https://gwmcp.lkcoffee.com/order/user/mcp` | 远程 MCP（公有云）|
| MT 管理器 | [`mt-manager/`](./mt-manager/README.md) | `http://127.0.0.1:8787/mcp` | 远程 MCP（本机）|

## 装之前先读

- 两个都是 **remote**（远程）型 MCP，需要各自的客户端/服务软件先跑起来。
- 凭据都不在库里，**需要你自己去要**（门口、商店页、品牌文档）。
- MT 管理器的端口是**本机回环**（`127.0.0.1`），只有在**跑了该服务的手机**上才有用。
