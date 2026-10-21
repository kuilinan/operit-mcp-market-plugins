# MT 管理器 MCP（mt-manager）

> ⚠️ **醒目警告**：本插件已适配 Operit **v1.12.2** 的 MCP 导入规范（`streamable_http`）。**旧版客户端安装会直接报 `unsupported transport`，请先把 Operit 升级至 v1.12.2 及以上。**

**当前版本：v1.0.2**

接入 MT 管理器逆向能力的远程 MCP，注册进 Operit 后能用对话检查 / 编辑 APK 的资源表、DEX、so、smali、字节 patch、构建与签名。

---

## 一、兼容性说明

本插件配置格式遵循 Operit v1.12.2 及以上的 MCP 导入规范，使用 `type: streamable_http` 声明传输方式。若你使用更早版本，请先升级客户端。

- 完整版本变更记录：[CHANGELOG.md](../CHANGELOG.md)
- 安装失败 / 连接报错排错手册：[TROUBLESHOOTING.md](../TROUBLESHOOTING.md)

---

## 二、配置与使用

### 第一步：在手机上启动 MT 的 MCP 服务

打开 MT 管理器 → 启动 **MCP 服务**：

- 授予它必要权限
- 关闭此应用的电池优化，允许后台长驻（否则息屏后会掉）
- 记下它给出的**本机地址**与 **Bearer Token**

> ❗ **必须看懂这条**：`127.0.0.1` 指的就是**你自己这台手机**。
> 如果 MT 的 MCP 服务没跑起来，填上地址也连不上。
> 别人的手机打开 `127.0.0.1` 永远是别人自己的机器。

### 第二步：在 Operit 里填入配置

打开 **MCP 插件（mcp_plugins）** → 新增 MCP（remote 类型）→ 粘贴：

```json
{
  "mcpServers": {
    "mt-manager": {
      "type": "streamable_http",
      "url": "http://127.0.0.1:8787/mcp",
      "headers": {
        "Authorization": "Bearer <填入 MT 自己生成的值>"
      }
    }
  }
}
```

| 字段 | 值 |
|:--|:--|
| `type` | `streamable_http`（带下划线，固定不变）|
| `url` | `http://127.0.0.1:8787/mcp`（本机回环）|
| `headers.Authorization` | `Bearer <你的 Token>`（注意 Bearer 后面有空格）|

> 💡 同网段的另一台设备可以用 `http://192.168.1.100:8787/mcp`（示例地址，换成实际 IP）。

### 第三步：校验与调用

1. 保存 → 点 **校验 / 拉取工具列表**
2. 能拉到工具列表就算通
3. 在对话里直接说：「看看这个 APK 的 manifest」「把这段 smali 改成...」

### 能干什么（概览）

APK 工作区｜资源（ARSC）读取与编辑｜DEX 阅读 / 类大纲 / Smali｜全文搜索（字符串 / 类名 / 方法）｜交叉引用（xref）｜原生库 ELF（inspect / xref / 反汇编 / CFG / patch）｜任意文本 / 字节 patch｜构建与签名（可保全原签名）。

### 硬边界

1. 端口是本机回环，**无法分享给异网的人**
2. 依赖你本机把 MT 的 MCP 服务跑着
3. 客户端必须在**本机**，否则地址连不上

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
| 2 | **沿用了 v1.0.0 旧配置**（`type: http`）| 报错 `不支持的 transport: http` | 换成 `type: streamable_http` |
| 3 | **沿用了 v1.0.1 旧配置**（`connectionType: httpStream`）| 报错 `不支持的 transport: httpStream` | 换成 `type: streamable_http` |
| 4 | **底层缓存残留** | 升级了仍报错，重启无效 | 清除 Operit 缓存 → 重装本插件 → 重新导入 |
| 5 | **MT 的 MCP 服务没启动** | 报错连接拒绝 / Connection refused | 回 MT 启动 MCP 服务 |
| 6 | **Token 无效** | 报错 401 / Unauthorized | 回 MT 重新生成 Token |

### 报错对照表

```
安装失败：mcpServers.mt-manager 使用了不支持的 transport: http
  → 旧版配置，改成 type: streamable_http

安装失败：mcpServers.mt-manager 使用了不支持的 transport: httpStream
  → v1.0.1 旧配置，改成 type: streamable_http

校验失败：Connection refused / 连接被拒绝
  → MT 的 MCP 服务没跑起来，回 MT 里启动它
```

更多场景见 [TROUBLESHOOTING.md](../TROUBLESHOOTING.md)。

---

## 四、链接

| 用途 | 链接 |
|:--|:--|
| **GitHub 仓库（本插件源码）** | https://github.com/kuilinan/operit-mcp-market-plugins |
| **版本变更记录** | [CHANGELOG.md](../CHANGELOG.md) |
| **排错手册** | [TROUBLESHOOTING.md](../TROUBLESHOOTING.md) |
| **联系邮箱** | kuilinan@qq.com |

---

## 五、安全声明

- 本仓**不含**任何真实 token，不发凭据。
- Token 由你本机 MT 生成，泄露后请在 MT 里重新生成。
- 不要把 token 提交进你 fork 的仓库里。

## 六、联系

邮箱：kuilinan@qq.com