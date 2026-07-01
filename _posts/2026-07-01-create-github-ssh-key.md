---
title: 配置 GitHub SSH Key（支持多账号）
date: 2026-07-01 12:00:00 +0800
categories: [工具]
tags: [github, ssh, git, multi-account]
description: >-
  一步步配置 GitHub SSH Key，支持在同一台电脑上管理多个 GitHub 账号。
---

配置 GitHub SSH Key 主要分三步：**生成密钥**、**添加公钥到 GitHub**、**测试连接**。整个过程约 5 分钟即可完成。

## 1. 生成 SSH 密钥

打开终端（Windows 用户可使用 Git Bash 或 PowerShell），执行以下命令：

```bash
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_github_name
```

> **参数说明**：
> - `-t ed25519`：使用 Ed25519 算法（更安全、更快）。如果系统不支持，可改用 `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`。
> - `-C`：添加注释，建议使用你的 GitHub 注册邮箱。
> - `-f`：指定密钥文件名。将 `id_github_name` 替换为你的 GitHub 账号名，例如 `https://github.com/john-shao` 对应 `~/.ssh/id_john_shao`。
{: .prompt-info }

执行后按提示操作：
1. **选择保存位置**：直接按 `Enter` 键，使用 `-f` 指定的路径。
2. **设置密码（可选）**：如需额外保护私钥，可输入密码；否则直接按两次 `Enter` 键跳过。

## 2. 将公钥添加到 GitHub

### 2.1 复制公钥

公钥文件位于 `~/.ssh/id_github_name.pub`，执行以下命令查看并复制输出内容：

```bash
cat ~/.ssh/id_github_name.pub
```

### 2.2 在 GitHub 中添加

登录 GitHub 网页，按以下步骤操作：

1. 点击右上角头像，选择 **Settings**（设置）
2. 在左侧菜单中，点击 **SSH and GPG keys**
3. 点击 **New SSH key** 按钮
4. 在 **Title** 栏输入便于识别的名称（如 "My Laptop"）
5. 在 **Key** 栏粘贴刚刚复制的公钥内容
6. 点击 **Add SSH key** 保存

> **提示**：可以为同一台机器添加多个 SSH Key，分别对应不同的 GitHub 账号。
{: .prompt-tip }

## 3. 测试 SSH 连接

配置完成后，在终端中执行以下命令测试连接：

```bash
ssh -T git@github.com
```

如果配置成功，会看到类似以下欢迎信息：

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

## 4. 多账号配置（可选）

以上配置方式天然支持在同一台电脑上管理**多个 GitHub 账号**（如个人和工作账号）。只需为每个账号生成不同的密钥即可，命名示例：

| 账号类型 | 密钥文件名示例        |
| :------- | :-------------------- |
| 个人账号 | `~/.ssh/id_john_shao` |
| 工作账号 | `~/.ssh/id_work_name` |
