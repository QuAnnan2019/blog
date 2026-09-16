# Latest Hexo + Butterfly Bootstrap Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build an isolated local Hexo site using the latest stable Hexo and Butterfly releases, ready for a Chinese `/blog/` preview.

**Architecture:** Initialize a fresh Hexo project at `D:\\project\\hexo-modern`, then clone Butterfly tag `5.7.0` under `themes/butterfly` and track its source in the parent repository. Keep the root config and theme overrides separate, validate static generation and local preview, and leave all GitHub publishing out of scope.

**Tech Stack:** Node.js 24, npm 11, latest stable Hexo packages, stable `jerryc127/hexo-theme-butterfly` release, Git, PowerShell.

---

## File Structure

- Create: `D:\\project\\hexo-modern\\package.json` and `package-lock.json` — clean Hexo dependency definition.
- Create: `D:\\project\\hexo-modern\\_config.yml` — root Hexo configuration.
- Create: `D:\\project\\hexo-modern\\_config.butterfly.yml` — user-owned Butterfly overrides.
- Create: `D:\\project\\hexo-modern\\.gitignore` — generated and local-only file exclusions.
- Create: `D:\\project\\hexo-modern\\scaffolds\\` and `source\\` — fresh Hexo defaults.
- Create: `D:\\project\\hexo-modern\\themes\\butterfly` — source for the pinned current Butterfly theme.

### Task 1: Select Versions and Initialize a Clean Hexo Application

**Files:**
- Create: `D:\\project\\hexo-modern\\package.json`
- Create: `D:\\project\\hexo-modern\\package-lock.json`
- Create: `D:\\project\\hexo-modern\\_config.yml`
- Create: `D:\\project\\hexo-modern\\scaffolds\\`
- Create: `D:\\project\\hexo-modern\\source\\`

- [ ] **Step 1: Verify the local toolchain and current package metadata**

Run:

```powershell
node --version
npm --version
git --version
npm view hexo version
npm view hexo-cli version
git ls-remote --tags --refs https://github.com/jerryc127/hexo-theme-butterfly.git
```

Expected: Node 24.x, npm 11.x, Git, Hexo `8.1.2`, Hexo CLI `4.3.2`, and Butterfly tag `5.7.0`. Stop for review if a newer stable release is returned; do not substitute an upstream development branch.

- [ ] **Step 2: Verify that the target still contains only committed documentation**

Run:

```powershell
git -C D:\\project\\hexo-modern status --short
Get-ChildItem -Force D:\\project\\hexo-modern
```

Expected: no untracked application files. Retain the existing `docs\\superpowers` tree.

- [ ] **Step 3: Generate a fresh site outside the target and copy only its application source**

Run:

```powershell
Set-Location D:\\project
npx --yes hexo-cli@latest init hexo-bootstrap-tmp
Copy-Item -Recurse -Force D:\\project\\hexo-bootstrap-tmp\\scaffolds D:\\project\\hexo-modern\\scaffolds
Copy-Item -Recurse -Force D:\\project\\hexo-bootstrap-tmp\\source D:\\project\\hexo-modern\\source
Copy-Item -Force D:\\project\\hexo-bootstrap-tmp\\_config.yml D:\\project\\hexo-modern\\_config.yml
Copy-Item -Force D:\\project\\hexo-bootstrap-tmp\\package.json D:\\project\\hexo-modern\\package.json
Copy-Item -Force D:\\project\\hexo-bootstrap-tmp\\package-lock.json D:\\project\\hexo-modern\\package-lock.json
```

Expected: the target has a generated Hexo application plus its existing documentation. Never copy any source, config, lockfile, dependency, deployment script, or certificate from `D:\\project\\hexo\\blog`.

- [ ] **Step 4: Remove the verified temporary bootstrap directory**

Run:

```powershell
Test-Path D:\\project\\hexo-bootstrap-tmp\\package.json
Remove-Item -Recurse -Force D:\\project\\hexo-bootstrap-tmp
Test-Path D:\\project\\hexo-bootstrap-tmp
```

Expected: the first command returns `True`; the final command returns `False`. This removal is limited to the temporary directory created in Step 3.

- [ ] **Step 5: Install and check the generated dependencies**

Run:

```powershell
Set-Location D:\\project\\hexo-modern
npm ci
npx hexo version
```

Expected: `npm ci` creates `node_modules` without changing `package-lock.json`, and the second command identifies Hexo, Node, plus its renderer/generator plugins.

- [ ] **Step 6: Commit the fresh framework baseline**

Run:

```powershell
git add package.json package-lock.json _config.yml scaffolds source docs
git commit -m "chore: initialize latest Hexo site"
```

Expected: one commit contains only generated source and planning docs, never `node_modules`, `public`, or legacy-project files.

### Task 2: Pin and Install the Latest Stable Butterfly Theme

**Files:**
- Create: `D:\\project\\hexo-modern\\themes\\butterfly`
- Modify: `D:\\project\\hexo-modern\\package.json`
- Modify: `D:\\project\\hexo-modern\\package-lock.json`

- [ ] **Step 1: Clone Butterfly at the verified stable tag**

Run from `D:\\project\\hexo-modern`:

```powershell
git clone --depth 1 --branch 5.7.0 https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
git -C themes\\butterfly describe --tags --exact-match
Remove-Item -Recurse -Force themes\\butterfly\\.git
```

Expected: the theme directory contains `_config.yml`; `describe` returns `5.7.0` before the nested Git metadata is removed. The removal is limited to metadata created by this clone, allowing the parent repository to track the exact source contents.

- [ ] **Step 2: Install Butterfly's required renderers**

Run:

```powershell
npm install hexo-renderer-pug@latest hexo-renderer-stylus@latest
npx hexo version
```

Expected: both renderers appear in `package.json`, and Hexo lists them without an error. Do not install former-project plugins such as music, Live2D, charts, encrypt, bangumi, analytics, or comments.

- [ ] **Step 3: Commit the pinned theme and renderers**

Run:

```powershell
git add themes/butterfly package.json package-lock.json
git commit -m "feat: add current Butterfly theme"
```

Expected: the commit pins one known theme release and adds only its necessary rendering dependencies.

### Task 3: Configure Chinese, System Color Scheme, and `/blog/`

**Files:**
- Modify: `D:\\project\\hexo-modern\\_config.yml`
- Create: `D:\\project\\hexo-modern\\_config.butterfly.yml`
- Create: `D:\\project\\hexo-modern\\.gitignore`

- [ ] **Step 1: Set only the required root Hexo fields**

In `D:\\project\\hexo-modern\\_config.yml`, set these keys and preserve all remaining fresh-generator defaults:

```yaml
title: 个人博客
subtitle: ''
description: ''
keywords: null
author: ''
language: zh-CN
timezone: Asia/Shanghai
url: https://quannan2019.github.io
root: /blog/
theme: butterfly
```

Expected: the theme is Butterfly and all generated links use the intended Pages subpath. Do not add profiles, social URLs, avatars, analytics, comments, custom widgets, or external services.

- [ ] **Step 2: Write the minimal Butterfly override file**

Create `D:\\project\\hexo-modern\\_config.butterfly.yml` with:

```yaml
darkmode:
  enable: true
  button: true
  autoChangeMode: 1

translate:
  enable: false
```

Expected: Butterfly follows the system preference when available and does not add a Chinese conversion control. All other components retain the current theme's defaults.

- [ ] **Step 3: Exclude generated and local-only data**

Create `D:\\project\\hexo-modern\\.gitignore` with:

```gitignore
node_modules/
public/
db.json
.DS_Store
.cache/
```

Expected: dependency and generated files are ignored, while source, configuration, documentation, and theme references remain trackable.

- [ ] **Step 4: Commit the minimal site configuration**

Run:

```powershell
git add _config.yml _config.butterfly.yml .gitignore
git commit -m "feat: configure Chinese Butterfly preview"
```

Expected: no old-site or sensitive configuration is staged.

### Task 4: Validate a Local Preview and Preserve the Clean Boundary

**Files:**
- Create: `D:\\project\\hexo-modern\\public\\index.html` — generated, ignored output.

- [ ] **Step 1: Generate the production site from a clean state**

Run from `D:\\project\\hexo-modern`:

```powershell
npx hexo clean
npx hexo generate
Test-Path .\\public\\index.html
Select-String -Path .\\public\\index.html -Pattern '/blog/' -Quiet
```

Expected: generation reports no theme or renderer errors and the final two commands return `True`.

- [ ] **Step 2: Start and inspect the local site**

Run:

```powershell
npx hexo server
```

Expected: Hexo serves the site locally. Inspect `http://localhost:4000/blog/` for a Butterfly homepage, navigation, default post, and a working light/dark control. Stop the server after inspection.

- [ ] **Step 3: Verify the Git boundary before the final commit**

Run:

```powershell
git status --short
git check-ignore node_modules public db.json
git log --oneline -3
```

Expected: `node_modules`, `public`, and `db.json` are ignored; no file refers to `D:\\project\\hexo\\blog`, `annnan.com_nginx`, `D:\\project\\hugo`, GitHub Pages workflows, tokens, or certificate material. The Git log contains only planning/bootstrap commits in the new project.
