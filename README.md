# Lumina Method Lab — 网站说明文档

> 当前版本：ToFu 内容站（讲故事阶段）
> 技术栈：Hugo 静态网站 + Cloudflare Workers 部署
> 网址：luminamethod.eu

---

## 目录结构

```
luminamethod-website/
│
├── content/                        ← 【日常编辑在这里】
│   ├── about.md                    ← 「关于我们」页面内容
│   ├── posts/                      ← 所有文章
│   │   ├── _index.md               ← 文章列表页的标题/描述（不是文章）
│   │   ├── 第一期_v0.md
│   │   ├── 第二期_v0.md
│   │   └── ...
│   └── _index.md                   ← 网站首页 SEO 元信息（不是首页内容）
│
├── static/                         ← 静态资源（直接复制到发布目录）
│   ├── logo.svg                    ← 导航栏 Logo（深色版，用于白色背景）
│   ├── logo-light.svg              ← 页脚 Logo（浅色版，用于深蓝背景）
│   └── _headers                    ← Cloudflare 安全响应头配置
│
├── themes/lumina/                  ← 主题（改样式/结构在这里）
│   ├── layouts/
│   │   ├── index.html              ← 首页模板（当前：显示最新一篇文章）
│   │   └── _default/
│   │       ├── baseof.html         ← 全站框架（导航、页脚、字体加载）
│   │       ├── single.html         ← 单篇文章/单个页面模板
│   │       └── list.html           ← 文章列表页模板（/posts/）
│   └── static/
│       └── styles.css              ← 全站样式表
│
├── hugo.toml                       ← 网站配置（标题、邮箱、描述等）
├── wrangler.json                   ← Cloudflare 部署配置
└── .claude/launch.json             ← 本地开发服务器配置
```

---

## 日常操作

### 写新文章

在 `content/posts/` 新建一个 `.md` 文件，开头必须有 front matter：

```markdown
---
title: "文章标题"
date: 2026-04-13
description: "一句话描述，显示在标题下方"
tags: ["财务框架", "法国理财"]
---

正文从这里开始，支持标准 Markdown 语法。
```

- **date** 决定文章排序（最新的显示在首页和列表顶部）
- **description** 显示为标题下方的斜体摘要
- **tags** 显示为文章头部的标签徽章
- 文件名用英文或拼音，避免空格（例：`di-wu-qi.md`）

### 修改「关于我们」

直接编辑 `content/about.md`，Markdown 正文即是页面内容。日期不显示在此页面。

### 修改网站基本信息

编辑 `hugo.toml`：

```toml
title = "Lumina Method Lab"          # 浏览器标签页标题

[params]
  description = "..."                # 网站默认 SEO 描述
  email = "contact@luminamethod.eu"  # 页脚联系邮箱
  location = "France"                # 页脚地点
```

---

## 视觉设计

### 字体

| 用途 | 字体 | 备注 |
|------|------|------|
| 大标题（拉丁） | Fraunces | 有温度感的衬线体，区别于竞品 |
| 中文标题 | Noto Serif SC | 随 Fraunces 自动回退，无需配置 |
| 正文/界面 | DM Sans | 中性无衬线，衬托中文不抢戏 |
| 中文正文 | Noto Sans SC | 长文阅读最佳中文无衬线 |

字体通过 Google Fonts 加载，访客本地无需安装。
加载代码在 `themes/lumina/layouts/_default/baseof.html` 的 `<head>` 部分。

### 颜色

所有颜色变量定义在 `themes/lumina/static/styles.css` 顶部的 `:root {}` 块，修改一处全站生效。

| 变量 | 值 | 用途 |
|------|----|------|
| `--bg` | `#F5F2EB` | 页面背景（暖白） |
| `--accent` | `#1D4E89` | 品牌主色（深海军蓝） |
| `--amber` | `#E8A030` | 强调色（琥珀橙，Logo 折线） |
| `--text` | `#1C1917` | 正文文字 |
| `--muted` | `#78716C` | 次要文字、日期 |

### Logo

- `static/logo.svg` — 深色版（导航栏，白色背景）
- `static/logo-light.svg` — 浅色版（页脚，深蓝背景）

替换 Logo：直接覆盖这两个文件，保持文件名不变即可。

---

## 页面结构

### 首页

由 `themes/lumina/layouts/index.html` 控制。
**当前逻辑：自动取日期最新的一篇文章，完整渲染在首页。**

如需改为其他内容（固定文字、文章列表等），修改这个文件。

### 导航

只有两项：**文章**（`/posts/`）和**关于我们**（`/about/`）。
导航代码在 `themes/lumina/layouts/_default/baseof.html` 的 `<nav>` 部分。

### 文章列表页

访问 `/posts/` 显示所有文章，按日期倒序排列。
列表页的标题和描述在 `content/posts/_index.md` 修改。

---

## 本地预览

在 Claude Code 中直接启动（配置已保存在 `.claude/launch.json`）：

```bash
hugo server --buildDrafts --port 1313
```

访问 `http://localhost:1313`。保存文件后自动刷新，无需重启服务器。

---

## 发布上线

```bash
# 第一步：构建静态文件（输出到 public/ 目录）
hugo

# 第二步：部署到 Cloudflare Workers
wrangler deploy
```

发布前确认 `wrangler.json` 中 `assets.directory` 已改为 `"./public"`。

---

## 不要动的文件

| 位置 | 原因 |
|------|------|
| `.git/` | Git 版本历史，动了会损坏记录 |
| `public/` | Hugo 自动生成，每次构建覆盖，不需要手动编辑 |
| `.claude/` | Claude Code 工具配置 |
| `static/_headers` | Cloudflare 安全头，不了解不要改 |
| `wrangler.json` | 部署配置，改错会导致无法上线 |
