---
title: Claude Code 最佳实践与使用指南
date: 2026-06-28 12:00:00 +0800
categories: [AI, 工具]
tags: [claude, best-practices, ai, developer-tools]
description: >-
  Claude Code 是一个代理编码工具，可以读取代码库、编辑文件、运行命令并与开发工具集成，覆盖终端、IDE、桌面应用和浏览器。
---

Claude Code 是一个由 AI 驱动的编码助手，可帮助你构建功能、修复错误和自动化开发任务。它理解你的整个代码库，可以跨多个文件和工具工作以完成任务。

## 目录

- [开始使用](#开始使用)
- [你可以做什么](#你可以做什么)
- [在任何地方使用 Claude Code](#在任何地方使用-claude-code)
- [后续步骤](#后续步骤)

## 开始使用

选择你的环境来开始使用。大多数界面需要 [Claude 订阅](https://claude.com/pricing) 或 [Anthropic 控制台](https://console.anthropic.com/) 账户。终端 CLI 和 VS Code 也支持[第三方提供商](/zh-CN/third-party-integrations)。

### 终端（Terminal CLI）

功能完整的 CLI，直接在终端中使用 Claude Code。编辑文件、运行命令，从命令行管理整个项目。

**安装方式：**

**原生安装（推荐）：**

macOS / Linux / WSL：
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Windows PowerShell：
```powershell
irm https://claude.ai/install.ps1 | iex
```

Windows CMD：
```batch
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

> **提示**：`PS C:\` 提示符表示 PowerShell，`C:\` 表示 CMD。原生安装会自动更新到最新版本。

**Homebrew：**
```bash
brew install --cask claude-code
```

- `claude-code` 跟踪稳定发布频道（约滞后一周）
- `claude-code@latest` 跟踪最新频道
- Homebrew 安装不会自动更新，需运行 `brew upgrade claude-code`

**WinGet：**
```powershell
winget install Anthropic.ClaudeCode
```

> WinGet 安装不会自动更新，需定期运行 `winget upgrade Anthropic.ClaudeCode`。

也可通过 `apt`、`dnf` 或 `apk` 在 Debian、Fedora、RHEL、Alpine 上安装。

**启动 Claude Code：**

```bash
cd your-project
claude
```

首次使用时会提示登录。详见[快速入门](/zh-CN/quickstart)和[高级设置](/zh-CN/setup)。

### VS Code

VS Code 扩展直接在编辑器中提供内联差异、@-提及、计划审查和对话历史。

- [为 VS Code 安装](vscode:extension/anthropic.claude-code)
- [为 Cursor 安装](cursor:extension/anthropic.claude-code)

或按 `Cmd+Shift+X` / `Ctrl+Shift+X` 打开扩展视图，搜索 `Claude Code`，点击**安装**。

详见 [VS Code 使用指南 →](/zh-CN/vs-code#get-started)

### 桌面应用

独立应用，在 IDE 或终端之外运行 Claude Code。直观查看差异、并行运行多个会话、安排定期任务、启动云会话。

下载：
- [macOS](https://claude.ai/api/desktop/darwin/universal/dmg/latest/redirect)（Intel / Apple Silicon）
- [Windows x64](https://claude.ai/api/desktop/win32/x64/setup/latest/redirect)
- [Windows ARM64](https://claude.ai/api/desktop/win32/arm64/setup/latest/redirect)

安装后启动 Claude，登录，点击**代码**标签开始编码。需要[付费订阅](https://claude.com/pricing)。详见[桌面应用指南 →](/zh-CN/desktop-quickstart)

### Web

在 [claude.ai/code](https://claude.ai/code) 的浏览器中运行 Claude Code，无需本地设置。适合长时间运行的任务、非本地仓库、并行多任务。详见[网络使用指南 →](/zh-CN/web-quickstart)

### JetBrains

用于 IntelliJ IDEA、PyCharm、WebStorm 等 JetBrains IDE 的插件。从 JetBrains Marketplace 安装 [Claude Code 插件](https://plugins.jetbrains.com/plugin/27310-claude-code-beta-)，重启 IDE。需单独安装 CLI。详见 [JetBrains 指南 →](/zh-CN/jetbrains)

## 你可以做什么

### 自动化繁琐任务

Claude Code 处理占用时间的重复任务：编写测试、修复 lint 错误、解决合并冲突、更新依赖项和编写发布说明。

```bash
claude "write tests for the auth module, run them, and fix any failures"
```

### 构建功能和修复错误

用自然语言描述需求。Claude Code 会规划方法、跨多文件编写代码并验证结果。粘贴错误消息或描述症状，它会追踪问题、识别根本原因、实施修复。详见[常见工作流](/zh-CN/common-workflows)。

### 创建提交和拉取请求

```bash
claude "commit my changes with a descriptive message"
```

在 CI 中使用 [GitHub Actions](/zh-CN/github-actions) 或 [GitLab CI/CD](/zh-CN/gitlab-ci-cd) 自动化代码审查和问题分类。

### 使用 MCP 连接工具

[Model Context Protocol (MCP)](/zh-CN/mcp) 是一种连接 AI 工具与外部数据源的开放标准。Claude Code 可读取 Google Drive 设计文档、更新 Jira 工单、拉取 Slack 数据或使用自定义工具。详见 [MCP 快速入门](/zh-CN/mcp-quickstart)。

### 使用说明、Skills 和 Hooks 自定义

- **`CLAUDE.md`**：放在项目根目录的 markdown 文件，Claude Code 在每次会话开始时读取。用于设置编码标准、架构决策、首选库和审查清单。Claude 还会构建自动内存，跨会话保存构建命令和调试见解。
- **Skills**：创建如 `/review-pr` 或 `/deploy-staging` 的可重复工作流，团队可共享。
- **Hooks**：在 Claude Code 操作前/后运行 shell 命令，如每次编辑后自动格式化或提交前 lint。

### 运行代理团队

生成[多个 Claude Code 代理](/zh-CN/sub-agents)同时处理任务的不同部分。主导代理协调、分配子任务、合并结果。使用[后台代理](/zh-CN/agent-view)并行运行多个会话并从一个屏幕监控。[Agent SDK](/zh-CN/agent-sdk/overview) 让你构建自定义代理。

### CLI 管道、脚本和自动化

```bash
# 分析日志
tail -200 app.log | claude -p "Slack me if you see any anomalies"

# CI 自动化翻译
claude -p "translate new strings into French and raise a PR for review"

# 批量安全审查
git diff main --name-only | claude -p "review these changed files for security issues"
```

详见 [CLI 参考](/zh-CN/cli-reference)。

### 安排定期任务

- [Routines](/zh-CN/routines)：在 Anthropic 管理的基础设施上运行，即使计算机关闭。可从 Web、桌面应用或 CLI (`/schedule`) 创建。
- [桌面计划任务](/zh-CN/desktop-scheduled-tasks)：在本地机器上运行，直接访问本地文件和工具。
- [`/loop`](/zh-CN/scheduled-tasks)：在 CLI 会话中重复提示进行快速轮询。

### 从任何地方工作

在界面之间无缝切换：

- 使用[远程控制](/zh-CN/remote-control)从手机或浏览器继续
- 向 [Dispatch](/zh-CN/desktop#sessions-from-dispatch) 发送任务
- 在 [Web](/zh-CN/claude-code-on-the-web) 上启动，用 `claude --teleport` 拉入终端
- 使用 `/desktop` 将终端会话交给桌面应用
- 在 [Slack](/zh-CN/slack) 中 `@Claude` 路由错误到 PR

## 在任何地方使用 Claude Code

所有界面连接到相同的底层引擎，`CLAUDE.md`、设置和 MCP 服务器在所有界面中通用。

| 需求                                      | 最佳选项                                                                                                        |
| ----------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| 从手机/其他设备继续本地会话               | [远程控制](/zh-CN/remote-control)                                                                               |
| 从 Telegram / Discord / iMessage 推送事件 | [Channels](/zh-CN/channels)                                                                                     |
| 本地启动 → 移动设备继续                   | [Web](/zh-CN/claude-code-on-the-web) 或 [iOS 应用](https://apps.apple.com/app/claude-by-anthropic/id6473753684) |
| 按计划运行                                | [Routines](/zh-CN/routines) 或[桌面计划任务](/zh-CN/desktop-scheduled-tasks)                                    |
| 自动化 PR 审查和问题分类                  | [GitHub Actions](/zh-CN/github-actions) / [GitLab CI/CD](/zh-CN/gitlab-ci-cd)                                   |
| 每次 PR 自动代码审查                      | [GitHub Code Review](/zh-CN/code-review)                                                                        |
| Slack 错误报告 → PR                       | [Slack](/zh-CN/slack)                                                                                           |
| 调试实时 Web 应用                         | [Chrome](/zh-CN/chrome)                                                                                         |
| 构建自定义代理工作流                      | [Agent SDK](/zh-CN/agent-sdk/overview)                                                                          |

## 后续步骤

安装 Claude Code 后，推荐阅读：

- [快速入门](/zh-CN/quickstart)：从探索代码库到提交修复的完整流程
- [存储说明和内存](/zh-CN/memory)：使用 `CLAUDE.md` 和自动内存为 Claude 提供持久说明
- [常见工作流](/zh-CN/common-workflows) 和 [最佳实践](/zh-CN/best-practices)：充分利用 Claude Code 的模式
- [设置](/zh-CN/settings)：为工作流自定义 Claude Code
- [故障排除](/zh-CN/troubleshooting)：常见问题解决方案
- [code.claude.com](https://code.claude.com/)：演示、定价和产品详情
