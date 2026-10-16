## 0. 安装前必读（v1.0.1）
本插件提供的是**配置示例**。它不含任何 Token。
Token 由你手机上的 MT 管理器生成，服务需你本人先在本机跑起来。
- 误以为“装完即能跑”：没在手机上先开 MT 的 MCP 服务，校验会当场失败。
- 本插件无法帮你、AI 也无法替你跑服务或生成 Token。
   本机服务各人各自启动，这一层只能由你自走。

### 你需要自己做的两件事
1. 打开 MT 管理器，启动它的 MCP 服务，记下它给出的本机地址与 Bearer Token。
2. 在 Operit 的 MCP 插件设置里新建 remote 类型，粘入下方示例配置，
   并把 Authorization 里的 <YOUR_MT_TOKEN> 换成 MT 生成的那串。
### 安装配置模板（只需改一处）
```json
{
  "mcpServers": {
    "mt-manager": {
      "connectionType": "httpStream",
      "url": "http://127.0.0.1:8787/mcp",
      "headers": {
        "Authorization": "Bearer <YOUR_MT_TOKEN>"
      }
    }
  }
}
```
### 版本规范
连接方式请写 `"connectionType": "httpStream"`（官方 MCP 文档列明的远程连接方式；
旧版示例中的 `"type": "http"` 已按此修正）。

### 反馈与联系
发布者邮箱：kuilinan@qq.com
（请勿在 Issue / 评论里贴 Token。凡与身份绑定的串一经公开，请立即作废重发。）

---

# MT 管理器 MCP（mt-manager）

接入 MT 管理器逆向能力的远程 MCP，注册进 Operit 后能用对话检查 / 编辑
APK 的资源表、DEX、so、(smali)、字节 patch、构建与签名。

## 一、需要什么

| 项 | 说明 |
|:--|:--|
| MCP 类型 | remote |
| 接入方式 | `connectionType: httpStream`（**不要用 `type: http`**）|
| 端点 | `http://127.0.0.1:8787/mcp` ← **本机回环** |
| 认证方式 | HTTP Header **Bearer** TOKEN |
| TOKEN 从哪来 | 由 MT 管理器客户端生成，**各人自有**，本仓不提供 |
| 前提 | 必须在**自己手机 开 MT 的 MCP 服务** |
| 补充可达地址 | 同机的 `192.168.1.100:8787`（同网可用，示例地址）|

> ❗ **必须看懂这条**：别人的手机打开 `127.0.0.1` 永远是**别人自己的 machine**。
> 所以本 MCP 的前提是**对方自己在机器上已把 MT 的 MCP 服务跑起来**，
> 若没有服务，填上地址也连不上。

## 二、在 Operit 里怎么装

1. 打开 MT 管理器，启动它的 **MCP 服务**（需要授予它必要权限；建议关闭
   此项的电池优化、允许后台长驻，否则息屏后会掉）
2. 在手机上看到它给出的**本机地址**与 **Bearer Token**
3. 打开 Operit MCP 插件设置，新增一个 remote MCP：
   - 连接方式：`httpStream`（**不要写 `type: http`**）
   - 端点：`http://127.0.0.1:8787/mcp`
   - 头：`Authorization: Bearer <填入 MT 自己生成的值>`
4. 保存，点校验 → 能拉到工具列表就算通

## 三、能干什么（概览）

APK 工作区｜资源（ARSC）读取与编辑｜DEX 阅读 / 类大纲 / Smali｜
全文搜索（字符串 / 类名 / 方法）｜交叉引用（xref）｜
原生库 ELF（inspect / xref / 反汇编 / CFG / patch）｜
任意文本 / 字节 patch｜构建与签名（可保全原签名）。

## 四、硬边界

1. 端口是本机回环，**无法分享给异网的人**
2. 依赖对方本机把 MT 的 MCP 服务跑着
3. 客户端必须在**本机**，而非它的地址连不上

## 五、安全声明

- 本仓不提供 MT token。
- 这是用来搞技术的工具，不是可以任开横口的大门。

## 六、联系
- 邮箱：kuilinan@qq.com
