# CHANGELOG

本仓库包含两个 MCP 插件：`luckin-coffee`（瑞幸咖啡）、`mt-manager`（MT 管理器）。
两者的配置格式演进完全同步，统一记录于此。

---

## v1.0.2 — 2026-10-21

**类型**：协议兼容性修复

- 修复：配置文件字段改为 `type: "streamable_http"`，适配 Operit v1.12.2 的 MCP 导入解析器
- 移除：`connectionType` 字段（v1.12.2 起导入阶段不再读取该字段）
- 影响：v1.0.1 及更早的配置文件在 Operit v1.12.2+ 上会报 `安装失败：... 使用了不支持的 transport`
- 客户端要求：**Operit v1.12.2 及以上**
- 文档：新增本 CHANGELOG 与 [TROUBLESHOOTING.md](./TROUBLESHOOTING.md)

**当前唯一正确写法**

```json
{
  "mcpServers": {
    "<server-name>": {
      "type": "streamable_http",
      "url": "<端点>",
      "headers": { "Authorization": "Bearer <TOKEN>" }
    }
  }
}
```

---

## v1.0.1 — 2026-10-15

**类型**：连接字段调整

- 变更：配置字段由 `type: "http"` 改为 `connectionType: "httpStream"`
- 原因：适配当时客户端版本的连接方式声明
- 备注：该写法在 Operit v1.12.2 起不再被导入解析器识别

---

## v1.0.0 — 2026-10-13

**类型**：首发

- 首次发布 `luckin-coffee` 与 `mt-manager` 两个 MCP 插件
- 配置字段：`type: "http"`

---

## 附：字段演进时间线（供排错参考）

| 日期 | Operit 版本 | 配置写法 | 结果 |
|:--|:--|:--|:--|
| 2026-10-13 | 早期版本 | `"type": "http"` | 当时可用 |
| 2026-10-15 | 中期版本 | `"connectionType": "httpStream"` | 当时可用 |
| 2026-10-21 | **v1.12.2** | `"type": "streamable_http"` | **当前唯一正确** |

> 说明：三个版本对应的写法差异，源于客户端导入解析器在不同版本间对字段名的要求发生变化。本仓库每次都按当期解析器的实际行为同步配置，以保证可安装。遇到安装失败请先核对客户端版本，再对照上表选择写法。