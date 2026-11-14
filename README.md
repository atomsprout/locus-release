# Locus — Public Preview

面向用户和 Agent 的 macOS 网络诊断与抓包工作台：**按应用抓取 HTTP(S)，让 Agent 依据请求响应排查问题，同时保留规则代理与连接证据。**

公开预览版 **0.3.0 (50)**。要求 **Apple silicon / macOS 26 或更新版本**。

## 安装或升级

升级前先在 Locus 点击 **Stop**：

```sh
brew update
brew upgrade --cask atomsprout/tap/locus
```

首次安装：

```sh
brew tap atomsprout/tap
brew install --cask atomsprout/tap/locus
```

如 Homebrew 提示 `untrusted tap`，先运行 `brew trust --cask atomsprout/tap/locus`。也可[直接下载发行包](https://github.com/atomsprout/locus-release/releases/tag/v0.3.0-50)。从 `/Applications/Locus.app` 打开，保持默认 Direct，手动 Start 并完成 macOS 网络扩展/代理配置授权。安装和重启不会自动开始抓取。

## 0.3 可以做什么

| 能力 | 使用方式 |
|---|---|
| HTTP(S) 请求响应 | Requests 审阅准确应用签名 ID、主机、端口和时间范围，显式批准。默认端口 80/443。 |
| AI API 诊断 | 查看脱敏 JSON、JSON SSE 样本、HTTP 状态、掩码头部和不完整/截断状态；支持 HTTP/2 与增量 SSE。 |
| Agent 闭环 | 申请 → 用户批准 → 请求/正文分页查询 → 结束抓取 → 读取已批准的保存证据。共 28 个本地 MCP 工具。 |
| 单独的正文授权 | 与抓取分离，可随时撤销；结束抓取后最多再保留十分钟分析期，历史范围也可单独批准。 |
| 可核对的证据 | 请求 ID、连接 flow ID、实际节点与规则；可按应用和时间关联诊断会话，明确缺失的连接证据。 |
| 原有网络能力 | Direct / Block / Proxy、按应用/域名规则、手动代理组、真实节点测试和托管订阅刷新预览。 |
| 保存诊断 | 10–600 秒诊断会话、同应用会话对比、脱敏报告、自检和规则试算。 |

支持 SOCKS5、HTTP CONNECT、Trojan、Shadowsocks AES-256-GCM / ChaCha20-IETF-Poly1305。正文抓取仍走原有具体节点，凭据保存在 Keychain；失败不会偷偷回退直连。

HTTPS 需要你显式信任本机 Locus CA，也可仅让特定客户端信任公开 CA 文件。**Start 不会自动安装或信任证书。** 私钥留在本机，不随发行包分发。证书固定、部分协议或上游身份配置可能导致失败，具体见[能力范围](docs/SUPPORTED-FEATURES.zh-CN.txt)。

## 给 Agent 的一句话

> 帮我检查这个应用的 API 请求为什么失败。先确认准确应用和目标主机，申请一个抓取窗口；我批准后重现问题。结束后用实际请求和连接 ID 解释响应，说明缺失或不确定的证据。

在 Agent Access 复制 MCP 配置，helper 路径为 `/Applications/Locus.app/Contents/Helpers/locus-mcp`。使用你已有的 Agent 客户端；Locus 不内置模型、云端分析服务或模型 API 密钥。实时抓取时保持 Locus 打开。

## 范围和数据

正文每侧最多 64 KiB，最多 1,000 条/128 MiB，不自动删旧证据。仅保留脱敏 JSON/JSON SSE；任意文本、二进制、超限正文和不支持的编码会注明未保留。原始未脱敏正文不写入捕获文件。脱敏不是匿名化，分享前仍需检查。

原有保存诊断会话最多 20 个、每个 5,000 条/8 MiB、总计 128 MiB。Connections、Sessions 与 Requests 分别管理；清空一类不等于清空其他证据。

当前没有 HTTP 重放/改写、E7、自动故障切换或新 VPN 隧道协议。QUIC/HTTP3、gRPC、WebSocket 帧和 loopback 抓取不在本版范围内。`bypassed` / `closed` 不证明 HTTP 请求成功，Agent 结论必须核对证据。参见[已知限制](docs/KNOWN-ISSUES.zh-CN.txt)。

发行包已签名、公证，实际验收来自隔离 macOS 27.2.1 访客。**macOS 26 实际运行、另一台实体 Mac 和完整长期压力测试仍未完成。** 历史测试保留各自实际构建范围，详见[发行说明](docs/RELEASE-NOTES.zh-CN.txt)。

卸载前：先 Stop；如果曾信任 Locus CA，进入 Requests → HTTPS certificate settings → Remove trust 完成移除，再到 Settings → Remove network integration。完成系统提示或重启后执行 `brew uninstall --cask atomsprout/tap/locus`。退出应用本身不会停止已运行的系统代理。

本仓库提供发行包、文档和[问题反馈](https://github.com/atomsprout/locus-release/issues)。不公开 Locus 项目源码、用户配置或抓取数据；必要第三方许可证与对应源码随引擎提供。
