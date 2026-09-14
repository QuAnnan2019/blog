# GitHub Pages Deployment Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Publish the Hugo knowledge blog from `D:\project\hugo` publicly at `https://quannan2019.github.io/blog/` with GitHub Pages.

**Architecture:** The `main` branch of `QuAnnan2019/blog` holds Hugo source and the vendored LoveIt theme. GitHub Actions builds static output with Hugo Extended, uploads `public/` as the Pages artifact, then deploys that artifact through GitHub's official action. Generated output and deployment credentials are never committed.

**Tech Stack:** Hugo Extended 0.166.0, LoveIt, GitHub Actions, GitHub Pages, GitHub CLI.

---

## File Structure

- Modify: `D:\project\hugo\hugo.toml` - production base URL.
- Create: `D:\project\hugo\.github\workflows\deploy-pages.yml` - build and deploy workflow.
- External: `https://github.com/QuAnnan2019/blog` - set public and enable Pages with the Actions build type.

### Task 1: Configure the Production URL

**Files:**
- Modify: `D:\project\hugo\hugo.toml`
- Test: `hugo --gc --minify`

- [ ] **Step 1: Update the site base URL**

Replace the first line of `D:\project\hugo\hugo.toml` with:

```toml
baseURL = "https://quannan2019.github.io/blog/"
```

- [ ] **Step 2: Build using the production URL**

Run `Set-Location D:\project\hugo; hugo --gc --minify`.

Expected: Hugo exits with code 0, writes static output to `public/`, and generated links use `/blog/` as their base path.

- [ ] **Step 3: Commit the URL configuration**

Run `git -C D:\project\hugo add hugo.toml`, then run `git -C D:\project\hugo commit -m "chore: set production site URL"`.

Expected: one commit contains only the base URL change.

### Task 2: Add the GitHub Pages Workflow

**Files:**
- Create: `D:\project\hugo\.github\workflows\deploy-pages.yml`
- Test: local Hugo build

- [ ] **Step 1: Create the deployment workflow**

Create `D:\project\hugo\.github\workflows\deploy-pages.yml`:

```yaml
name: Deploy Hugo site to Pages

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
    env:
      HUGO_VERSION: 0.166.0
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Install Hugo Extended
        run: |
          wget -O "$RUNNER_TEMP/hugo.deb" "https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb"
          sudo dpkg -i "$RUNNER_TEMP/hugo.deb"

      - name: Configure Pages
        id: pages
        uses: actions/configure-pages@v5

      - name: Build site
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
        run: hugo --gc --minify --baseURL "${{ steps.pages.outputs.base_url }}/"

      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v5
```

- [ ] **Step 2: Validate the source still builds locally**

Run `Set-Location D:\project\hugo; hugo --gc --minify`.

Expected: Hugo exits with code 0 and `public/` remains ignored by Git.

- [ ] **Step 3: Commit the workflow**

Run `git -C D:\project\hugo add .github/workflows/deploy-pages.yml`, then run `git -C D:\project\hugo commit -m "ci: deploy Hugo site to Pages"`.

Expected: one commit adds the workflow file.

### Task 3: Publish Source and Enable Pages

**Files:**
- Modify: Git remote configuration for `D:\project\hugo`
- External: repository visibility and Pages configuration
- Test: GitHub repository and Pages API responses

- [ ] **Step 1: Make the target repository public**

Run `gh repo edit QuAnnan2019/blog --visibility public --accept-visibility-change-consequences`, then run `gh api repos/QuAnnan2019/blog --jq '.visibility'`.

Expected: the API output is `public`.

- [ ] **Step 2: Enable Pages for GitHub Actions deployments**

Run `gh api --method POST repos/QuAnnan2019/blog/pages -f build_type=workflow`.

Expected: the Pages API creates a site with `build_type` set to `workflow`. If it reports an existing Pages site, run `gh api --method PUT repos/QuAnnan2019/blog/pages -f build_type=workflow`.

- [ ] **Step 3: Add the empty repository as the source remote**

Run `git -C D:\project\hugo remote add origin https://github.com/QuAnnan2019/blog.git`, then run `git -C D:\project\hugo remote -v`.

Expected: `origin` fetch and push URLs both resolve to `https://github.com/QuAnnan2019/blog.git`.

- [ ] **Step 4: Push prepared source history to remote main**

Run `git -C D:\project\hugo push --set-upstream origin HEAD:main`.

Expected: Git reports that the local `codex/hugo-local-preview` branch now tracks `origin/main`; the push starts deployment.

### Task 4: Verify the Published Deployment

**Files:**
- Modify: none
- Test: GitHub Actions, Pages API, production HTTP responses

- [ ] **Step 1: Watch the Pages workflow run**

Run `gh run list --repo QuAnnan2019/blog --workflow "Deploy Hugo site to Pages" --limit 1`, then run `gh run watch --repo QuAnnan2019/blog --exit-status`.

Expected: the build and deploy jobs both conclude with `success`.

- [ ] **Step 2: Read the Pages URL from GitHub**

Run `gh api repos/QuAnnan2019/blog/pages --jq '.html_url'`.

Expected: `https://quannan2019.github.io/blog/`.

- [ ] **Step 3: Verify public routes**

Run `curl.exe -I https://quannan2019.github.io/blog/`, `curl.exe -I https://quannan2019.github.io/blog/posts/`, `curl.exe -I https://quannan2019.github.io/blog/notes/`, `curl.exe -I https://quannan2019.github.io/blog/tags/`, and `curl.exe -I https://quannan2019.github.io/blog/about/`.

Expected: each response includes HTTP 200.

- [ ] **Step 4: Confirm the source worktree is clean**

Run `git -C D:\project\hugo status --short`.

Expected: no output.
