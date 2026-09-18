# ChatFate

**简体中文** · [English](README.en.md) · [繁體中文](README.zh-Hant.md)

在熟悉的对话里，问一件事、认识自己。ChatFate 是由 ChatFate 团队维护的八字与六爻插件，通过远程服务完成排盘、解读和报告。

[访问官网](https://chatfate.cc) · [安装说明](https://chatfate.cc/install.md)

## 可以做什么

- **今日指引**：看看今天值得留意的事。
- **问一件事**：围绕具体问题进行六爻解读。
- **年度主题**：了解一年的主题和不同阶段。
- **人生命盘**：通过八字解读认识自己。
- 登录 Google 账号后，保存并回看自己的报告。

## 安装与更新

以下安装方式适用于 macOS 上的 ChatGPT／Codex 桌面应用，需要能够使用 Codex 插件的环境；不是把指令粘贴到任意聊天网页就能安装。

把下面这句话发送到 Codex：

> 阅读 https://chatfate.cc/install.md，帮我在 Codex 中安装 ChatFate 插件；安装完成后创建一个新任务并开始使用 ChatFate。

也可以在终端运行官网安装器：

```sh
curl -fsSL https://chatfate.cc/i | sh
```

安装器使用桌面应用内置的 Codex CLI，从本仓库的 `main` 分支安装或更新 ChatFate，并确认官方 Browser 插件已启用。旧版安装也使用同一个入口更新。

安装后，在新任务中选择 `@ChatFate`，发送“开始使用 ChatFate”。不要在已经运行的旧任务里判断新插件是否加载成功。

如安装失败，请保留错误信息和插件版本，通过本仓库的 Issues 反馈；不要公开 API Key、登录凭据或私人报告链接。

## 这个仓库包含什么

这里是供安装使用的公开插件包，包含插件声明、远程 MCP 连接配置、Skills 和品牌资源。排盘引擎、网站、账号、支付、数据库及部署源码不在本仓库中。

维护人员在私有工程开发和测试，审核后只导出公开插件文件，以 ChatFate 员工账号发布。公开仓库仅保留 `main` 分支，每次正式发布记录一个发布提交，不合入私有开发历史。

## 使用说明

八字与六爻属于传统文化解读，不保证现实预测结果，也不能替代医疗、法律或投资等专业建议。ChatGPT／Codex 的订阅及模型使用费用，与 ChatFate 报告购买相互独立。

[隐私政策](https://chatfate.cc/privacy) · [使用条款](https://chatfate.cc/terms)
