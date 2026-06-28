---
title: 在 VS Code 中使用 Claude Code
date: 2026-06-28 12:00:00 +0800
categories: [AI, 工具]
tags: [claude, vscode, ai, extension]
description: >-
  安装和配置 VS Code 的 Claude Code 扩展，获得 AI 编码协助、内联差异、@-提及、计划审查和快捷键支持。
---

![VS Code Claude Code 扩展界面](https://mintcdn.com/claude-code/-YhHHmtSxwr7W8gy/images/vs-code-extension-interface.jpg?fit=max&auto=format&n=-YhHHmtSxwr7W8gy&q=85&s=300652d5678c63905e6b0ea9e50835f8)

VS Code 扩展为 Claude Code 提供了原生图形界面，直接集成到您的 IDE 中。这是在 VS Code 中使用 Claude Code 的推荐方式。

使用该扩展，您可以在接受 Claude 的计划之前审查和编辑它们、在进行编辑时自动接受、@-提及特定行范围的文件、访问对话历史记录，以及在单独选项卡或窗口中打开多个会话。

## 目录

- [前置条件](#前置条件)
- [安装扩展](#安装扩展)
- [开始使用](#开始使用)
- [使用提示框](#使用提示框)
- [引用文件和文件夹](#引用文件和文件夹)
- [恢复过去的对话](#恢复过去的对话)
- [检查账户和使用情况](#检查账户和使用情况)
- [自定义您的工作流](#自定义您的工作流)
- [管理 Plugins](#管理-plugins)
- [使用 Chrome 自动化浏览器任务](#使用-chrome-自动化浏览器任务)
- [VS Code 命令和快捷键](#vs-code-命令和快捷键)
- [从其他工具启动 VS Code 选项卡](#从其他工具启动-vs-code-选项卡)
- [配置设置](#配置设置)
- [VS Code 扩展与 Claude Code CLI](#vs-code-扩展与-claude-code-cli)
- [使用 Checkpoints 进行倒带](#使用-checkpoints-进行倒带)
- [在 VS Code 中运行 CLI](#在-vs-code-中运行-cli)
- [在扩展和 CLI 之间切换](#在扩展和-cli-之间切换)
- [在提示中包含终端输出](#在提示中包含终端输出)
- [使用 MCP 连接到外部工具](#使用-mcp-连接到外部工具)
- [使用 git](#使用-git)
- [使用第三方提供商](#使用第三方提供商)
- [安全和隐私](#安全和隐私)
- [常见故障修复](#常见故障修复)
- [卸载扩展](#卸载扩展)
- [后续步骤](#后续步骤)

## 前置条件

安装前，请确保您拥有：

- **VS Code 1.98.0 或更高版本**
- **Anthropic 账户**：任何付费 Claude 订阅（Pro、Max、Team 或 Enterprise）或 Claude Console 账户
- 如果通过第三方提供商（如 Amazon Bedrock 或 Google Vertex AI）访问 Claude，请参阅 [使用第三方提供商](#使用第三方提供商)

> **提示**：该扩展包含其自己的 CLI 副本用于聊天面板。要在 VS Code 的集成终端中运行 `claude`，仍然需要[独立 CLI 安装](/zh-CN/setup)。

## 安装扩展

点击以下链接直接安装扩展：

- [为 VS Code 安装](vscode:extension/anthropic.claude-code)
- [为 Cursor 安装](cursor:extension/anthropic.claude-code)

或者按以下步骤安装：

1. 打开扩展视图：`Cmd+Shift+X`（Mac）或 `Ctrl+Shift+X`（Windows/Linux）
2. 搜索 `Claude Code`
3. 点击**安装**

该扩展也可以安装到 VS Code 的其他分支，如 Devin Desktop 或 Kiro。无法安装时，请[安装 CLI](/zh-CN/quickstart) 并在终端中运行 `claude`。

> **注意**：如果安装后扩展没有出现，请重启 VS Code 或从命令面板运行 `Developer: Reload Window`。

## 开始使用

### 打开 Claude Code 面板

VS Code 中的 Spark 图标表示 Claude Code：

![Spark 图标](https://mintcdn.com/claude-code/c5r9_6tjPMzFdDDT/images/vs-code-spark-icon.svg?fit=max&auto=format&n=c5r9_6tjPMzFdDDT&q=85&s=3ca45e00deadec8c8f4b4f807da94505)

打开 Claude 的常用方式：

- **编辑器工具栏**：编辑器右上角的 Spark 图标（仅在打开文件时显示）
- **活动栏**：左侧边栏中的 Spark 图标，打开会话列表
- **命令面板**：`Cmd+Shift+P` / `Ctrl+Shift+P`，输入 `Claude Code`
- **状态栏**：右下角的 `✱ Claude Code`

您可以将 Claude 面板拖动到次级边栏、主边栏或编辑器区域。

### 登录

首次打开面板时，会出现登录屏幕。点击**登录**并在浏览器中完成授权。

如果看到"未登录 · 请运行 /login"，请从命令面板运行 `Developer: Reload Window`。如果您的 shell 设置了 `ANTHROPIC_API_KEY`，但 VS Code 未继承该环境变量，请使用 `code .` 从终端启动 VS Code。

登录后，会出现**学习 Claude Code**检查清单。点击**显示给我**逐项完成，或用 X 关闭它。要稍后重新打开，请在扩展设置中取消选中**隐藏入门**。

### 发送提示

要求 Claude 帮助您处理代码或文件：解释、调试或更改。

> **提示**：Claude 会自动看到您选择的文本。按 `Option+K`（Mac）/`Alt+K`（Windows/Linux）可插入 @-提及引用，例如 `@file.ts#5-10`。

### 审查更改

当 Claude 提出编辑时，它会显示原始内容与建议更改并排比较，并请求许可。您可以：

- 接受更改
- 拒绝更改
- 在差异视图中编辑建议内容

如果您在接受前修改建议内容，Claude 会知道它已更改，不会假设内容与原始提案匹配。

有关更多工作流，请参阅[常见工作流](/zh-CN/common-workflows)。

> **提示**：从命令面板运行 `Claude Code: Open Walkthrough` 可获得基础功能的引导式教程。

## 使用提示框

提示框支持多项功能：

- **权限模式**：正常模式会在每次操作前请求许可；Plan mode 先描述计划再等待批准；自动接受模式直接应用更改。点击提示框底部的模式指示器切换。
- **命令菜单**：输入 `/` 打开命令菜单，访问附加文件、切换模型、切换扩展思考、查看用量、启动 Remote Control 等。
- **上下文指示器**：显示当前使用的 context window 大小。Claude 在需要时自动压缩上下文，或手动运行 `/compact`。
- **扩展思考**：让 Claude 对复杂问题进行更深入推理。在命令菜单中切换后，推理会以折叠块显示。
- **多行输入**：按 `Shift+Enter` 换行而不发送消息。

## 引用文件和文件夹

使用 @-提及为 Claude 提供文件或文件夹上下文：

```text
> Explain the logic in @auth
> What's in @src/components/
```

- 对于大型 PDF，可请求特定页面范围
- 选中的代码会自动可见，提示框页脚显示选中行数
- 按 `Option+K`（Mac）/`Alt+K`（Windows/Linux）插入当前文件和选择行的 @-提及引用
- 将文件拖到提示框时按住 `Shift` 将其作为附件添加

## 恢复过去的对话

点击 Claude Code 面板顶部的**会话历史**按钮：

- 按关键字搜索或按日期浏览（今天、昨天、过去 7 天等）
- 点击会话以完整消息历史恢复
- 新会话基于第一条消息生成 AI 标题
- 悬停会话可重命名或删除

### 从 Claude.ai 恢复远程会话

1. 打开会话历史
2. 选择**远程**选项卡
3. 选择要恢复的会话

> 只有使用 GitHub 存储库启动的远程会话才会出现在远程选项卡中。恢复后更改不会同步回 claude.ai。

## 检查账户和使用情况

运行 `/usage` 打开账户和使用情况对话框，显示：

- 登录账户、计划及使用情况条形图
- 当前会话和周的使用量、限制重置时间
- 高用量行为标记（缓存未命中、长上下文等）

需要 Claude Code v2.1.174 或更高版本。

## 自定义您的工作流

### 选择 Claude 的位置

将 Claude 面板拖动到：

- **次级边栏**：窗口右侧，编码时保持 Claude 可见
- **主边栏**：左侧边栏
- **编辑器区域**：作为选项卡并排显示

### 运行多个对话

使用"在新选项卡中打开"或"在新窗口中打开"启动其他对话。Spark 图标的彩色点表示状态：

- 蓝色：权限请求待处理
- 橙色：Claude 在隐藏选项卡中完成

### 切换到终端模式

打开 VS Code 设置（`Cmd+,` / `Ctrl+,`），转到扩展 → Claude Code，勾选**使用终端**。

## 管理 Plugins

在提示框中输入 `/plugins` 打开管理界面。

### 安装 Plugins

插件对话框有两个选项卡：**Plugins**（已安装和可用）和 **Marketplaces**（插件源）。

安装范围选择：
- **为您安装**：用户范围
- **为此项目安装**：项目范围
- **本地安装**：仅当前仓库

### 管理 Marketplaces

- 输入 GitHub 仓库、URL 或本地路径添加 marketplace
- 点击刷新更新列表，点击垃圾桶删除

更改后需重启 Claude Code 以应用更新。

## 使用 Chrome 自动化浏览器任务

需要 [Claude in Chrome 扩展](https://chromewebstore.google.com/detail/claude/fcoeoabgfenejglbffodgkkbkcdhcgfn) v1.0.36+。

```text
@browser go to localhost:3000 and check the console for errors
```

Claude 会打开新标签页并共享浏览器登录状态。

## VS Code 命令和快捷键

| 命令                 | 快捷键                             | 描述                           |
| -------------------- | ---------------------------------- | ------------------------------ |
| 焦点输入             | `Cmd+Esc` / `Ctrl+Esc`             | 在编辑器和 Claude 之间切换焦点 |
| 在新选项卡中打开     | `Cmd+Shift+Esc` / `Ctrl+Shift+Esc` | 将新会话作为编辑器选项卡打开   |
| 新对话               | `Cmd+N` / `Ctrl+N`                 | 开始新对话（需启用快捷键）     |
| 重新打开已关闭的会话 | `Cmd+Shift+T` / `Ctrl+Shift+T`     | 恢复最近关闭的 Claude 会话     |
| 插入 @-提及引用      | `Option+K` / `Alt+K`               | 插入当前文件和选择的引用       |
| 显示日志             | -                                  | 打开扩展调试日志               |
| 登出                 | -                                  | 登出 Anthropic 账户            |

> **注意**：macOS Tahoe+ 上 `Cmd+Esc` 可能被系统拦截，请在键盘设置中清除游戏覆盖快捷键或重新绑定。

## 从其他工具启动 VS Code 选项卡

扩展注册了 `vscode://anthropic.claude-code/open` URI 处理程序：

**Mac：**
```bash
open "vscode://anthropic.claude-code/open"
```

**Windows（PowerShell）：**
```powershell
Start-Process "vscode://anthropic.claude-code/open"
```

**Linux：**
```bash
xdg-open "vscode://anthropic.claude-code/open"
```

可选查询参数：

| 参数      | 描述                          |
| --------- | ----------------------------- |
| `prompt`  | 预填充提示文本（需 URL 编码） |
| `session` | 恢复指定会话 ID               |

## 配置设置

### 扩展设置

| 设置                                | 默认值    | 描述                     |
| ----------------------------------- | --------- | ------------------------ |
| `useTerminal`                       | `false`   | 以终端模式启动 Claude    |
| `initialPermissionMode`             | `default` | 新对话默认权限模式       |
| `preferredLocation`                 | `panel`   | Claude 打开位置          |
| `autosave`                          | `true`    | 读取/写入前自动保存文件  |
| `useCtrlEnterToSend`                | `false`   | 使用 Ctrl/Cmd+Enter 发送 |
| `enableNewConversationShortcut`     | `false`   | 启用新对话快捷键         |
| `enableReopenClosedSessionShortcut` | `true`    | 重新打开已关闭会话       |
| `respectGitIgnore`                  | `true`    | 排除 .gitignore 模式     |
| `usePythonEnvironment`              | `true`    | 激活 Python 环境         |
| `disableLoginPrompt`                | `false`   | 跳过登录提示             |

## VS Code 扩展与 Claude Code CLI

| 功能            | CLI  | VS Code 扩展 |
| --------------- | ---- | ------------ |
| 命令和 skills   | 全部 | 子集         |
| MCP server 配置 | 是   | 部分         |
| Checkpoints     | 是   | 是           |
| `!` bash 快捷键 | 是   | 否           |
| Tab 完成        | 是   | 否           |

### 使用 Checkpoints 进行倒带

悬停消息显示倒带按钮：

- 从此处分叉对话
- 将代码倒带到此处
- 分叉对话并倒带代码

### 在 VS Code 中运行 CLI

打开集成终端（`` Ctrl+` `` / `` Cmd+` ``）并运行 `claude`。独立 CLI 安装不会随扩展自动完成。若未找到，请[验证 PATH](/zh-CN/troubleshoot-install#verify-your-path)。

### 在扩展和 CLI 之间切换

```bash
claude --resume
```

选择要恢复的对话即可在 CLI 中继续扩展会话。

### 在提示中包含终端输出

使用 `@terminal:name` 引用终端输出，让 Claude 看到命令输出和日志而无需复制粘贴。

## 使用 MCP 连接到外部工具

添加 MCP server：

```bash
claude mcp add --transport http github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer YOUR_GITHUB_PAT"
```

或在聊天面板中输入 `/mcp` 管理服务器。

## 使用 Git

### 创建提交和拉取请求

```text
> commit my changes with a descriptive message
> create a pr for this feature
> summarize the changes I've made to the auth module
```

### 使用 Git Worktrees

```bash
claude --worktree feature-auth
```

每个 worktree 拥有独立工作目录，共享 git 历史记录。

## 使用第三方提供商

如果使用 Amazon Bedrock、Google Vertex AI 或 Microsoft Foundry：

1. 在扩展设置中勾选 `disableLoginPrompt`
2. 按照提供商指南在 `~/.claude/settings.json` 中配置

## 安全和隐私

Claude Code 处理代码以提供协助，但不用于训练模型。在不受信任的代码库中：

- 启用 VS Code 受限模式
- 使用手动批准模式
- 在接受更改前仔细审查

### 内置 IDE MCP Server

扩展运行本地 MCP server，绑定到 `127.0.0.1` 的随机端口，使用随机认证令牌。可从工具列表中过滤，仅暴露诊断获取和 Jupyter 代码执行给模型。

## 常见故障修复

### 扩展无法安装

- 确保 VS Code 1.98.0+
- 检查扩展安装权限
- 从 [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=anthropic.claude-code) 直接安装

### Spark 图标不可见

1. 打开文件（图标仅在打开文件时显示）
2. 检查 VS Code 版本
3. 运行 `Developer: Reload Window`
4. 禁用冲突扩展
5. 检查工作区信任状态

### macOS 上 Cmd+Esc 无效

打开系统设置 → 键盘 → 键盘快捷键 → 游戏控制器，清除游戏覆盖快捷键。或在 VS Code 中重新绑定 `Claude Code: Focus input`。

### Claude Code 不响应

1. 检查网络连接
2. 开始新对话
3. 在终端中运行 `claude` 获取详细错误

## 卸载扩展

1. 在扩展视图中搜索 `Claude Code`，点击**卸载**

要删除扩展数据：

**macOS：**
```bash
rm -rf ~/Library/"Application Support"/Code/User/globalStorage/anthropic.claude-code
```

**Linux：**
```bash
rm -rf ~/.config/Code/User/globalStorage/anthropic.claude-code
```

**Windows：**
```powershell
Remove-Item -Recurse -Force "$env:APPDATA\Code\User\globalStorage\anthropic.claude-code"
```

## 后续步骤

- [探索常见工作流](/zh-CN/common-workflows)
- [设置 MCP servers](/zh-CN/mcp)
- [配置 Claude Code 设置](/zh-CN/settings)
