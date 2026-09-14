# Chinese Profile Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Present the LoveIt site as a Chinese-first personal knowledge blog with an original replaceable illustration avatar and Chinese starter content.

**Architecture:** Keep LoveIt vendored and configured through the root `hugo.toml`; no theme templates or CSS are overridden. Hugo copies a generated avatar from `static/images/` into the published site, while content Markdown supplies the Chinese page metadata and prose. The default theme remains `auto`, so LoveIt follows each visitor's system appearance preference.

**Tech Stack:** Hugo Extended 0.166.0, vendored LoveIt theme, TOML configuration, Markdown content, generated PNG asset, GitHub Pages.

---

## File Structure

- Create: `D:\project\hugo\static\images\avatar.png` - original profile illustration served as `/images/avatar.png`.
- Modify: `D:\project\hugo\hugo.toml` - Chinese site and author metadata plus profile avatar reference.
- Modify: `D:\project\hugo\content\_index.md` - Chinese home metadata.
- Modify: `D:\project\hugo\content\about\_index.md` - Chinese personal introduction.
- Modify: `D:\project\hugo\content\posts\_index.md` - Chinese blog listing metadata.
- Modify: `D:\project\hugo\content\notes\_index.md` - Chinese notes listing metadata.
- Modify: `D:\project\hugo\content\posts\welcome-to-my-digital-garden.md` - Chinese welcome post.
- Modify: `D:\project\hugo\content\notes\markdown-writing-workflow.md` - Chinese starter note.

### Task 1: Add a Replaceable Illustration Avatar

**Files:**
- Create: `D:\project\hugo\static\images\avatar.png`
- Test: generated static output at `D:\project\hugo\public\images\avatar.png`

- [ ] **Step 1: Generate the avatar asset**

Use the `imagegen` skill to create a 512x512 PNG with this prompt:

```text
Minimal original flat editorial avatar illustration, a friendly East Asian adult with short dark hair, neutral expression, warm off-white background, muted teal and charcoal accents, centered head-and-shoulders portrait, clean geometric shapes, no text, no logo, no watermark, suitable for a personal technical knowledge blog.
```

Save the returned PNG as `D:\project\hugo\static\images\avatar.png`.

- [ ] **Step 2: Verify the asset is present and square**

Run:

```powershell
Get-Item D:\project\hugo\static\images\avatar.png | Select-Object FullName,Length
```

Expected: the file exists and has a nonzero length.

- [ ] **Step 3: Commit the asset**

Run:

```powershell
Set-Location D:\project\hugo
git add static/images/avatar.png
git commit -m "feat: add profile illustration"
```

Expected: one commit contains only the avatar image.

### Task 2: Configure the Chinese Profile

**Files:**
- Modify: `D:\project\hugo\hugo.toml`
- Test: `hugo --gc --minify`

- [ ] **Step 1: Set Chinese site identity and author data**

In the existing `[params]` block, set the title and description. Add author metadata, retain `defaultTheme = "auto"`, and update the existing profile block to match:

```toml
[params]
  defaultTheme = "auto"
  dateFormat = "2006-01-02"
  title = "安南的博客"
  description = "记录技术、思考与知识。"

  [params.author]
    name = "安南"
    email = ""
    link = ""

  [params.header]
    desktopMode = "fixed"
    mobileMode = "auto"
    [params.header.title]
      name = "安南的博客"
      typeit = false

  [params.home]
    [params.home.profile]
      enable = true
      avatarURL = "/images/avatar.png"
      title = "安南的博客"
      subtitle = "博客、笔记，以及值得长期保存的思考。"
      typeit = false
      social = false
```

Keep `title = "安南的博客"`, `locale = "zh-cn"`, `defaultContentLanguage = "zh-cn"`, and the existing Chinese navigation entries at the root of `hugo.toml`. Do not change `baseURL`.

- [ ] **Step 2: Build the site with the production configuration**

Run:

```powershell
Set-Location D:\project\hugo
hugo --gc --minify
Test-Path public/images/avatar.png
```

Expected: Hugo exits with code 0 and the final command outputs `True`.

- [ ] **Step 3: Commit the profile configuration**

Run:

```powershell
git add hugo.toml
git commit -m "feat: configure Chinese profile"
```

Expected: one commit contains only `hugo.toml`.

### Task 3: Localize the Starter Content

**Files:**
- Modify: `D:\project\hugo\content\_index.md`
- Modify: `D:\project\hugo\content\about\_index.md`
- Modify: `D:\project\hugo\content\posts\_index.md`
- Modify: `D:\project\hugo\content\notes\_index.md`
- Modify: `D:\project\hugo\content\posts\welcome-to-my-digital-garden.md`
- Modify: `D:\project\hugo\content\notes\markdown-writing-workflow.md`
- Test: `hugo --gc --minify`

- [ ] **Step 1: Replace section and home metadata**

Set each file's front matter and visible copy as follows:

```markdown
<!-- content/_index.md -->
---
title: "首页"
---

<!-- content/posts/_index.md -->
---
title: "博客"
description: "完整的文章、项目复盘与值得展开的思考。"
---

<!-- content/notes/_index.md -->
---
title: "笔记"
description: "持续整理的知识、参考资料与长期有效的方法。"
---

<!-- content/about/_index.md -->
---
title: "关于我"
---

这里是安南的个人知识空间，用来记录技术实践、学习笔记与值得反复推敲的想法。
```

- [ ] **Step 2: Replace the welcome post with Chinese starter prose**

Replace `content/posts/welcome-to-my-digital-garden.md` with:

```markdown
---
title: "欢迎来到安南的博客"
date: 2026-09-11T09:00:00+08:00
draft: false
categories: ["随笔"]
tags: ["欢迎", "知识管理"]
series: ["站点笔记"]
---

这里存放已经沉淀成形的思考，也保留它们与笔记之间的联系。

## 这里适合写什么

博客用于发布已经准备好分享的内容：一篇随笔、一次项目复盘，或一个值得完整说明的实践经验。

## 让知识持续生长

笔记不必足够完整才有价值。一个简短的记录可以随着补充的背景、案例和观点，慢慢发展成一篇文章。
```

- [ ] **Step 3: Replace the starter note with Chinese starter prose**

Replace `content/notes/markdown-writing-workflow.md` with:

```markdown
---
title: "Markdown 写作工作流"
date: 2026-09-11T10:00:00+08:00
lastmod: 2026-09-11T10:00:00+08:00
draft: false
categories: ["知识库"]
tags: ["Markdown", "写作", "工作流"]
series: ["写作系统"]
---

使用一个简单的循环：捕捉、澄清、连接、发布。

## 捕捉

想法出现时，先写下最小但有用的版本。

## 澄清

给笔记一个标题、几个标签，并补足未来的自己能够理解的背景。

## 连接

在扩写之前，用共同的标签或系列关联相关笔记。
```

- [ ] **Step 4: Build and check that starter English copy is absent from the generated home page**

Run:

```powershell
hugo --gc --minify
$home = Get-Content -Raw public/index.html
"ContainsEnglishStarter=$($home -match 'My Knowledge Garden|Essays, notes, and things worth keeping')"
```

Expected: Hugo exits with code 0 and the final command outputs `ContainsEnglishStarter=False`.

- [ ] **Step 5: Commit the localized content**

Run:

```powershell
git add content/_index.md content/about/_index.md content/posts/_index.md content/notes/_index.md content/posts/welcome-to-my-digital-garden.md content/notes/markdown-writing-workflow.md
git commit -m "docs: localize starter content"
```

Expected: one commit contains the six localized content files.

### Task 4: Verify the Published Experience

**Files:**
- Modify: none
- Test: local preview, production build, GitHub Pages deployment

- [ ] **Step 1: Start a local Hugo preview**

Run:

```powershell
Set-Location D:\project\hugo
hugo server --disableFastRender --port 1313
```

Expected: Hugo reports that the site is available at `http://localhost:1313/blog/`.

- [ ] **Step 2: Verify the profile and primary navigation in a browser**

Open `http://localhost:1313/blog/`. Confirm the circular illustration avatar, `安南的博客`, Chinese subtitle, Chinese navigation, search icon, and automatic theme toggle are visible. Open `/blog/posts/`, `/blog/notes/`, `/blog/tags/`, and `/blog/about/` and confirm each renders.

- [ ] **Step 3: Push the four commits and watch deployment**

Run:

```powershell
git status --short
git -c http.version=HTTP/1.1 push
gh run watch --repo QuAnnan2019/blog --exit-status
```

Expected: `git status --short` has no output, push completes, and the Pages workflow concludes with `success`.

- [ ] **Step 4: Verify the public homepage**

Run:

```powershell
curl.exe -I https://quannan2019.github.io/blog/
```

Expected: response includes `HTTP/1.1 200 OK`.
