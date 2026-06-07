---
title: Claude Code CLI 速查表：配置、命令、提示词与最佳实践
date: 2026-06-07 12:00:00 +0800
categories: [AI, 工具]
tags: [claude, ai, cli, 速查表]
description: >-
  用这份速查表玩转 Anthropic 的 Claude Code，涵盖配置、CLI 命令、高级特性以及加快开发和测试的最佳实践。
---

用这份速查表玩转 Anthropic 的 Claude Code，涵盖配置、CLI 命令、高级特性以及加快开发和测试的最佳实践。

> 作者：Shipyard 团队 | 原文发布日期：2026 年 4 月 24 日

Claude Code 是 Anthropic 推出的终端智能编程工具，目前是编码领域的 SOTA（最先进水平）。这份速查表将为你提供安装、配置和使用 Claude Code 所需的一切。

## 开始使用 Claude Code

如果你是 Claude Code 的新手，建议先阅读[这份入门指南](https://shipyard.build/blog/claude-code-getting-started/)。一旦你拥有了 Claude Pro 或 Max 订阅（或付费购买 API 访问权限），就可以开始在终端或[网页版](https://claude.ai/code)使用 Claude Code 了。

> **建议**：如果你打算持续且适度地使用，建议选择订阅方案。如果你不想处理令牌刷新窗口的问题，获取 API 密钥会更合适。

### 安装

全局安装：

```bash
npm install -g @anthropic-ai/claude-code
```

**前置要求**：Node.js 18 或更高版本

**诊断问题**：

```bash
claude doctor
```

### 身份验证

在启动 Claude Code 之前，请先设置好你的 Anthropic API 密钥。

**获取密钥**：从 [Anthropic Console](https://console.anthropic.com/) 获取 API 密钥。

**设置密钥**：设置 `ANTHROPIC_API_KEY` 环境变量：

```bash
export ANTHROPIC_API_KEY="你的_ANTHROPIC_API_密钥"
```

或者，如果你拥有 Pro 或 Max 套餐，可以通过浏览器进行身份验证。

将上述命令添加到你的 Shell 配置文件（如 `~/.bashrc`、`~/.zshrc`）中，以便在会话间持久化保存。

### 基本用法

**交互模式（REPL）**：启动一个对话式编码会话。

```bash
claude
```

**带初始提示词的 REPL**：以特定问题开始会话。

```bash
claude "解释这个项目"
```

**打印模式**：单次查询后退出（非常适合脚本编写）。

```bash
claude -p "解释这个函数"
```

**管道输入内容**：处理管道传入的内容。

```bash
cat logs.txt | claude -p "解释这些错误"
```

**继续最近的对话**：

```bash
claude -c
```

**恢复特定会话**：

```bash
claude -r "会话ID" "继续完善这个功能"
```

### IDE 扩展

Claude Code 也可作为扩展用于 [VS Code](https://code.visualstudio.com/) 和 [JetBrains](https://www.jetbrains.com/) IDE。安装后，你将在编辑器内获得一个 Claude Code 面板。

- **VS Code**：在扩展市场中搜索"Claude Code"
- **JetBrains**：在插件市场中搜索"Claude Code"

## 配置

你可以编写（或生成）配置文件来定义 Claude Code 的基本行为。

### 模型

Claude Code 方案均可让你访问最新三款 Claude 模型：Sonnet 4.6、Haiku 4.5 和 Opus 4.6。

**快速概览**：

- **Sonnet 4.6**：如果你使用的是 Pro 或 Max5 套餐，这是最佳的默认模型，对大多数直接提示词都能给出出色结果。
- **Haiku 4.5**：用于简单任务以节省令牌（并获得更快结果）。
- **Opus 4.6**：SOTA 模型，在 Pro 或 Max5 套餐下谨慎使用，适用于多步骤规划或复杂编码任务；Max20 套餐下可作为默认模型使用。

要切换到上述三款模型之一，可以使用 `/model` 斜杠命令。要使用不同的 Claude 模型，可以通过标志位指定模型字符串：

```bash
claude --model claude-haiku-4-5-20251001
```

### 配置文件

Claude Code 使用存储在 JSON 文件中的分层设置：

- **用户设置**：`~/.claude/settings.json`（适用于所有项目）
- **项目设置**：`.claude/settings.json`（与团队共享，提交到 Git）
- **本地项目设置**：`.claude/settings.local.json`（个人设置，被 Git 忽略）

**settings.json 示例**：

```json
{
  "model": "claude-sonnet-4-6",
  "maxTokens": 4096,
  "permissions": {
    "allowedTools": ["Read", "Write", "Bash(git *)"],
    "deny": [
      "Read(./.env)",
      "Read(./.env.*)",
      "Write(./production.config.*)"
    ]
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write(*.py)",
        "hooks": [
          {
            "type": "command",
            "command": "python -m black $file"
          }
        ]
      }
    ]
  }
}
```

### 记忆文件（CLAUDE.md）

使用 `CLAUDE.md` 文件为 Claude 提供上下文和指令。它们可以节省时间和令牌，对于本应放入提示词中的信息非常有帮助。

这些文件分层加载：

- **全局**：`~/.claude/CLAUDE.md`（适用于所有项目）
- **项目根目录**：`./CLAUDE.md`（项目范围上下文）
- **子目录**：特定组件的指令

**CLAUDE.md 示例**：

```markdown
# 项目上下文

## 编码规范
- 所有新代码使用 TypeScript
- 遵循现有的 ESLint 配置
- 为所有新函数编写测试（使用 Jest）
- 在 React 中使用函数组件和 Hooks

## 架构
- 前端：Next.js with TypeScript
- 后端：Node.js with Express
- 数据库：PostgreSQL with Prisma
- 状态管理：Zustand 管理客户端状态

## 文件组织
- 组件放在 `src/components/`
- 工具函数放在 `src/utils/`
- 测试文件与源文件放在一起，扩展名为 `.test.ts`
```

## CLI 命令与标志位

你可以在 Claude 会话之外使用以下 Shell 命令。

### 核心命令

| 命令                    | 描述                       | 示例                           |
| :---------------------- | :------------------------- | :----------------------------- |
| `claude`                | 启动交互式 REPL            | `claude`                       |
| `claude "查询"`         | 使用初始提示词启动 REPL    | `claude "解释这个项目"`        |
| `claude -p "查询"`      | 通过打印模式查询，然后退出 | `claude -p "审查这段代码"`     |
| `claude -c`             | 继续最近的对话             | `claude -c`                    |
| `claude -c -p "查询"`   | 在打印模式下继续对话       | `claude -c -p "运行测试"`      |
| `claude -r "ID" "查询"` | 通过会话 ID 恢复会话       | `claude -r "abc123" "完成 PR"` |
| `claude update`         | 更新到最新版本             | `claude update`                |
| `claude mcp`            | 配置 MCP 服务器            | `claude mcp add 服务器名称`    |

### CLI 标志位

| 标志位                           | 描述                                  | 示例                                          |
| :------------------------------- | :------------------------------------ | :-------------------------------------------- |
| `--add-dir`                      | 添加额外的工作目录                    | `claude --add-dir ../apps ../lib`             |
| `--allowedTools`                 | 无需提示即允许特定工具                | `claude --allowedTools "Write" "Bash(git *)"` |
| `--disallowedTools`              | 阻止特定工具                          | `claude --disallowedTools "Bash(rm *)"`       |
| `--model`                        | 使用特定的 Claude 模型                | `claude --model claude-opus-4-6`              |
| `--max-turns`                    | 限制对话轮次                          | `claude -p --max-turns 3 "查询"`              |
| `--output-format`                | 设置输出格式（text/json/stream-json） | `claude -p --output-format json "查询"`       |
| `--input-format`                 | 设置输入格式                          | `claude -p --input-format stream-json`        |
| `--verbose`                      | 启用详细日志记录                      | `claude --verbose`                            |
| `--continue`                     | 继续最近的对话                        | `claude --continue`                           |
| `--resume`                       | 恢复特定会话                          | `claude --resume abc123`                      |
| `--dangerously-skip-permissions` | 跳过所有权限提示（请谨慎使用）        | `claude --dangerously-skip-permissions`       |

## 交互式会话命令

你可以在 Claude Code 会话期间使用以下斜杠命令。

### 内置斜杠命令

| 命令                  | 描述                                            |
| :-------------------- | :---------------------------------------------- |
| `/help`               | 显示所有命令及自定义斜杠命令                    |
| `/config`             | 交互式配置 Claude Code 设置                     |
| `/model`              | 切换当前会话的模型                              |
| `/allowed-tools`      | 交互式配置工具权限                              |
| `/hooks`              | 配置钩子                                        |
| `/mcp`                | 管理 MCP 服务器                                 |
| `/agents`             | 管理子代理（创建、编辑、列表）                  |
| `/fast`               | 切换快速输出模式                                |
| `/compact`            | 手动压缩对话上下文                              |
| `/clear`              | 清除对话历史                                    |
| `/doctor`             | 诊断 Claude Code 相关问题                       |
| `/ide`                | 连接到 IDE 扩展                                 |
| `/vim`                | 启用 Vim 风格编辑模式                           |
| `/terminal-setup`     | 安装终端快捷键（iTerm2/VS Code 的 Shift+Enter） |
| `/install-github-app` | 设置 GitHub Actions 集成                        |

> **注意**：`/help` 命令会显示所有可用的斜杠命令，包括来自 `.claude/commands/` 和 `~/.claude/commands/` 目录的自定义命令，以及从连接的 MCP 服务器获得的任何命令。

### 文件和目录引用（@）

你可以在提示词中引用文件或目录。（如果你不知道确切的文件名/位置，Claude Code 可以使用 grep 查找。）

**单个文件**：

> 审查这个组件的可访问性问题。 @./src/components/Button.tsx

**目录（递归）**：

> 为所有 API 路由添加全面的错误处理。 @./src/api/

**多个文件**：

> 比较这两个实现。 @./src/old.js @./src/new.js

**Glob 模式**：

> 审查所有测试文件的完整性。 @./src/**/*.test.ts

### Shell 命令（!）

你可以直接在 Claude 会话中运行 Shell 命令。

**单个命令**：

> !npm test

**Shell 模式切换**：

> ! # 现在处于 Shell 模式。再次输入 ! 即可退出。

## 高级特性

我们非常欣赏 Claude Code 的高度可定制性，通过自定义命令、钩子、MCP 和存储提示词等特性，可以轻松扩展它。

### 自定义斜杠命令

你可以创建自己的 Claude Code 斜杠命令。这是一个调用常用提示词的"快捷方式"。同样，上下文越丰富越好（但也要保持抽象，以便广泛适用）。

在 Markdown 文件中定义它们：

**项目命令（`.claude/commands/`）**：

```bash
# 创建项目特定命令
mkdir -p .claude/commands
echo "分析这段代码的性能问题并提出优化建议：" > .claude/commands/optimize.md
```

**个人命令（`~/.claude/commands/`）**：

```bash
# 为所有项目创建个人命令
mkdir -p ~/.claude/commands
echo "审查这段代码的安全漏洞：" > ~/.claude/commands/security.md
```

**带参数的命令**：

```bash
# 创建参数化命令
echo '修复问题 #$ARGUMENTS，遵循我们的编码规范' > .claude/commands/fix-issue.md

# 在 Claude 会话中使用该命令
> /fix-issue 123
```

**带上下文的高级命令**：

```markdown
---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git commit:*)
description: 创建带有上下文的 git 提交
---

## 上下文
- 当前状态：!`git status`
- 当前差异：!`git diff HEAD`
- 当前分支：!`git branch --show-current`

请根据上述更改创建一个有意义的提交消息。
```

### 自动化钩子（Hooks）

钩子可以在特定提示/事件后自动运行 Shell 命令。

**示例：自动格式化 Python 文件**

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write(*.py)",
        "hooks": [
          {
            "type": "command",
            "command": "python -m black \"$file\""
          }
        ]
      }
    ]
  }
}
```

**钩子事件**：

- `PreToolUse`：工具执行前（可以阻止）
- `PostToolUse`：工具执行后
- `UserPromptSubmit`：处理用户输入前
- `Notification`：当 Claude 发送通知时
- `Stop`：会话结束时

### 模型上下文协议（MCP）

你可以通过添加 MCP 服务器来扩展 Claude Code 的功能。

**添加 MCP 服务器**：

```bash
claude mcp add my-server -e API_KEY=123 -- /path/to/server arg1 arg2
```

> 请查阅 MCP 工具的文档以获取正确的语法。

默认情况下，MCP 服务器以项目范围添加。使用 `--scope` 来控制配置的保存位置：

- `--scope project`：与团队共享，提交到 Git — `.claude/settings.json`（默认）
- `--scope local`：个人配置，不共享 — `.claude/settings.local.json`
- `--scope global`：适用于所有项目 — `~/.claude/settings.json`

**常见的 MCP 用例**：

- 连接到 Google Drive 获取设计文档
- 与 Jira 集成进行工单管理
- 添加自定义开发工具
- 访问外部数据库

### 技能（Skills）

技能是基于 Markdown 的指南，教 Claude Code 如何处理特定任务。与斜杠命令不同，技能通过自然语言调用，由 Claude 决定何时使用它们。

**创建技能**：

```bash
# 在项目中添加一个空白技能
mkdir -p .claude/skills/new-skill
```

在该目录中创建一个 `SKILL.md` 文件：

```markdown
---
name: add-numbers
description: 从自然语言输入中添加数字
---

# 数字相加技能

当用户要求你添加、求和或合计数字时：

1. 从提示词中提取所有数值
2. 使用 `add.py` 计算总和
3. 返回结果并附上简要说明

示例："add 15, 27, and 8" → "总和是 50 (15 + 27 + 8)"
```

Claude 将自动参考技能的指令、脚本和模板。查看 [Anthropic 的官方技能库](https://github.com/anthropics/skills)，其中包含用于处理 pdf、docx、pptx、xlsx 等文件的技能。

### 子代理（Subagents）

子代理是具有自己上下文窗口和角色设定的专用 Claude 实例。它们用于特定领域的任务（代码审查、调试、架构设计），以获得更好结果并节省令牌。

**创建子代理**：

> /agents # 按照提示定义名称、描述、模型和角色

**子代理配置（`.claude/agents/reviewer.md`）**：

```markdown
---
name: reviewer
description: 用于全面代码审查
model: sonnet
color: orange
---

你是一位专业的代码审查专家。专注于安全性、性能和可维护性。
```

当任务与描述匹配时，Claude 将调用子代理。

### 自动模式

自动模式让 Claude Code 自主运行。它适合长时间任务，你可以放手不管，稍后再回来查看结果。你可以通过以下方式启用它：

```bash
claude --permission-mode auto
```

或者在 Claude Code 配置文件中进行设置：

```json
{
  "permissions": {
    "defaultMode": "auto"
  }
}
```

### 扩展思维

扩展思维默认启用。它让 Claude 在编写代码/响应之前先推理复杂问题。

**切换思维**：

- 会话期间：`Option+T`（macOS）或 `Alt+T`（Windows/Linux）
- 设置默认值：使用 `/config` 进行全局切换（保存到 `~/.claude/settings.json`）

**查看思维输出**：按 `Ctrl+O` 启用详细模式，以便查看 Claude 的推理过程。

**限制令牌预算**：

```bash
export MAX_THINKING_TOKENS=10000
```

> **注意**：思维令牌是收费的。像"再努力思考一下"这样的提示词并不会分配额外的令牌，所以请使用切换开关。

### 对话管理

**继续最近的工作**：

```bash
claude --continue
claude --continue --print "显示我们的进度"
```

**恢复特定会话**：

```bash
claude --resume        # 显示选择器
claude --resume session-id
```

**保存和恢复上下文**：所有 Claude Code 对话都会自动保存，包含完整的消息历史和工具状态。

## 常用工作流

以下是 Claude Code 可以帮助完成的一些不同任务。记住，上下文越丰富越好，因此如果你能提供具体细节，Claude 会给出更好的结果（并且你需要纠正的地方也更少）。

### 代码分析

> 分析此代码库结构并提出改进建议。 @./src/

### 功能开发

> 使用 JWT 令牌和密码哈希实现用户身份验证系统。

### 错误修复

> 调试此错误："TypeError: Cannot read property 'id' of undefined" @./src/user-service.js

### 代码审查

> 审查此拉取请求中的潜在问题、性能问题以及是否符合我们的编码规范。 @./src/

### 测试

> 为此工具模块生成全面的单元测试。 @./src/utils/validation.js

### 重构

> 重构此类以使用依赖注入并使其更易于测试。 @./src/services/EmailService.js

### 文档

> 生成此目录中所有端点的 API 文档。 @./src/routes/

### CI/CD 集成

> # 在 GitHub Actions 或其他 CI 中
> claude -p "如果有任何 lint 错误，请修复它们并建议一条提交消息"

## 安全与权限

Claude Code 默认会对其执行的每个操作请求权限。如果你信任它对某种类型的操作（例如获取链接、读取文件），可以授予更广泛的权限。大多数开发者会单独批准操作。

### 权限系统

Claude Code 允许你根据需要授予权限：

**工具权限**：

- `Read`：文件读取操作
- `Write`：文件写入/修改
- `Bash`：Shell 命令执行
- `MCP tools`：外部集成

**配置示例**：

```json
{
  "permissions": {
    "allowedTools": [
      "Read",
      "Write(src/**)",
      "Bash(git *)",
      "Bash(npm *)"
    ],
    "deny": [
      "Read(.env*)",
      "Write(production.config.*)",
      "Bash(rm *)",
      "Bash(sudo *)"
    ]
  }
}
```

## 最佳实践

与开发中的任何事物一样，请注意你授予的权限，并关注正在运行的 Shell 命令。此外：

- **始终**在接受前审查更改
- 使用 `.claude/settings.local.json` 存储个人/敏感设置
- 为你的环境配置工具权限；验证所有内容（除非已设置适当的保护措施，否则不要使用 YOLO 模式）
- 使用钩子进行自动代码格式化/验证
- 将敏感数据保存在 `.env` 文件中；禁止 Claude Code 读取这些文件

## 理解 Claude 的会话模型

如果你想更好地规划 Claude 会话，你需要了解自己的限制。Claude 的令牌根据套餐由整体服务器负载决定，因此在繁忙时段你获得的令牌会更少。如果你不使用即用即付的 API 访问方式，请选择最适合你的 Claude 套餐。

- **Pro**：适合中高编码工作量。预期用于持续进行较小的代码更改，并作为你自己编码的补充。有限的 Opus 访问权限。**每月 20 美元**
- **Max5**：适合高强度编码工作量。令牌配额是 Pro 的 5 倍。包含 Opus 访问权限。**每月 100 美元**
- **Max20**：适合近乎自主、持续的高负载开发工作量，支持多个会话/代理。显著更大的上下文窗口。令牌配额是 Pro 的 20 倍。包含 Opus 访问权限。**每月 200 美元**

会话在你发送第一条消息时开始，持续五个小时。如果你使用 Opus，令牌消耗会快得多。为不同任务启动不同会话是最节省令牌（且能保证更好输出）的方式。Claude Code 会在达到限制前自动压缩对话上下文，因此长时间会话不会突然停止。你也可以在会话中通过 `/compact` 手动触发压缩。

## 额外资源

- Claude 内置了对自身文档的访问权限：你可以直接询问关于功能的问题
- 查看[官方文档](https://docs.anthropic.com/en/docs/claude-code)
- 在你的 Claude 会话中使用 `/help`
- 通过这些[配方（recipes）](https://github.com/sgharlow/claude-code-recipes?tab=readme-ov-file)扩展 Claude Code

## 原文链接

[Claude Code 速查表](https://shipyard.build/blog/claude-code-cheat-sheet/)
