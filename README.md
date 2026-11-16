# Locus — Public Preview

面向用户和 Agent 的 macOS 网络诊断与抓包工作台：**按应用抓取 HTTP(S)，让 Agent 依据请求响应排查问题，同时保留规则代理与连接证据。**

公开预览版 **0.6.0 (68)**。要求 **Apple silicon / macOS 26 或更新版本**。

## 安装或升级

升级前先在 Locus 点击 **Disconnect**：

```sh
brew update
brew upgrade --cask atomsprout/tap/locus
```

首次安装：

```sh
brew tap atomsprout/tap
brew install --cask atomsprout/tap/locus
```

如 Homebrew 提示 `untrusted tap`，先运行 `brew trust --cask atomsprout/tap/locus`。也可[直接下载发行包](https://github.com/atomsprout/locus-release/releases/tag/v0.6.0-68)。从 `/Applications/Locus.app` 打开，先选择路由模式和节点，手动 Connect 并完成 macOS 网络扩展/代理配置授权。安装和重启不会自动开始抓取。

## 0.6 可以做什么

| 能力 | 使用方式 |
|---|---|
| Header 改写 | 请求/响应 Set、Add、Remove；匹配准确应用、主机、端口、路径前缀和可选方法。保存值使用 Keychain。 |
| 按窗口启用 | 每次抓包默认不改写，明确勾选并核对匹配规则后才生效；结束、到期或引擎退出后停止。 |
| 改写证据 | 查看规则 ID、动作和脱敏变化；通过自己的服务端日志或回显确认真正收到的内容。 |
| 应用发现 | Requests 的 Choose app 按名称、图标或签名 ID 选择；独立显示实际联网身份，未知辅助进程归属不猜测。 |
| 抓包准备 | 审批页 Check preparation 检查扩展、同步、当前路由与证书准备；不自动连接或信任证书。 |
| HTTP 诊断 | Investigate & share 按应用／会话统计 HTTP 错误、传输错误、状态、耗时和证据 ID。 |
| 验证修复 | 比较同一应用已结束、按时间先后的两次会话；显示窗口差异，不把计数变化当作因果证明。 |
| AI 实测信息 | 展示保留样本中的模型、接口报告的 token 用量、SSE 和实际首字节时间；缺失不估算。 |
| 预览分享 | 默认排除正文、目标地址、头部与路由名称；可显式包含样本，先预览再保存 JSON。 |
| 日常代理 | Overview 中切换 Direct / Rules / Global Proxy，选择节点或手动组；显示扩展是否已确认当前配置。 |
| 菜单栏 | 连接／断开、切换模式与出口、重新打开主窗口；关闭窗口后仍可操作。 |
| 节点管理 | 按名称、主机或协议搜索，单个或顺序批量 HTTP 检查，查看延迟、时间与失败阶段；旧配置结果明确标注。 |
| 托管订阅 | HTTPS 下载与更新预览、更新时间、保留被引用节点；可只忘记来源、保留节点。更新需先断开。 |
| HTTP(S) 请求响应 | Requests 审阅准确应用签名 ID、主机、端口和时间范围，显式批准。默认端口 80/443。 |
| AI API 诊断 | 查看脱敏 JSON、JSON SSE 样本、HTTP 状态、掩码头部和不完整/截断状态；支持 HTTP/2 与增量 SSE。 |
| Agent 闭环 | 申请 → 用户批准 → 请求/正文分页查询 → 结束抓取 → 读取已批准的保存证据。共 36 个本地 MCP 工具。 |
| 单独的正文授权 | 与抓取分离，可随时撤销；结束抓取后最多再保留十分钟分析期，历史范围也可单独批准。 |
| 可核对的证据 | 请求 ID、连接 flow ID、实际节点与规则；可按应用和时间关联诊断会话，明确缺失的连接证据。 |
| 原有网络能力 | Direct / Block / Proxy、按应用/域名规则、手动代理组、真实节点测试和托管订阅刷新预览。 |
| 保存诊断 | 10–600 秒诊断会话、同应用会话对比、脱敏报告、自检和规则试算。 |

日常代理与抓包独立：普通代理使用不需要解密 CA。初始配置是 Rules 模式、未匹配规则时 Direct；需要代理时先添加自己的节点或订阅，选择 Global Proxy 或配置应用规则，再 Connect。Locus 不提供节点服务。模式或节点变化仅影响新连接；应等待“Applied to new connections”。

支持 SOCKS5、HTTP CONNECT、Trojan、Shadowsocks AES-256-GCM / ChaCha20-IETF-Poly1305。正文抓取仍走原有具体节点，凭据保存在 Keychain；失败不会偷偷回退直连。

HTTPS 需要你显式信任本机 Locus CA，也可仅让特定客户端信任公开 CA 文件。**Connect 不会自动安装或信任证书。** 私钥留在本机，不随发行包分发。证书固定、部分协议或上游身份配置可能导致失败，具体见[能力范围](docs/SUPPORTED-FEATURES.zh-CN.txt)。

## 给 Agent 的一句话

> 帮我检查这个应用的 API 请求为什么失败。先确认准确应用和目标主机，申请一个抓取窗口；我批准后重现问题。结束后用实际请求和连接 ID 解释响应，说明缺失或不确定的证据。

在 Agent Access 复制 MCP 配置，helper 路径为 `/Applications/Locus.app/Contents/Helpers/locus-mcp`。使用你已有的 Agent 客户端；Locus 不内置模型、云端分析服务或模型 API 密钥。实时抓取时保持 Locus 打开。

Header 改写也支持 Agent 提案，但提案不包含替换值，不会直接改变流量。你需要在 Requests → Header rewrites 审核、填值并保存，再在抓包确认中勾选启用。结束抓包会等待网络扩展确认撤销，普通代理保持独立。

## 范围和数据

正文每侧最多 64 KiB，最多 1,000 条/128 MiB，不自动删旧证据。仅保留脱敏 JSON/JSON SSE；任意文本、二进制、超限正文和不支持的编码会注明未保留。原始未脱敏正文不写入捕获文件。脱敏不是匿名化，分享前仍需检查。

原有保存诊断会话最多 20 个、每个 5,000 条/8 MiB、总计 128 MiB。Connections、Sessions 与 Requests 分别管理；清空一类不等于清空其他证据。

Global 是符合范围流量的代理模式；仍遵守系统排除和本地网络绕过，不代表所有 IP 流量或防泄漏。不能以此替代 WireGuard/OpenVPN 客户端，也没有 kill switch。本机 loopback 节点不可用于 Global，避免递归；本地代理客户端应使用 Rules 并配置它自身的 Direct 例外，任意 DNS 别名或本机 LAN 地址不保证被识别为本地节点。

当前没有断点暂停编辑、HTTP 重放、Mock、E7、自动故障切换或新 VPN 隧道协议。QUIC/HTTP3、gRPC、WebSocket 帧和 loopback 抓取不在本版范围内。`bypassed` / `closed` 不证明 HTTP 请求成功，Agent 结论必须核对证据。参见[已知限制](docs/KNOWN-ISSUES.zh-CN.txt)。

发行包已签名、公证。最终 68 的 **macOS 26.6.2 (25G83)** / **27.2.1 (26B131)** 虚拟机验收、146 项核心测试、14 组签名 MCP 集成测试与实际测试时长见[0.6 验收说明](docs/ACCEPTANCE-0.6.zh-CN.txt)。这不代表实体机、真实休眠唤醒、Wi-Fi 漫游或多日稳定性认证。历史测试保留各自实际构建范围。

卸载前：先 Disconnect；如果曾信任 Locus CA，进入 Requests → HTTPS certificate settings → Remove trust 完成移除，再到 Settings → Remove network integration。完成系统提示或重启后执行 `brew uninstall --cask atomsprout/tap/locus`。退出应用本身不会停止已运行的系统代理。

本仓库提供发行包、文档和[问题反馈](https://github.com/atomsprout/locus-release/issues)。不公开 Locus 项目源码、用户配置或抓取数据；必要第三方许可证与对应源码随引擎提供。
