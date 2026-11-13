# Locus — Public Preview

面向用户与 Agent 的 macOS 规则代理、按应用分流和连接记录工具。

**公开预览版：0.1.0 (31)**。要求 **Apple silicon / macOS 26 或更新版本**。

本仓库只提供发行包、使用说明和反馈入口，不包含应用源码。当前版本已签名、公证，最低部署版本为 macOS 26。此前 macOS 27 虚拟机完成了安装、授权、真实抓取、升级、重启与卸载验收；这些历史结果不代表 macOS 26 已通过运行验收。实体 Mac 反馈和验收尚未完成；历史协议及模型测试保留各自的构建范围。

## Homebrew 安装

先安装或更新 Homebrew，然后执行：

```sh
brew update
brew tap atomsprout/tap
brew install --cask atomsprout/tap/locus
```

如新版 Homebrew 提示 `untrusted tap`，运行 `brew trust --cask atomsprout/tap/locus`，然后重试安装或升级。

手动下载：[Locus 0.1.0 (31)](https://github.com/atomsprout/locus-release/releases/tag/v0.1.0-31)。

Homebrew 仅安装应用，不会自动启用网络扩展。打开 `/Applications/Locus.app`，保持默认 **Direct**，点击 **Start**，按 macOS 提示允许网络扩展和代理配置。重启后需要再次手动 Start。

## 首版能力

- 应用签名标识、域名/后缀规则，Direct / Block / Proxy 分流，单条规则暂停。
- SOCKS5、HTTP CONNECT、Trojan、Shadowsocks AES-256-GCM / ChaCha20-IETF-Poly1305。
- 连接元数据、状态和错误，保留记录搜索、详情与 JSON 导出。
- 九个本地 stdio MCP 工具，供已有 Agent 按应用观察、查询、解释和汇总证据；客户端和模型需自备。

当前不提供 HTTPS 正文解密、HTTP 重放或 E7 脚本。`bypassed` 表示规则放行，不证明请求成功。外部模型仍可能推断或措辞出错，需要核对原始记录 ID。

## 停止、升级和卸载

停止请点击 **Stop**。关闭窗口或退出应用不会自动停止已运行的网络扩展。

升级前先 Stop，然后：

```sh
brew update
brew upgrade --cask atomsprout/tap/locus
```

卸载前必须在 **Settings → Remove network integration** 中移除网络集成，按系统要求批准或重启，再运行：

```sh
brew uninstall --cask atomsprout/tap/locus
```

不要手动删除 `/Library/SystemExtensions`。移除网络集成保留规则、历史和 Keychain 凭据。

## Agent 接入

[MCP 配置示例](docs/MCP.example.json)：

```json
{"mcpServers":{"locus":{"command":"/Applications/Locus.app/Contents/Helpers/locus-mcp","args":[]}}}
```

这是本地可执行文件，不是 HTTP 地址。首次调用若被 macOS 拒绝，在 **Privacy & Security → Files & Folders** 中，为实际 Agent 客户端开启 **Data shared by Locus and affiliated apps**。无需 Full Disk Access。

## 说明与反馈

- [安装、代理与 Agent 使用指南](docs/START-HERE.zh-CN.txt)
- [支持范围](docs/SUPPORTED-FEATURES.zh-CN.txt)
- [已知限制](docs/KNOWN-ISSUES.zh-CN.txt)
- [首版说明](docs/RELEASE-NOTES.zh-CN.txt)
- [测试与反馈清单](docs/TEST-CHECKLIST.zh-CN.txt)
- [提交问题](https://github.com/atomsprout/locus-release/issues/new/choose)

请提供版本、Mac 芯片、macOS、复现步骤和 Stop 后恢复情况。不要提交代理密码、订阅令牌、API 密钥或未经检查的完整连接历史。
