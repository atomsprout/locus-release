# Locus — Public Preview

面向用户和 Agent 的 macOS 网络诊断工作台：规则代理、按应用分流、可保存和核对的连接证据。

**公开预览版：0.2.0 (40)**。要求 **Apple silicon / macOS 26 或更新版本**。

0.2 新增保存诊断会话、两次会话对比、抓取自检、规则试算与应用前预览、真实节点测试、手动代理组、托管订阅刷新预览，以及需要用户批准的 Agent 临时规则。共 23 个本地 MCP 工具，配合你已有的 Agent 客户端使用。

## 安装或升级

已安装用户先在 Locus 点击 **Stop**，然后：

```sh
brew update
brew upgrade --cask atomsprout/tap/locus
```

首次安装：

```sh
brew tap atomsprout/tap
brew install --cask atomsprout/tap/locus
```

如 Homebrew 提示 `untrusted tap`，先运行 `brew trust --cask atomsprout/tap/locus`，再重试。

手动下载：[Locus 0.2.0 (40)](https://github.com/atomsprout/locus-release/releases/tag/v0.2.0-40)。打开 `/Applications/Locus.app`，保持默认 Direct，手动点击 Start，完成 macOS 网络扩展和代理配置授权。安装不会自动启用网络；重启后也需要手动 Start。

## 0.2 可以做什么

| 能力 | 使用方式 |
|---|---|
| 保存诊断会话 | Sessions 选择准确应用标识，观察 10–600 秒。保存时间窗口、规则快照和证据 ID，独立于滚动日志。结束时等最终同步完成。 |
| 对比与报告 | 对比同一应用的两次已完成会话，查看窗口、目标与配置差异；导出脱敏报告。未知字节数不当成零，连接记录不当成请求成功。 |
| 规则与分流 | Direct / Block / Proxy；按签名标识和域名/后缀匹配，顺序优先。Rules 可试算并在新增前预览。 |
| 手动代理组 | 将多个节点放入组，让规则引用组。选择节点影响新连接；记录保留当时的组和实际节点。 |
| 节点测试 | 通过所选真实节点访问 example.com:80，区分阶段、错误和 HTTP 结果。需要 Start，失败不会回退直连；不是测速或应用成功保证。 |
| 托管订阅 | HTTPS 手动刷新、预览后应用；重命名尽量保留 ID，被引用的已移除节点保留为独立节点。应用更新需先 Stop，随后手动 Start。 |
| Agent 临时规则 | Agent 提议，用户在 Rules 明确批准；30–600 秒到期后扩展自行停止匹配，即使界面退出也有效。配置变化后旧提案不能批准。 |

支持 SOCKS5、HTTP CONNECT、Trojan、Shadowsocks AES-256-GCM / ChaCha20-IETF-Poly1305。凭据及托管订阅 URL 放在 Keychain。保留原有 TCP、UDP、DNS 和本地网络策略；详细限制见 [能力范围](docs/SUPPORTED-FEATURES.zh-CN.txt)。

## Agent 用法

在 Agent Access 复制 MCP 配置。可执行文件：`/Applications/Locus.app/Contents/Helpers/locus-mcp`。

可以说：“为 Safari 建一个 60 秒诊断会话，我现在重现问题。结束后检查是否完整同步，给出连接证据和无法确定的部分，再和上一次比较。”

长任务使用 start / poll / finish，不会占着一次工具调用等待十分钟。保持 Locus 打开以同步记录。共享数据授权应授予实际 Agent 客户端；无需 Full Disk Access。Locus 不内置模型、云端分析或模型 API 密钥。

## 数据和边界

保存会话最多 20 个、每个 5,000 条/8 MiB、总计 128 MiB。清空 Connections 的滚动日志会保留 Sessions；删除会话需单独操作。脱敏报告仍保留应用标识、时间、端口和证据 ID，分享前请检查。

当前没有 HTTPS 正文解密、HTTP 重放/改写、E7、自动故障切换或其他 VPN 隧道协议。`bypassed` / `closed` 都不证明请求成功，模型结论需要核对证据。

发行包已签名、公证。实际验收主要来自隔离 macOS 27 虚拟机；**macOS 26 实际运行和另一台实体 Mac 仍未验证**。历史完整协议矩阵保留原构建范围，不代表所有新版本路径均重复验证。参见[已知限制](docs/KNOWN-ISSUES.zh-CN.txt)。

停止代理请点击 Stop；退出窗口或应用不会停止已经运行的扩展。卸载前先到 Settings 选择 Remove network integration，完成系统提示或重启后再执行 `brew uninstall --cask atomsprout/tap/locus`。

本仓库仅提供发行包、文档和[问题反馈](https://github.com/atomsprout/locus-release/issues)，不包含应用源码。
