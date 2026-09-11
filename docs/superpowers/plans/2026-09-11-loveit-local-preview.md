# LoveIt Local Preview Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a locally previewable Hugo site in `D:\project\hugo` using LoveIt, with separate posts and notes sections.

**Architecture:** Content and configuration live in the repository; the upstream LoveIt source remains a pinned Git submodule at `themes/LoveIt`. Hugo reads `hugo.toml` and Markdown files under `content/`, creates static HTML, and serves it locally for review.

**Tech Stack:** Hugo Extended, LoveIt, Markdown, TOML front matter, Git submodules.

---

## File Structure

- Create: `D:\project\hugo\.gitignore` - ignores generated Hugo output.
- Create: `D:\project\hugo\hugo.toml` - theme, navigation, search, and render configuration.
- Create: `D:\project\hugo\content\_index.md` - homepage metadata.
- Create: `D:\project\hugo\content\posts\_index.md` - Posts section metadata.
- Create: `D:\project\hugo\content\posts\welcome-to-my-digital-garden.md` - sample article.
- Create: `D:\project\hugo\content\notes\_index.md` - Notes section metadata.
- Create: `D:\project\hugo\content\notes\markdown-writing-workflow.md` - sample knowledge note.
- Create: `D:\project\hugo\content\about\_index.md` - About page.
- Create: `D:\project\hugo\themes\LoveIt` - Git submodule, not authored source.
- Create: `D:\project\hugo\.gitmodules` - submodule URL and path.

### Task 1: Install and Verify Hugo Extended

**Files:**
- Modify: none
- Test: `hugo version`

- [ ] **Step 1: Check the installed Hugo build**

Run `hugo version`.

Expected: output contains a version and `extended`. If `hugo` is not found, continue to Step 2.

- [ ] **Step 2: Install Hugo Extended**

Run `winget install --id Hugo.Hugo.Extended --exact --source winget --accept-package-agreements --accept-source-agreements`.

Expected: Winget reports a successful installation. Start a new PowerShell process if it updates `PATH`.

- [ ] **Step 3: Verify the installed distribution**

Run `hugo version`.

Expected: an `extended` Hugo version is reported. LoveIt requires it to compile SCSS assets.

### Task 2: Add LoveIt as a Pinned Git Submodule

**Files:**
- Create: `D:\project\hugo\.gitmodules`
- Create: `D:\project\hugo\themes\LoveIt`
- Test: `git submodule status`

- [ ] **Step 1: Add the upstream theme at the dedicated path**

Run `git -C D:\project\hugo submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt`.

Expected: Git clones the repository and creates `.gitmodules` with a `themes/LoveIt` entry.

- [ ] **Step 2: Confirm the exact pinned theme revision**

Run `git -C D:\project\hugo submodule status`, then run `git -C D:\project\hugo\themes\LoveIt rev-parse --short HEAD`.

Expected: the first command begins with a 40-character commit ID and the second reports its matching short ID.

- [ ] **Step 3: Commit the theme pin**

Run `git -C D:\project\hugo add .gitmodules themes/LoveIt`, then run `git -C D:\project\hugo commit -m "chore: add LoveIt theme"`.

Expected: a commit includes `.gitmodules` and the submodule gitlink.

### Task 3: Configure Hugo and LoveIt

**Files:**
- Create: `D:\project\hugo\.gitignore`
- Create: `D:\project\hugo\hugo.toml`
- Test: `hugo --gc --minify`

- [ ] **Step 1: Add generated-output ignore rules**

Create `D:\project\hugo\.gitignore`:

```gitignore
/public/
/resources/
/.hugo_build.lock
.DS_Store
Thumbs.db
```

- [ ] **Step 2: Add the complete initial site configuration**

Create `D:\project\hugo\hugo.toml`:

```toml
baseURL = "http://localhost:1313/"
languageCode = "en"
defaultContentLanguage = "en"
title = "My Knowledge Garden"
theme = "LoveIt"
enableRobotsTXT = true
enableEmoji = true

[pagination]
  pagerSize = 10

[taxonomies]
  category = "categories"
  tag = "tags"
  series = "series"

[outputs]
  home = ["HTML", "RSS", "JSON"]
  section = ["HTML", "RSS"]
  taxonomy = ["HTML", "RSS"]

[[menu.main]]
  identifier = "posts"
  name = "Posts"
  url = "/posts/"
  weight = 10

[[menu.main]]
  identifier = "notes"
  name = "Notes"
  url = "/notes/"
  weight = 20

[[menu.main]]
  identifier = "archives"
  name = "Archives"
  url = "/archives/"
  weight = 30

[[menu.main]]
  identifier = "tags"
  name = "Tags"
  url = "/tags/"
  weight = 40

[[menu.main]]
  identifier = "about"
  name = "About"
  url = "/about/"
  weight = 50

[params]
  defaultTheme = "auto"
  dateFormat = "2006-01-02"
  title = "My Knowledge Garden"
  description = "A personal collection of essays and notes."

  [params.header]
    desktopMode = "fixed"
    mobileMode = "auto"
    [params.header.title]
      name = "My Knowledge Garden"
      typeit = false

  [params.home]
    [params.home.profile]
      enable = true
      title = "My Knowledge Garden"
      subtitle = "Essays, notes, and things worth keeping."
      typeit = false
      social = false
    [params.home.posts]
      enable = true
      paginate = 6

  [params.search]
    enable = true
    type = "fuse"
    contentLength = 4000
    maxResultLength = 10
    snippetLength = 30

  [params.page]
    linkToMarkdown = true
    [params.page.toc]
      enable = true
      keepStatic = false
      auto = true
    [params.page.code]
      copy = true

[markup]
  [markup.goldmark]
    [markup.goldmark.renderer]
      unsafe = true
  [markup.highlight]
    noClasses = false
```

- [ ] **Step 3: Run the configuration integration check**

Run `Set-Location D:\project\hugo; hugo --gc --minify`.

Expected: Hugo exits with status 0 and produces `public/`. It may report no regular pages until Task 4 creates content.

- [ ] **Step 4: Commit configuration**

Run `git -C D:\project\hugo add .gitignore hugo.toml`, then run `git -C D:\project\hugo commit -m "feat: configure LoveIt site"`.

Expected: one commit includes the configuration and ignore rules.

### Task 4: Add Initial Blog and Knowledge Content

**Files:**
- Create: `D:\project\hugo\content\_index.md`
- Create: `D:\project\hugo\content\posts\_index.md`
- Create: `D:\project\hugo\content\posts\welcome-to-my-digital-garden.md`
- Create: `D:\project\hugo\content\notes\_index.md`
- Create: `D:\project\hugo\content\notes\markdown-writing-workflow.md`
- Create: `D:\project\hugo\content\about\_index.md`
- Test: `hugo list all` and `hugo --gc --minify`

- [ ] **Step 1: Add section metadata files**

Create `D:\project\hugo\content\_index.md`:

```markdown
---
title: "Home"
---
```

Create `D:\project\hugo\content\posts\_index.md`:

```markdown
---
title: "Posts"
description: "Long-form writing and finished ideas."
---
```

Create `D:\project\hugo\content\notes\_index.md`:

```markdown
---
title: "Notes"
description: "Working knowledge, references, and durable ideas."
---
```

- [ ] **Step 2: Add a sample post**

Create `D:\project\hugo\content\posts\welcome-to-my-digital-garden.md`:

```markdown
---
title: "Welcome to My Digital Garden"
date: 2026-09-11T09:00:00+08:00
draft: false
categories: ["Writing"]
tags: ["welcome", "knowledge-management"]
series: ["Site Notes"]
---

This is where finished thoughts live alongside the notes that produced them.

## What belongs here

Posts are for ideas that are ready to share. They can be essays, project write-ups, or useful explanations that deserve a complete narrative.

## How the garden grows

Notes do not need to be complete before they are useful. A short note can become a post when it has enough context, examples, and a point of view.
```

- [ ] **Step 3: Add a sample note**

Create `D:\project\hugo\content\notes\markdown-writing-workflow.md`:

```markdown
---
title: "A Markdown Writing Workflow"
date: 2026-09-11T10:00:00+08:00
lastmod: 2026-09-11T10:00:00+08:00
draft: false
categories: ["Knowledge Base"]
tags: ["markdown", "writing", "workflow"]
series: ["Writing Systems"]
---

Use a small loop: capture, clarify, connect, and publish.

## Capture

Write the smallest useful version of an idea as soon as it appears.

## Clarify

Give each note a title, a few tags, and enough context for your future self.

## Connect

Link related notes with shared tags or a series before expanding the idea into a post.
```

- [ ] **Step 4: Add an About page**

Create `D:\project\hugo\content\about\_index.md`:

```markdown
---
title: "About"
---

This is a personal space for essays, technical notes, and ideas worth revisiting.
```

- [ ] **Step 5: Validate content discovery and static generation**

Run `Set-Location D:\project\hugo; hugo list all; hugo --gc --minify`.

Expected: `hugo list all` reports the sample post and note with `draft` false. The build exits 0 and renders section, taxonomy, and article pages.

- [ ] **Step 6: Commit initial content**

Run `git -C D:\project\hugo add content`, then run `git -C D:\project\hugo commit -m "feat: add initial posts and notes"`.

Expected: one commit contains the initial content files.

### Task 5: Preview and Inspect Locally

**Files:**
- Modify: none
- Test: browser inspection on port 1313

- [ ] **Step 1: Start the local Hugo server**

Run `Set-Location D:\project\hugo; hugo server --buildDrafts --disableFastRender`.

Expected: Hugo reports `Web Server is available at http://localhost:1313/` and watches for content/configuration changes.

- [ ] **Step 2: Inspect the rendered site**

Open these paths in a browser:

```text
http://localhost:1313/
http://localhost:1313/posts/
http://localhost:1313/notes/
http://localhost:1313/posts/welcome-to-my-digital-garden/
http://localhost:1313/notes/markdown-writing-workflow/
http://localhost:1313/tags/
http://localhost:1313/about/
```

Expected: LoveIt navigation renders; Posts, Notes, Tags, and About are reachable; the sample post and note show taxonomy metadata; article headings appear in the table of contents.

- [ ] **Step 3: Confirm generated output is ignored and commits are clean**

Run `git -C D:\project\hugo status --short`.

Expected: no `public/`, `resources/`, or `.hugo_build.lock` entry appears, and the worktree is clean.

## Final Verification

- [ ] `hugo version` reports an Extended build.
- [ ] `git -C D:\project\hugo submodule status` reports a LoveIt commit ID.
- [ ] `hugo --gc --minify` exits 0.
- [ ] The six local URLs in Task 5 load without broken layout or missing navigation.
- [ ] `git -C D:\project\hugo status --short` is empty after commits.
