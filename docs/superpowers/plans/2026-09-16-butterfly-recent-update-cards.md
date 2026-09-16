# Butterfly Recent Update Cards Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the homepage text list with responsive, alternating Butterfly-inspired update cards with optional page covers.

**Architecture:** `layouts/home.html` retains sorting of the combined `posts` and `notes` pages. One card partial resolves section labels, optional `featuredImage`, plain summaries, and two tag terms. Project-level Sass owns layout and theme adaptation.

**Tech Stack:** Hugo 0.166 extended, Go templates, Sass, LoveIt, Hugo taxonomies.

---

## File Structure

- Modify: `D:/project/hugo/layouts/home.html` - invoke a card partial for each update.
- Create: `D:/project/hugo/layouts/partials/home/recent-update-card.html` - render one page card.
- Modify: `D:/project/hugo/assets/css/_custom.scss` - add card and breakpoint styles.
- Modify: `D:/project/hugo/docs/theme-source.md` - record the cover image Front Matter contract.

### Task 1: Render Update Cards

**Files:**
- Create: `D:/project/hugo/layouts/partials/home/recent-update-card.html`
- Modify: `D:/project/hugo/layouts/home.html`

- [ ] **Step 1: Create the card partial**

```go-html-template
{{- $page := .page -}}
{{- $index := .index -}}
{{- $kind := "博客" -}}
{{- $kindClass := "post" -}}
{{- if eq $page.Section "notes" -}}{{- $kind = "笔记" -}}{{- $kindClass = "note" -}}{{- end -}}
{{- $featuredImage := $page.Params.featuredImage -}}
{{- $tags := $page.GetTerms "tags" -}}
{{- $cardClass := "recent-update-card" -}}
{{- if modBool $index 2 -}}{{- $cardClass = printf "%s recent-update-card--cover-right" $cardClass -}}{{- end -}}
<article class="{{ $cardClass }}">
  {{- with $featuredImage -}}
    <a class="recent-update-cover" href="{{ $page.RelPermalink }}" aria-label="阅读：{{ $page.Title }}"><img src="{{ . | relURL }}" alt="{{ $page.Title }} 的封面" loading="lazy"></a>
  {{- else -}}
    <a class="recent-update-cover recent-update-cover--fallback recent-update-cover--{{ $kindClass }}" href="{{ $page.RelPermalink }}" aria-label="阅读：{{ $page.Title }}"><span>{{ $kind }}</span></a>
  {{- end -}}
  <div class="recent-update-copy">
    <div class="recent-update-meta"><span class="recent-update-kind">{{ $kind }}</span><time datetime="{{ $page.Date.Format "2006-01-02" }}">{{ $page.Date.Format "2006-01-02" }}</time></div>
    <h3><a href="{{ $page.RelPermalink }}">{{ $page.Title }}</a></h3>
    {{- with $page.Summary | plainify | htmlUnescape | truncate 130 -}}<p class="recent-update-summary">{{ . }}</p>{{- end -}}
    {{- with $tags -}}<div class="recent-update-tags" aria-label="文章标签">{{- range first 2 . -}}<a href="{{ .RelPermalink }}">{{ .LinkTitle }}</a>{{- end -}}</div>{{- end -}}
  </div>
</article>
```

- [ ] **Step 2: Replace the inline list body in `layouts/home.html`**

```go-html-template
      <div class="recent-updates-list">
        {{- range $index, $update := first 6 $updates -}}
          {{ partial "home/recent-update-card.html" (dict "page" $update "index" $index) }}
        {{- else -}}
          <p class="recent-updates-empty">新的内容正在整理中。</p>
        {{- end -}}
      </div>
```

- [ ] **Step 3: Verify rendered cards and GitHub Pages paths**

Run `hugo --destination public` followed by:

```powershell
rg -n "recent-update-card|recent-update-cover--fallback|recent-update-tags" public/index.html
rg -n 'href="/blog/(posts|notes|tags)/' public/index.html
```

Expected: Hugo exits `0`; both current pages have fallback covers and all card,
article, and tag links start below `/blog/`.

- [ ] **Step 4: Commit the template work**

```powershell
git add layouts/home.html layouts/partials/home/recent-update-card.html
git commit -m "feat: add alternating recent update cards"
```

### Task 2: Style Desktop Alternation And Mobile Stacking

**Files:**
- Modify: `D:/project/hugo/assets/css/_custom.scss`

- [ ] **Step 1: Replace the old `.recent-update-item` rule with card styles**

```scss
.recent-updates-list { display: grid; gap: 1rem; }
.recent-update-card { background: $global-background-color; border: 1px solid $global-border-color; border-radius: .45rem; display: grid; grid-template-columns: minmax(12rem, 42%) minmax(0, 1fr); min-height: 12.5rem; overflow: hidden; [theme=dark] & { background: $global-background-color-dark; border-color: $global-border-color-dark; } }
.recent-update-card--cover-right .recent-update-cover { order: 2; }
.recent-update-card--cover-right .recent-update-copy { order: 1; }
.recent-update-cover { background: $global-border-color; display: block; min-height: 100%; overflow: hidden; img { display: block; height: 100%; object-fit: cover; width: 100%; } }
.recent-update-cover--fallback { align-items: center; display: flex; justify-content: center; span { border: 1px solid currentColor; font-size: .875rem; padding: .35rem .65rem; } }
.recent-update-cover--note { background: #dbeaf4; color: #407a9f; }
.recent-update-cover--post { background: #f3e8df; color: #9b6b49; }
[theme=dark] .recent-update-cover--note { background: #263744; color: #9bc7df; }
[theme=dark] .recent-update-cover--post { background: #44342c; color: #dfba9b; }
.recent-update-copy { align-content: center; display: grid; padding: 1.4rem 1.5rem; }
.recent-update-copy h3 { font-size: 1.15rem; line-height: 1.4; margin: .35rem 0; }
.recent-update-summary { color: $global-font-secondary-color; line-height: 1.65; margin: .25rem 0 0; [theme=dark] & { color: $global-font-secondary-color-dark; } }
.recent-update-tags { display: flex; flex-wrap: wrap; gap: .45rem; margin-top: .8rem; a { border: 1px solid $global-border-color; font-size: .75rem; padding: .1rem .45rem; [theme=dark] & { border-color: $global-border-color-dark; } } }
```

- [ ] **Step 2: Add this inside the existing 680px media query**

```scss
  .recent-update-card { grid-template-columns: 1fr; }
  .recent-update-card--cover-right .recent-update-cover,
  .recent-update-card--cover-right .recent-update-copy { order: initial; }
  .recent-update-cover { aspect-ratio: 16 / 7; min-height: 0; }
  .recent-update-copy { padding: 1.15rem 1.2rem; }
```

- [ ] **Step 3: Check the compiled result**

```powershell
hugo --minify --gc --destination public
rg -n "recent-update-card--cover-right|recent-update-cover--note|recent-update-cover--post" public/css/style.min.css
rg -n "recent-update-card--cover-right|recent-update-cover--fallback" public/index.html
```

Expected: Hugo exits `0`; CSS contains both fallback variants; the second card
has the alternating cover-right class.

- [ ] **Step 4: Commit the card styles**

```powershell
git add assets/css/_custom.scss
git commit -m "style: add Butterfly-inspired update cards"
```

### Task 3: Document Covers And Verify The Page

**Files:**
- Modify: `D:/project/hugo/docs/theme-source.md`

- [ ] **Step 1: Append this documentation**

````markdown
## 首页文章封面

首页最近更新卡片会读取文章或笔记 Front Matter 的 `featuredImage`：

```yaml
featuredImage: "images/posts/your-cover.jpg"
```

图片放在 `static/images/posts/` 后，首页会生成 `/blog/images/posts/your-cover.jpg`。
未填写 `featuredImage` 时，博客和笔记会显示本地 CSS 占位封面，不请求外部图片。
````

- [ ] **Step 2: Run layout checks**

Run `hugo server --disableFastRender --baseURL "http://localhost:54807/blog/" --port 54807`.
At desktop width, verify adjacent cards have covers on opposite sides. At 390px,
verify every card stacks cover above copy and the page has no horizontal overflow.

- [ ] **Step 3: Run the final build, commit, and push**

```powershell
hugo --minify --gc --destination public
git add docs/theme-source.md
git commit -m "docs: describe homepage cover images"
git -c http.version=HTTP/1.1 push
```

Expected: the production build exits `0`; the three commits are pushed to
`main`; the existing GitHub Pages workflow deploys the card layout.
