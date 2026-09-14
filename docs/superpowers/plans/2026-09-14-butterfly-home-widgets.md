# Butterfly Home Widgets Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add Hugo-native, Butterfly-inspired announcement, site-data, and tag-cloud widgets between the profile hero and recent updates.

**Architecture:** `layouts/home.html` remains the composition root. A single Hugo partial computes post, note, and taxonomy data at build time, and `assets/css/_custom.scss` supplies responsive styles using LoveIt variables.

**Tech Stack:** Hugo 0.166 extended, Go templates, Sass, LoveIt, Hugo taxonomies.

---

## File Structure

- Modify: `D:/project/hugo/hugo.toml` - editable Chinese announcement text.
- Create: `D:/project/hugo/layouts/partials/home/butterfly-widgets.html` - widget markup and Hugo-derived data.
- Modify: `D:/project/hugo/layouts/home.html` - insert the widget partial after the profile hero.
- Modify: `D:/project/hugo/assets/css/_custom.scss` - card surfaces and responsive layout.
- Modify: `D:/project/hugo/content/posts/welcome-to-my-digital-garden.md` - supply a third unique tag for visual verification.

### Task 1: Add Widget Inputs

**Files:**
- Modify: `D:/project/hugo/hugo.toml`
- Modify: `D:/project/hugo/content/posts/welcome-to-my-digital-garden.md`

- [ ] **Step 1: Add the editable homepage announcement**

Add this TOML table inside `[params.home]`, after `profile` and before `posts`:

```toml
    [params.home.widgets]
      announcement = "欢迎来到安南的博客，记录技术、思考与持续生长的知识。"
```

- [ ] **Step 2: Add a third distinct tag to the introductory post**

Replace its current tag line with:

```yaml
tags: ["欢迎", "知识管理", "博客"]
```

- [ ] **Step 3: Confirm Hugo reads the configured value**

Run:

```powershell
hugo config | Select-String -Pattern "announcement"
```

Expected: output contains the Chinese announcement value.

- [ ] **Step 4: Commit the inputs**

```powershell
git add hugo.toml content/posts/welcome-to-my-digital-garden.md
git commit -m "feat: configure homepage widget content"
```

### Task 2: Build The Widget Partial

**Files:**
- Create: `D:/project/hugo/layouts/partials/home/butterfly-widgets.html`
- Modify: `D:/project/hugo/layouts/home.html`

- [ ] **Step 1: Create the widget partial**

Create `layouts/partials/home/butterfly-widgets.html` with:

```go-html-template
{{- $posts := where .Site.RegularPages "Section" "posts" -}}
{{- $notes := where .Site.RegularPages "Section" "notes" -}}
{{- $tags := .Site.Taxonomies.tags -}}
{{- $announcement := .Site.Params.home.widgets.announcement | default "欢迎来到这里。" -}}

<section class="home-widgets" aria-label="站点信息">
  <article class="home-widget home-widget-announcement">
    <h2 class="home-widget-title"><i class="fas fa-bullhorn" aria-hidden="true"></i><span>公告</span></h2>
    <p>{{ $announcement }}</p>
  </article>
  <div class="home-widget-grid">
    <article class="home-widget home-widget-data">
      <h2 class="home-widget-title"><i class="fas fa-chart-bar" aria-hidden="true"></i><span>站点数据</span></h2>
      <nav class="home-widget-stats" aria-label="内容统计">
        <a href="{{ "posts/" | relURL }}"><span>博客</span><strong>{{ len $posts }}</strong></a>
        <a href="{{ "notes/" | relURL }}"><span>笔记</span><strong>{{ len $notes }}</strong></a>
        <a href="{{ "tags/" | relURL }}"><span>标签</span><strong>{{ len $tags }}</strong></a>
      </nav>
    </article>
    {{- with $tags -}}
      <article class="home-widget home-widget-tags">
        <h2 class="home-widget-title"><i class="fas fa-tags" aria-hidden="true"></i><span>标签</span></h2>
        <div class="home-widget-tag-cloud">
          {{- range .ByCount -}}
            <a href="{{ .Page.RelPermalink }}" title="{{ .Page.Title }}：{{ .Count }} 篇内容">{{ .Page.Title }}</a>
          {{- end -}}
        </div>
      </article>
    {{- end -}}
  </div>
</section>
```

- [ ] **Step 2: Include it after the hero**

In `layouts/home.html`, add this directly after `</section>` closing
`.profile-hero`, before the `recent-updates` section:

```go-html-template
    {{ partial "home/butterfly-widgets.html" . }}
```

- [ ] **Step 3: Check the rendered markup and Pages paths**

Run:

```powershell
hugo --destination public
rg -n "公告|站点数据|欢迎来到安南的博客|/blog/(posts|notes|tags)/" public/index.html
```

Expected: a successful build; `public/index.html` contains the three headings,
the announcement text, and all three `/blog/` target paths.

- [ ] **Step 4: Commit the template work**

```powershell
git add layouts/home.html layouts/partials/home/butterfly-widgets.html
git commit -m "feat: add homepage information widgets"
```

### Task 3: Style, Verify, And Publish

**Files:**
- Modify: `D:/project/hugo/assets/css/_custom.scss`

- [ ] **Step 1: Add the widget layout and card styles**

Insert this before `.recent-updates`:

```scss
.home-widgets { margin: 0 auto; max-width: 46rem; padding: 0 1.5rem; }
.home-widget-grid { display: grid; gap: 1rem; grid-template-columns: minmax(0, 1fr) minmax(0, 1.25fr); margin-top: 1rem; }
.home-widget { background: $global-background-color; border: 1px solid $global-border-color; border-radius: .45rem; box-shadow: 0 .25rem .85rem rgba(0, 0, 0, .04); padding: 1.1rem 1.2rem; [theme=dark] & { background: $global-background-color-dark; border-color: $global-border-color-dark; box-shadow: 0 .25rem .85rem rgba(0, 0, 0, .18); } }
.home-widget-title { align-items: center; display: flex; font-size: 1rem; gap: .55rem; margin: 0 0 .75rem; i { color: $global-link-color; [theme=dark] & { color: $global-link-color-dark; } } }
.home-widget-announcement p { color: $global-font-secondary-color; line-height: 1.7; margin: 0; [theme=dark] & { color: $global-font-secondary-color-dark; } }
.home-widget-stats { display: grid; gap: .75rem; grid-template-columns: repeat(3, minmax(0, 1fr)); a { color: inherit; display: grid; gap: .25rem; text-align: center; } span { color: $global-font-secondary-color; font-size: .8125rem; [theme=dark] & { color: $global-font-secondary-color-dark; } } strong { color: $global-link-color; font-size: 1.4rem; font-weight: 600; [theme=dark] & { color: $global-link-color-dark; } } }
.home-widget-tag-cloud { display: flex; flex-wrap: wrap; gap: .55rem .7rem; a { font-size: .875rem; } }
@media (max-width: 680px) { .home-widgets { padding: 0 1.25rem; } .home-widget-grid { grid-template-columns: 1fr; } }
```

- [ ] **Step 2: Build and validate generated output**

Run:

```powershell
hugo --minify --gc --destination public
$css = Get-ChildItem public/css -Filter '*.css' | Select-Object -First 1
rg -n "home-widgets|home-widget-tag-cloud" $css.FullName
rg -n "公告|站点数据|标签|/blog/posts/|/blog/notes/|/blog/tags/" public/index.html
```

Expected: Hugo exits `0`; generated CSS includes both selectors; rendered HTML
includes all labels and all three base-path-aware navigation targets.

- [ ] **Step 3: Run visual checks in both target layouts**

Run:

```powershell
hugo server --disableFastRender --baseURL "http://localhost:1313/blog/"
```

Inspect `http://localhost:1313/blog/` at desktop width and at 680px or below.
Expected: announcement is full-width, data and tag cards share a row on desktop
and stack on mobile, and both themes retain readable contrast and clickable
links.

- [ ] **Step 4: Commit and push after the checks pass**

```powershell
git add assets/css/_custom.scss
git commit -m "style: add Butterfly-inspired homepage widgets"
git -c http.version=HTTP/1.1 push
```

Expected: three focused commits are present locally and `main` pushes to
`origin`, allowing the existing GitHub Actions workflow to deploy the site.
