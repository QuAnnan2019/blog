# Butterfly Style Restoration Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Apply the approved legacy Butterfly identity, local cover/avatar, native layout, and light effects without restoring legacy content or plugins.

**Architecture:** The two approved presentation images enter the new source tree at `source/img/`; root `_config.yml` stores identity and root `_config.butterfly.yml` stores theme overrides. Native Markdown pages make the visible routes valid, and the existing Pages workflow publishes the final static output.

**Tech Stack:** Hexo 8.1.2, Butterfly 5.7.0, YAML, Markdown, GitHub Pages Actions, agent-browser.

---

## File Structure

- Create: `D:\\project\\hexo-modern\\source\\img\\xiaogong.jpg` — local avatar.
- Create: `D:\\project\\hexo-modern\\source\\img\\fengmian.png` — local home cover.
- Create: `D:\\project\\hexo-modern\\source\\about\\index.md` — about route.
- Create: `D:\\project\\hexo-modern\\source\\tags\\index.md` — tags route.
- Create: `D:\\project\\hexo-modern\\source\\categories\\index.md` — categories route.
- Delete: `D:\\project\\hexo-modern\\source\\_posts\\hello-world.md` — generated sample.
- Modify: `D:\\project\\hexo-modern\\_config.yml` and `_config.butterfly.yml` — identity, empty-home generation, and presentation.

### Task 1: Add Approved Images and Remove the Sample Post

**Files:**
- Create: `D:\\project\\hexo-modern\\source\\img\\xiaogong.jpg`
- Create: `D:\\project\\hexo-modern\\source\\img\\fengmian.png`
- Delete: `D:\\project\\hexo-modern\\source\\_posts\\hello-world.md`

- [ ] **Step 1: Verify the legacy image sources**

```powershell
Get-Item D:\\project\\hexo\\blog\\source\\img\\xiaogong.jpg | Select-Object FullName,Length
Get-Item D:\\project\\hexo\\blog\\source\\img\\fengmian.png | Select-Object FullName,Length
```

Expected: both files exist and are non-empty. Do not copy other legacy assets.

- [ ] **Step 2: Copy just the approved image files**

```powershell
New-Item -ItemType Directory -Force -Path D:\\project\\hexo-modern\\source\\img | Out-Null
Copy-Item -Force D:\\project\\hexo\\blog\\source\\img\\xiaogong.jpg D:\\project\\hexo-modern\\source\\img\\xiaogong.jpg
Copy-Item -Force D:\\project\\hexo\\blog\\source\\img\\fengmian.png D:\\project\\hexo-modern\\source\\img\\fengmian.png
Get-FileHash D:\\project\\hexo\\blog\\source\\img\\xiaogong.jpg,D:\\project\\hexo-modern\\source\\img\\xiaogong.jpg
Get-FileHash D:\\project\\hexo\\blog\\source\\img\\fengmian.png,D:\\project\\hexo-modern\\source\\img\\fengmian.png
```

Expected: each source/destination hash pair is equal.

- [ ] **Step 3: Remove only the generated post with Git**

```powershell
Set-Location D:\\project\\hexo-modern
git rm source/_posts/hello-world.md
Test-Path .\\source\\_posts\\hello-world.md
```

Expected: the final command returns `False`.

### Task 2: Configure the Native Butterfly Presentation

**Files:**
- Modify: `D:\\project\\hexo-modern\\_config.yml`
- Modify: `D:\\project\\hexo-modern\\_config.butterfly.yml`

- [ ] **Step 1: Set the approved root identity**

Replace the root configuration values with:

```yaml
title: 你好吖~
subtitle: coding
description: coding
keywords: 博客
author: 安南
```

Expected: existing `url`, `root`, `language`, `timezone`, and `theme` values remain unchanged.

- [ ] **Step 2: Write the entire Butterfly override file**

Write `D:\\project\\hexo-modern\\_config.butterfly.yml` as:

```yaml
menu:
  主页: / || fas fa-home
  时间轴: /archives/ || fas fa-archive
  标签: /tags/ || fas fa-tags
  分类: /categories/ || fas fa-folder-open
  关于: /about/ || fas fa-heart

avatar:
  img: /img/xiaogong.jpg
  effect: false

index_img: /img/fengmian.png

subtitle:
  enable: true
  effect: true
  source: false
  sub:
    - coding

enter_transitions: true

darkmode:
  enable: true
  button: true
  autoChangeMode: 1

translate:
  enable: false

aside:
  enable: true
  hide: true
  button: true
  mobile: true
  position: left
  card_author:
    enable: true
    description: coding
    button:
      enable: false
  card_announcement:
    enable: true
    content: 欢迎来到『annnan』的小站(￣▽￣)～■干杯□～(￣▽￣)
  card_recent_post:
    enable: true
    limit: 5
  card_archives:
    enable: true
    type: monthly
    format: MMMM YYYY
    order: -1
    limit: 8
  card_webinfo:
    enable: true
    post_count: true
    last_push_date: true

footer:
  owner:
    enable: true
    since: 2021
  custom_text: Hi, welcome to my blog!
```

Expected: no social link, remote text source, script injection, counter, comment, or legacy-plugin setting exists.

### Task 3: Create the Working Native Routes

**Files:**
- Create: `D:\\project\\hexo-modern\\source\\about\\index.md`
- Create: `D:\\project\\hexo-modern\\source\\tags\\index.md`
- Create: `D:\\project\\hexo-modern\\source\\categories\\index.md`

- [ ] **Step 1: Configure the empty-home generator and create the About page**

In `D:\\project\\hexo-modern\\_config.yml`, set `index_generator.per_page` to `0`. Hexo's index generator then produces `public\\index.html` with an empty `page.posts` collection.

Create the About page:

```markdown
---
title: 关于
date: 2026-09-16 00:00:00
---

你好吖~
```

Save it as `D:\\project\\hexo-modern\\source\\about\\index.md`.

- [ ] **Step 2: Create the tags page**

```markdown
---
title: 标签
date: 2026-09-16 00:00:00
type: tags
---
```

Save it as `D:\\project\\hexo-modern\\source\\tags\\index.md`.

- [ ] **Step 3: Create the categories page**

```markdown
---
title: 分类
date: 2026-09-16 00:00:00
type: categories
---
```

Save it as `D:\\project\\hexo-modern\\source\\categories\\index.md`.

- [ ] **Step 4: Commit the presentation changes**

```powershell
Set-Location D:\\project\\hexo-modern
git add _config.yml _config.butterfly.yml source/img source/about source/tags source/categories
git add -u source/_posts/hello-world.md
git commit -m "feat: restore Butterfly site presentation"
```

Expected: only approved assets, settings, routes, and the sample-post deletion are committed.

### Task 4: Verify Locally and Publish

**Files:**
- Create: `D:\\project\\hexo-modern\\public\\` — ignored generated output.

- [ ] **Step 1: Build and assert the static output**

```powershell
Set-Location D:\\project\\hexo-modern
npx hexo clean
npx hexo generate
Test-Path .\\public\\about\\index.html
Test-Path .\\public\\tags\\index.html
Test-Path .\\public\\categories\\index.html
Select-String -Path .\\public\\index.html -Pattern 'Hello World' -Quiet
Select-String -Path .\\public\\index.html -Pattern '/blog/img/fengmian.png' -Quiet
Select-String -Path .\\public\\index.html -Pattern '/blog/img/xiaogong.jpg' -Quiet
```

Expected: the first three checks return `True`; the sample-text check returns `False`; both image-path checks return `True`.

- [ ] **Step 2: Verify the local interactive page**

Run `npx hexo server`, then use an isolated agent-browser session for `http://localhost:4000/blog/`. Confirm title `你好吖~`, all five menu links, the absence of `Follow Me`, and the built-in theme controls. Save any screenshot outside version control.

- [ ] **Step 3: Publish the verified main branch**

```powershell
Set-Location D:\\project\\hexo-modern
git push --set-upstream origin main
$mainSha = git rev-parse main
$run = gh run list --repo QuAnnan2019/blog --workflow deploy-pages.yml --branch main --commit $mainSha --limit 1 --json databaseId,status,conclusion,headSha | ConvertFrom-Json | Select-Object -First 1
if (-not $run -or $run.headSha -ne $mainSha) { throw 'Expected Pages run was not found for the new main commit.' }
gh run watch $run.databaseId --repo QuAnnan2019/blog --exit-status
```

Expected: the matching Pages workflow exits successfully.

- [ ] **Step 4: Verify the live homepage**

```powershell
$response = Invoke-WebRequest -UseBasicParsing https://quannan2019.github.io/blog/
"status=$($response.StatusCode)"
"hasTitle=$($response.Content -match '<title>你好吖~</title>')"
"hasCover=$($response.Content -match '/blog/img/fengmian.png')"
git -C D:\\project\\hexo-modern status --short
```

Expected: `status=200`, `hasTitle=True`, `hasCover=True`, and no uncommitted tracked changes.
