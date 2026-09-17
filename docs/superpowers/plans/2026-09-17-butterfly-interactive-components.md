# Butterfly Interactive Components Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add the approved native Butterfly background, effects, controls, and 今日诗词 subtitle to the published Hexo site.

**Architecture:** One root `_config.butterfly.yml` override enables only theme-native features; the theme submodule, assets, posts, workflow, and root URL remain untouched. The existing Pages Action deploys the ordinary static build after local browser verification.

**Tech Stack:** Hexo 8.1.2, Butterfly 5.7.0, YAML, GitHub Pages Actions, agent-browser.

---

## File Structure

- Modify: `D:\\project\\hexo-modern\\_config.butterfly.yml` — native component settings.
- Modify: `D:\\project\\hexo-modern\\docs\\superpowers\\plans\\2026-09-17-butterfly-interactive-components.md` — records execution results.

### Task 1: Configure Native Interactive Components

**Files:**
- Modify: `D:\\project\\hexo-modern\\_config.butterfly.yml`

- [ ] **Step 1: Preserve current navigation, asset, sidebar, footer, dark-mode, and transition settings**

Run:

```powershell
Get-Content -Raw D:\\project\\hexo-modern\\_config.butterfly.yml
```

Expected: current content includes the local avatar `/img/xiaogong.jpg`, cover `/img/fengmian.png`, left sidebar, and `autoChangeMode: 1` before new effect settings are added.

- [ ] **Step 2: Add the approved native settings to the same YAML file**

Append these keys without duplicating existing top-level keys:

```yaml
background: "#49b1f5"

preloader:
  enable: true
  source: 1

readmode: true
rightside_scroll_percent: true

canvas_nest:
  enable: true
  color: '255,255,255'
  opacity: 0.35
  zIndex: -1
  count: 60
  mobile: false

clickShowText:
  enable: true
  text:
    - 富强
    - 民主
    - 文明
    - 和谐
    - 爱国
    - 敬业
    - 诚信
    - 友善
    - 公正
    - 法制
  fontSize: 15px
  random: false
  mobile: false
```

Replace the existing `subtitle` section with:

```yaml
subtitle:
  enable: true
  effect: true
  source: 3
  sub:
    - coding
```

Expected: source mode `3` uses 今日诗词. No plugins, injected scripts, external CSS, analytics, comments, or social links are added.

- [ ] **Step 3: Validate YAML with Hexo and commit it**

Run from `D:\\project\\hexo-modern`:

```powershell
npx hexo clean
npx hexo generate
git diff --check
git add _config.butterfly.yml
git commit -m "feat: add Butterfly interactive components"
```

Expected: Hexo completes generation without configuration errors and the commit contains only the theme override file.

### Task 2: Verify Locally and Publish

**Files:**
- Create: `D:\\project\\hexo-modern\\public\\` — ignored static output.

- [ ] **Step 1: Assert generated effect markers**

Run from `D:\\project\\hexo-modern`:

```powershell
Select-String -Path .\\public\\index.html -Pattern 'canvas-nest|clickShowText|jinrishici|fullpage-loading' -AllMatches
Select-String -Path .\\public\\index.html -Pattern '/blog/img/fengmian.png' -Quiet
Select-String -Path .\\public\\index.html -Pattern '/blog/img/xiaogong.jpg' -Quiet
```

Expected: the first command identifies native effect resources; both path checks return `True`.

- [ ] **Step 2: Inspect the desktop local page in an isolated browser session**

Run `npx hexo server`, then use `agent-browser` with a unique session to open `http://localhost:4000/blog/`. Capture an interactive snapshot and a screenshot outside version control. Verify the title, five navigation links, no `Follow Me` control, native theme controls, and readable header/content contrast over the blue background.

- [ ] **Step 3: Publish main and wait for the matching Pages workflow**

Run:

```powershell
Set-Location D:\\project\\hexo-modern
git push origin main
$mainSha = git rev-parse main
$run = gh run list --repo QuAnnan2019/blog --workflow deploy-pages.yml --branch main --commit $mainSha --limit 1 --json databaseId,status,conclusion,headSha | ConvertFrom-Json | Select-Object -First 1
if (-not $run -or $run.headSha -ne $mainSha) { throw 'Expected Pages run was not found for the new main commit.' }
gh run watch $run.databaseId --repo QuAnnan2019/blog --exit-status
```

Expected: the matching Pages workflow exits successfully.

- [ ] **Step 4: Verify the published homepage**

Run:

```powershell
$response = Invoke-WebRequest -UseBasicParsing https://quannan2019.github.io/blog/
"status=$($response.StatusCode)"
"hasTitle=$($response.Content -match '<title>你好吖~ - coding</title>')"
"hasCover=$($response.Content -match '/blog/img/fengmian.png')"
"hasAvatar=$($response.Content -match '/blog/img/xiaogong.jpg')"
git -C D:\\project\\hexo-modern status --short
```

Expected: `status=200`, all content checks return `True`, and no tracked changes remain.
