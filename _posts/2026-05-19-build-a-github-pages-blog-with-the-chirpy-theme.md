---
title: 用 Chirpy 主题搭建 GitHub Pages 博客
date: 2026-05-19 13:30:18 +0800
categories: [博客]
tags: [github, pages]
---

需要先提醒一点：你给的链接 `cotes2020/jekyll-theme-chirpy` 是**主题的完整源码仓库**，官方文档其实**不推荐**直接 fork 它来搭站，除非你想深度定制源码。官方推荐用配套的 **Chirpy Starter** 仓库（`cotes2020/chirpy-starter`），它精简、便于后续升级，几乎可以零配置上线。下面按推荐路径讲。

## 一、用 Starter 模板创建仓库

1. 登录 GitHub，打开 <https://github.com/cotes2020/chirpy-starter>
2. 点右上角绿色按钮 **Use this template → Create a new repository**
3. 仓库名必须是 `<你的GitHub用户名>.github.io`（全部小写），例如 `zhangsan.github.io`。这是 User Pages 的固定命名规则，GitHub 会自动识别并托管。
4. 设为 **Public**（私有仓库要 Pro 才能开 Pages）。

## 二、开启 GitHub Pages

进入新建的仓库 → **Settings → Pages**：

- **Source** 选择 **GitHub Actions**（不是传统的 "Deploy from a branch"）

Starter 模板自带了 `.github/workflows/pages-deploy.yml`，第一次 push 后会自动跑构建流程，几分钟后访问 `https://<用户名>.github.io` 就能看到默认站点。

## 三、改基础配置

编辑根目录 `_config.yml`，至少改这几项：

```yaml
lang: zh-CN              # 中文界面
timezone: Asia/Shanghai
title: 你的博客名
tagline: 副标题
url: "https://<用户名>.github.io"
github:
  username: <你的GitHub用户名>
social:
  name: 你的名字
  email: you@example.com
avatar: /assets/img/avatar.png   # 头像放到对应路径即可
```

每次 push 到 `main` 分支，Actions 会自动重新构建部署。

## 四、写文章

在 `_posts/` 目录下新建 Markdown 文件，文件名格式必须是 `YYYY-MM-DD-标题.md`，文件头加 front matter：

```markdown
---
title: 我的第一篇文章
date: 2026-05-19 10:00:00 +0800
categories: [生活, 随笔]
tags: [hello]
---

正文内容……
```

## 五、本地预览（可选但推荐）

如果只想在线编辑，可以跳过这步。要本地预览得装 Ruby 环境：

```bash
git clone https://github.com/<用户名>/<用户名>.github.io.git
cd <用户名>.github.io
bundle install
bundle exec jekyll s
```

浏览器打开 `http://localhost:4000` 就能看到效果。Windows 用户建议用 WSL，原生环境装 Jekyll 比较折腾。

## 几个常见坑

- 别直接 fork `jekyll-theme-chirpy`，那是给想改主题源码的开发者用的，普通用户用 starter 升级方便得多（直接更新 `Gemfile` 里的版本号就行）。
- Pages 的 Source 一定要选 **GitHub Actions**，选成分支会构建失败，因为 Chirpy 用了一些 GitHub Pages 安全模式不允许的脚本。
- 头像、favicon 这些资源要按 `_config.yml` 里写的路径放到 `assets/img/` 下。GitHub 网页端不能直接创建空文件夹，得先建个占位文件。
- 第一次部署后没显示，去 **Actions** 标签看看构建日志，多半是 `_config.yml` 的 YAML 语法错了（缩进或冒号后面少了空格）。

主题完整文档在 <https://chirpy.cotes.page>，所有可配置项和高级用法都在那里。