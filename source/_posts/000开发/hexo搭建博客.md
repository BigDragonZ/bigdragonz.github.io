---
title: hexo搭建博客
date: 2025-11-09 10:57:02
tags: [博客]
categories: [实践]
---

# 环境准备

## 初始化

```
npm install -g hexo-cli
hexo init my-blog
cd my-blog
npm install
hexo server
# 或者简写为
hexo s
# 安装部署插件: 让 hexo deploy 命令生效
npm install hexo-deployer-git --save
```

## hexo目录

```
my-blog
├── _config.yml       # 网站的**主配置文件**，非常重要！
├── source            # 存放你的资源和文章
│   └── _posts        # 你的所有博客文章都放在这里（.md文件）
├── themes            # 存放主题的文件夹
│   └── landscape     # 默认主题
└── public            # 执行生成命令后，Hexo会在这里创建静态文件（用于部署）
```

## 常用命令

- `hexo clean`：清理缓存文件和已生成的静态文件 (`public` 文件夹)。在遇到奇怪的问题时，先执行这个命令。
- `hexo generate` 或 `hexo g`：根据 `source` 下的内容生成静态文件到 `public` 文件夹。
- `hexo server` 或 `hexo s`：启动本地服务器，用于预览。
- `hexo deploy` 或 `hexo d`：部署网站到远程仓库（比如 GitHub）

## 常用配置

修改主配置文件 (`_config.yml`

```
# Site
title: 张三的博客        # 博客标题
subtitle: '热爱技术与生活' # 副标题
description: '...'       # 描述，用于SEO
keywords:               # 关键词
author: 张三            # 作者名
language: zh-CN         # 语言，中文主题建议用这个
timezone: 'Asia/Shanghai' # 时区
```

**部署配置**

```
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: git
  repo: https://github.com/<你的用户名>/<你的用户名>.github.io.git
  # 例如： repo: https://github.com/zhangsan/zhangsan.github.io.git
  branch: main          # 或者 gh-pages，但 main 最简单
  message: "Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }}" # 可选的部署提交信息
```

## 发布文章

```
hexo new "我的第一篇文章"
hexo clean && hexo generate
hexo deploy
```

第一次执行时，可能会弹出一个窗口让你输入 GitHub 的用户名和密码（Personal Access Token）。

- **重要**：由于 GitHub 不再支持账户密码直接操作，你需要使用 **Personal Access Token**。
- **生成 Token**：在 GitHub -> Settings -> Developer settings -> Personal access tokens -> Tokens (classic) 生成一个新的 Token。需要勾选 `repo` 权限。生成后请妥善保存。
- 在弹出窗口中，**用户名**填你的 GitHub 用户名，**密码**就填刚才生成的 Token。

## 绑定自定义域名

如果你有自己的域名（比如 `www.example.com`），可以将其绑定到你的 GitHub Pages。

1. 在博客项目根目录的 `source` 文件夹下，创建一个名为 `CNAME` 的文件（**没有后缀**）。
2. 在文件内写入你的域名，例如：`www.example.com`。
3. 在你的域名注册商那里，添加两条 DNS 记录：
   - **类型 CNAME**： 主机记录 `www`， 记录值 `<你的用户名>.github.io`。
   - **类型 A**： 主机记录 `@`， 记录值 `185.199.108.153`（这是 GitHub Pages 的 IP，有多个，可以都配上）。
4. 将 `CNAME` 文件提交并部署到 GitHub。
5. 在 GitHub 仓库的 **Settings -> Pages -> Custom domain** 中，填上你的域名并保存。

# 多分支管理

很好问到了这个问题！Hexo + GitHub Pages 部署后，文件管理是非常重要的。这里为你详细解释如何高效地管理你的博客目录和文件。

## 核心概念：两种仓库管理策略

### 策略一：单分支管理（推荐给初学者）
只使用 `main` 分支存放生成的静态文件（`public` 文件夹内容）

### 策略二：双分支管理（**推荐**）
- `main` 分支：存放生成的静态文件（用于 GitHub Pages 展示）
- `source` 分支：存放完整的 Hexo 源码文件

---

## 策略二详细配置（最佳实践）

### 1. 初始化源码分支

在你的 Hexo 博客项目根目录执行：

```bash
# 1. 初始化 Git（如果还没初始化）
git init

# 2. 创建并切换到 source 分支
git checkout -b source

# 3. 添加所有文件到暂存区
git add .

# 4. 提交初始版本
git commit -m "Initial Hexo source code"

# 5. 添加远程仓库
git remote add origin https://github.com/你的用户名/你的用户名.github.io.git

# 6. 推送 source 分支到 GitHub
git push -u origin source
```

### 2. 配置部署到 main 分支

确保 `_config.yml` 中的部署配置指向 `main` 分支：

```yaml
deploy:
  type: git
  repo: https://github.com/你的用户名/你的用户名.github.io.git
  branch: main
```

### 3. 日常工作流程

```bash
# 1. 确保在 source 分支
git checkout source

# 2. 写新文章
hexo new "新文章标题"

# 3. 编辑文章（在 source/_posts/）
# 4. 本地测试
hexo clean && hexo g && hexo s

# 5. 部署到 GitHub Pages（更新 main 分支）
hexo clean && hexo g && hexo d

# 6. 保存源码变更到 source 分支
git add .
git commit -m "添加新文章：新文章标题"
git push origin source
```

---

## 重要文件和目录管理

### 必须纳入版本控制的文件/目录：

```
my-blog/
├── _config.yml           # ✅ 网站配置文件（必须）
├── themes/               # ✅ 主题目录（必须）
├── source/               # ✅ 源文件目录（必须）
│   ├── _posts/           # ✅ 所有文章（必须）
│   └── _pages/           # ✅ 自定义页面（如果有）
├── scaffolds/            # ✅ 模板文件（建议）
├── package.json          # ✅ 依赖配置（必须）
└── .gitignore            # ✅ Git忽略文件（必须）
```

### 不应该纳入版本控制的文件/目录：

```
my-blog/
├── node_modules/         # ❌ 依赖包（太大，可重新安装）
├── public/               # ❌ 生成的静态文件（自动生成）
├── .deploy_git/          # ❌ 部署缓存（自动生成）
└── db.json               # ❌ 缓存数据库（自动生成）
```

### 正确的 .gitignore 文件内容：

```
# 依赖目录
node_modules/

# 生成的文件
public/
.deploy_git/

# 缓存文件
db.json
*.log

# 环境变量
.env

# IDE文件
.vscode/
.idea/
*.swp
*.swo
```

---

## 多设备同步工作流程

当你需要在另一台电脑上继续写博客时：

```bash
# 1. 克隆源码分支
git clone -b source https://github.com/你的用户名/你的用户名.github.io.git my-blog
cd my-blog

# 2. 安装依赖
npm install

# 3. 开始写作和部署
hexo new "新文章"
# ... 编辑文章 ...
hexo clean && hexo g && hexo d

# 4. 推送源码更新
git add .
git commit -m "在新电脑上添加文章"
git push origin source
```

---

## 目录结构详细说明

### 核心目录：

```bash
my-blog/
├── _config.yml           # 主配置文件 - 网站标题、描述、URL等
├── package.json          # 项目依赖和脚本
├── scaffolds/            # 文章模板
│   ├── post.md          # 文章模板
│   ├── page.md          # 页面模板
│   └── draft.md         # 草稿模板
├── source/               # 源文件（重点管理）
│   ├── _posts/          # 文章目录（.md文件）
│   ├── _pages/          # 自定义页面（关于页、联系页等）
│   ├── categories/      # 分类页（自动生成）
│   ├── tags/            # 标签页（自动生成）
│   └── images/          # 图片资源
├── themes/               # 主题目录
│   └── your-theme/      # 你使用的主题
└── public/              # 生成的静态文件（自动生成，不管理）
```

### 文章文件管理：

在 `source/_posts/` 目录下，建议这样组织：

```
_posts/
├── 2023-10-01-我的第一篇文章.md
├── 2023-10-15-技术笔记.md
├── 2023-11-01-生活随笔.md
└── images/              # 文章专用图片目录
    ├── post1-image1.jpg
    └── post2-diagram.png
```

### 资源文件管理：

对于图片等资源，有两种推荐方式：

**方式一：放在主题的 source 目录**
```bash
# 在主题的 source 目录创建 images 文件夹
themes/your-theme/source/images/
```

**方式二：使用 CDN**
```markdown
![图片描述](https://cdn.example.com/your-image.jpg)
```

---

## 实用管理技巧

### 1. 使用标签和分类管理文章

在文章 Front-matter 中：

```markdown
---
title: 我的技术文章
date: 2023-10-27
tags: [Hexo, GitHub, 教程]
categories: [技术]
---
```

### 2. 创建自定义页面

```bash
hexo new page "about"
```
这会在 `source/about/index.md` 创建关于页面。

### 3. 备份重要配置

定期备份：
- `_config.yml`（主配置）
- `themes/your-theme/_config.yml`（主题配置）
- `package.json`（依赖列表）

### 4. 使用子模块管理主题

如果主题来自 Git 仓库，使用子模块：
```bash
git submodule add https://github.com/theme-next/hexo-theme-next themes/next
```

---

## 完整的日常操作示例

```bash
# 开始新的一天
cd my-blog
git checkout source
git pull origin source  # 获取最新更改

# 写新文章
hexo new "深度学习入门笔记"
# 用你喜欢的编辑器编辑 source/_posts/深度学习入门笔记.md

# 本地预览
hexo clean && hexo g && hexo s

# 部署到线上
hexo clean && hexo g && hexo d

# 保存源码
git add .
git commit -m "发布文章：深度学习入门笔记"
git push origin source

# 查看状态
git status
```

这样管理，你的博客源码就得到了完整的版本控制，可以在多设备间无缝切换，并且部署过程自动化。这是最专业和可靠的 Hexo + GitHub Pages 管理方式！


