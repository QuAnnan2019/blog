# Hexo GitHub Pages Deployment Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the published Hugo output at `https://quannan2019.github.io/blog/` with the current Hexo + Butterfly site while preserving the old remote source on a backup branch.

**Architecture:** GitHub Actions builds the source repository on pushes to `main`, checking out the pinned Butterfly submodule and uploading Hexo's `public/` directory to Pages. The remote `main` history is first copied to `hugo-backup-20260916`; the new project's independent history is then pushed with an explicit force-with-lease.

**Tech Stack:** GitHub Actions, GitHub Pages, Node.js 24, npm lockfile, Hexo 8.1.2, Butterfly 5.7.0, GitHub CLI, PowerShell.

---

## File Structure

- Create: `D:\\project\\hexo-modern\\.github\\workflows\\deploy-pages.yml` — builds and deploys the static site from `main`.
- Modify: `D:\\project\\hexo-modern\\docs\\superpowers\\plans\\2026-09-16-hexo-pages-deployment.md` — records completion and any verified operational result.
- Modify: GitHub remote `QuAnnan2019/blog` — adds `hugo-backup-20260916` and replaces `main` only after backup verification.

### Task 1: Verify Local Build and Existing Pages State

**Files:**
- Modify: none.

- [ ] **Step 1: Build the source from a clean local state**

Run from `D:\\project\\hexo-modern`:

```powershell
npx hexo clean
npx hexo generate
Test-Path .\\public\\index.html
Select-String -Path .\\public\\index.html -Pattern '/blog/' -Quiet
```

Expected: generation succeeds, and both checks return `True`.

- [ ] **Step 2: Verify current Pages configuration before changing Git history**

Run:

```powershell
gh api repos/QuAnnan2019/blog/pages --jq '.build_type + " " + .html_url'
git ls-remote --symref https://github.com/QuAnnan2019/blog.git HEAD
```

Expected: Pages reports `workflow https://quannan2019.github.io/blog/` and the current default branch is `main`. Stop if Pages does not use the workflow build type.

### Task 2: Add the Pages Deployment Workflow

**Files:**
- Create: `D:\\project\\hexo-modern\\.github\\workflows\\deploy-pages.yml`

- [ ] **Step 1: Create the workflow with the minimal Pages permissions**

Create `D:\\project\\hexo-modern\\.github\\workflows\\deploy-pages.yml` with:

```yaml
name: Deploy Hexo site to Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
      - name: Set up Node
        uses: actions/setup-node@v4
        with:
          node-version: 24
          cache: npm
      - name: Install dependencies
        run: npm ci
      - name: Build site
        run: npx hexo generate
      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: public

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

Expected: the workflow checks out the Butterfly submodule, uses the lockfile, and uploads only generated static output.

- [ ] **Step 2: Validate the workflow source and commit it**

Run:

```powershell
git diff --check
git add .github/workflows/deploy-pages.yml
git commit -m "ci: deploy Hexo site to Pages"
git show --check --stat --oneline HEAD
```

Expected: the commit contains exactly the workflow file with no whitespace errors.

### Task 3: Back Up the Current Remote and Replace `main`

**Files:**
- Modify: Git refs in `https://github.com/QuAnnan2019/blog.git`.

- [ ] **Step 1: Add the GitHub repository as this project's remote**

Run from `D:\\project\\hexo-modern`:

```powershell
git remote add origin https://github.com/QuAnnan2019/blog.git
git remote -v
```

Expected: both fetch and push URLs are `https://github.com/QuAnnan2019/blog.git`.

- [ ] **Step 2: Preserve the existing remote main commit**

Run:

```powershell
git fetch origin main
$remoteMainSha = git rev-parse origin/main
if (-not $remoteMainSha) { throw 'Remote main was not found; refusing replacement.' }
git push origin origin/main:refs/heads/hugo-backup-20260916
$backupSha = (git ls-remote origin refs/heads/hugo-backup-20260916).Split("`t")[0]
if ($backupSha -ne $remoteMainSha) { throw 'Backup SHA does not match remote main; refusing replacement.' }
"backupSha=$backupSha"
```

Expected: the backup branch resolves to exactly `$remoteMainSha`. Stop if the SHA differs or the push fails.

- [ ] **Step 3: Point the local main branch at the verified Hexo commit**

Run:

```powershell
git branch -f main codex/hexo-butterfly-bootstrap
git rev-parse main
git log --oneline -1 main
```

Expected: local `main` resolves to the commit containing `ci: deploy Hexo site to Pages`.

- [ ] **Step 4: Replace remote main with force-with-lease**

Run:

```powershell
git push --force-with-lease="refs/heads/main:$remoteMainSha" origin main:main
git ls-remote origin refs/heads/main
```

Expected: the remote `main` SHA matches local `main`. If the lease is rejected, do not retry with `--force`; fetch and report the changed remote SHA.

### Task 4: Verify GitHub Deployment and Published Site

**Files:**
- Modify: none.

- [ ] **Step 1: Find the workflow run triggered by the new main commit**

Run:

```powershell
$mainSha = git -C D:\\project\\hexo-modern rev-parse main
$run = gh run list --repo QuAnnan2019/blog --workflow deploy-pages.yml --branch main --commit $mainSha --limit 1 --json databaseId,status,conclusion,url,headSha | ConvertFrom-Json | Select-Object -First 1
if (-not $run -or $run.headSha -ne $mainSha) { throw 'Expected Pages run was not found for the new main commit.' }
$run
```

Expected: one run has `headSha` equal to `$mainSha`.

- [ ] **Step 2: Wait for deployment completion**

Run with the `$run` object retained from Step 1:

```powershell
gh run watch $run.databaseId --repo QuAnnan2019/blog --exit-status
```

Expected: command exits 0 and reports a successful workflow.

- [ ] **Step 3: Verify the published homepage**

Run:

```powershell
$response = Invoke-WebRequest -UseBasicParsing https://quannan2019.github.io/blog/
"status=$($response.StatusCode)"
"hasTitle=$($response.Content -match '<title>个人博客</title>')"
"hasBasePath=$($response.Content -match '/blog/')"
```

Expected: `status=200`, `hasTitle=True`, and `hasBasePath=True`.

- [ ] **Step 4: Confirm the final repository state**

Run:

```powershell
git -C D:\\project\\hexo-modern status --short
git -C D:\\project\\hexo-modern log --oneline -5
gh api repos/QuAnnan2019/blog/pages --jq '.build_type + " " + .html_url'
```

Expected: local worktree is clean and Pages still reports `workflow https://quannan2019.github.io/blog/`.
