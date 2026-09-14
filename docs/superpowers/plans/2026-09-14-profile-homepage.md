# Profile Homepage Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the default home content flow with a full-height personal-card hero followed by a mixed recent-updates list.

**Architecture:** Override LoveIt's `home.html` from the site-level `layouts/` directory, while preserving the theme's base template, header, footer, search, and theme toggle. Add a project-level Sass partial that LoveIt's existing stylesheet imports automatically. The home template derives title, subtitle, and avatar from the current profile configuration and computes recent content from published `posts` and `notes` pages.

**Tech Stack:** Hugo Extended 0.166.0, LoveIt, Hugo templates, Sass, TOML, GitHub Pages.

---

## File Structure

- Create: `D:\project\hugo\layouts\home.html` - site-level home template override.
- Create: `D:\project\hugo\assets\css\_custom.scss` - scoped hero and updates-list styles.
- Modify: `D:\project\hugo\hugo.toml` - commit the user's selected header icon and profile subtitle.

### Task 1: Commit the Selected Personal-Card Copy

**Files:**
- Modify: `D:\project\hugo\hugo.toml`
- Test: TOML parsing via `hugo --gc --minify`

- [ ] **Step 1: Confirm the profile fields match the approved text**

Keep the existing profile title and ensure these fields are present:

```toml
[params.header.title]
  name = "📚"
  typeit = false

[params.home.profile]
  avatarURL = "images/avatar.png"
  title = "安南的博客"
  subtitle = "先相信，再看见。"
  typeit = false
  social = false
```

Do not change `baseURL`, `defaultTheme = "auto"`, or the existing Chinese navigation entries.

- [ ] **Step 2: Validate the configuration**

Run:

```powershell
Set-Location D:\project\hugo
hugo --gc --minify
```

Expected: Hugo exits with code 0.

- [ ] **Step 3: Commit only the profile-copy configuration**

Run:

```powershell
git add hugo.toml
git diff --cached --check
git commit -m "feat: refine profile copy"
```

Expected: the commit contains only `hugo.toml` and preserves the user's selected `📚` header title and `先相信，再看见。` subtitle.

### Task 2: Override the Home Template

**Files:**
- Create: `D:\project\hugo\layouts\home.html`
- Test: generated home HTML includes the hero and mixed content labels

- [ ] **Step 1: Create the personal-card home template**

Create `D:\project\hugo\layouts\home.html` with:

```html
{{- define "content" -}}
  {{- $profile := .Site.Params.home.profile -}}
  {{- $avatar := $profile.avatarURL -}}
  {{- $updates := where .Site.RegularPages "Section" "in" (slice "posts" "notes") -}}
  {{- $updates = $updates.ByDate.Reverse -}}

  <div class="profile-home">
    <section class="profile-hero" aria-labelledby="profile-home-title">
      <div class="profile-hero-content">
        {{- with $avatar -}}
          <img class="profile-hero-avatar" src="{{ . | relURL }}" alt="{{ $profile.title }} 的头像" width="192" height="192">
        {{- end -}}
        <h1 id="profile-home-title" class="profile-hero-title">{{ $profile.title }}</h1>
        {{- with $profile.subtitle -}}
          <p class="profile-hero-subtitle">{{ . }}</p>
        {{- end -}}
      </div>
      <a class="profile-hero-scroll" href="#recent-updates" aria-label="查看最近更新" title="查看最近更新">
        <i class="fas fa-chevron-down" aria-hidden="true"></i>
      </a>
    </section>

    <section id="recent-updates" class="recent-updates" aria-labelledby="recent-updates-title">
      <div class="recent-updates-heading">
        <p class="recent-updates-eyebrow">持续整理</p>
        <h2 id="recent-updates-title">最近更新</h2>
      </div>

      <div class="recent-updates-list">
        {{- range first 6 $updates -}}
          {{- $kind := "博客" -}}
          {{- if eq .Section "notes" -}}
            {{- $kind = "笔记" -}}
          {{- end -}}
          <article class="recent-update-item">
            <div class="recent-update-meta">
              <span class="recent-update-kind">{{ $kind }}</span>
              <time datetime="{{ .Date.Format "2006-01-02" }}">{{ .Date.Format "2006-01-02" }}</time>
            </div>
            <h3><a href="{{ .RelPermalink }}">{{ .Title }}</a></h3>
            {{- with .Summary | plainify | htmlUnescape | truncate 110 -}}
              <p>{{ . }}</p>
            {{- end -}}
          </article>
        {{- else -}}
          <p class="recent-updates-empty">新的内容正在整理中。</p>
        {{- end -}}
      </div>
    </section>
  </div>
{{- end -}}
```

- [ ] **Step 2: Build and inspect structural output**

Run:

```powershell
hugo --gc --minify
$siteHtml = Get-Content -Raw public/index.html
"HeroPresent=$($siteHtml -match 'profile-hero')"
"RecentUpdatesPresent=$($siteHtml -match 'recent-updates')"
"PostLabelPresent=$($siteHtml -match '>博客<')"
"NoteLabelPresent=$($siteHtml -match '>笔记<')"
```

Expected: Hugo exits with code 0 and all four checks output `True`.

- [ ] **Step 3: Commit the home template**

Run:

```powershell
git add layouts/home.html
git diff --cached --check
git commit -m "feat: add profile homepage layout"
```

Expected: one commit contains only `layouts/home.html`.

### Task 3: Add Responsive Home Styles

**Files:**
- Create: `D:\project\hugo\assets\css\_custom.scss`
- Test: Hugo Sass compilation through `hugo --gc --minify`

- [ ] **Step 1: Create the scoped Sass partial**

Create `D:\project\hugo\assets\css\_custom.scss` with:

```scss
html {
  scroll-behavior: smooth;
}

.profile-home {
  padding-bottom: 4rem;
}

.profile-hero {
  align-items: center;
  display: flex;
  flex-direction: column;
  justify-content: center;
  min-height: calc(100vh - 4rem);
  padding: 3rem 1.5rem 2rem;
  position: relative;
  text-align: center;
}

.profile-hero-content {
  max-width: 36rem;
}

.profile-hero-avatar {
  border-radius: 50%;
  box-shadow: 0 0 0 .35rem rgba(0, 0, 0, .05);
  height: 7rem;
  object-fit: cover;
  width: 7rem;
}

.profile-hero-title {
  font-size: 1.5rem;
  margin: 1.25rem 0 .5rem;
}

.profile-hero-subtitle {
  color: $global-font-secondary-color;
  font-size: 1rem;
  margin: 0;

  [theme=dark] & {
    color: $global-font-secondary-color-dark;
  }
}

.profile-hero-scroll {
  align-items: center;
  bottom: 1.5rem;
  color: $global-font-secondary-color;
  display: inline-flex;
  font-size: 1rem;
  height: 2.5rem;
  justify-content: center;
  position: absolute;
  width: 2.5rem;

  [theme=dark] & {
    color: $global-font-secondary-color-dark;
  }
}

.recent-updates {
  margin: 0 auto;
  max-width: 46rem;
  padding: 4rem 1.5rem 0;
}

.recent-updates-heading {
  border-bottom: 1px solid $global-border-color;
  margin-bottom: 1rem;
  padding-bottom: .75rem;

  [theme=dark] & {
    border-color: $global-border-color-dark;
  }

  h2 {
    font-size: 1.25rem;
    margin: 0;
  }
}

.recent-updates-eyebrow {
  color: $global-font-secondary-color;
  font-size: .8125rem;
  margin: 0 0 .25rem;

  [theme=dark] & {
    color: $global-font-secondary-color-dark;
  }
}

.recent-update-item {
  border-bottom: 1px dashed $global-border-color;
  padding: 1rem 0;

  [theme=dark] & {
    border-color: $global-border-color-dark;
  }

  h3 {
    font-size: 1.125rem;
    line-height: 1.4;
    margin: .3rem 0;
  }

  p {
    color: $global-font-secondary-color;
    line-height: 1.6;
    margin: .35rem 0 0;

    [theme=dark] & {
      color: $global-font-secondary-color-dark;
    }
  }
}

.recent-update-meta {
  color: $global-font-secondary-color;
  display: flex;
  font-size: .8125rem;
  gap: .75rem;

  [theme=dark] & {
    color: $global-font-secondary-color-dark;
  }
}

.recent-update-kind {
  color: $global-link-color;

  [theme=dark] & {
    color: $global-link-color-dark;
  }
}

.recent-updates-empty {
  color: $global-font-secondary-color;
  margin: 1.5rem 0;

  [theme=dark] & {
    color: $global-font-secondary-color-dark;
  }
}

@media (max-width: 680px) {
  .profile-hero {
    min-height: calc(100vh - 3.5rem);
    padding: 2.5rem 1.25rem 2rem;
  }

  .profile-hero-title {
    font-size: 1.375rem;
  }

  .recent-updates {
    padding: 3rem 1.25rem 0;
  }
}

@media (prefers-reduced-motion: reduce) {
  html {
    scroll-behavior: auto;
  }
}
```

- [ ] **Step 2: Compile the Sass and verify the output CSS contains the custom selector**

Run:

```powershell
hugo --gc --minify
$siteHtml = Get-Content -Raw public/index.html
"CustomStyleLinked=$($siteHtml -match 'css/style')"
```

Expected: Hugo exits with code 0 and `CustomStyleLinked=True`.

- [ ] **Step 3: Commit the styles**

Run:

```powershell
git add assets/css/_custom.scss
git diff --cached --check
git commit -m "style: add profile homepage presentation"
```

Expected: one commit contains only `assets/css/_custom.scss`.

### Task 4: Verify Local and Published Pages

**Files:**
- Modify: none
- Test: local HTTP routes, local responsive page inspection, GitHub Pages deployment

- [ ] **Step 1: Build and check all public routes**

Run:

```powershell
hugo --gc --minify
curl.exe -I http://127.0.0.1:9306/blog/
curl.exe -I http://127.0.0.1:9306/blog/posts/
curl.exe -I http://127.0.0.1:9306/blog/notes/
curl.exe -I http://127.0.0.1:9306/blog/tags/
curl.exe -I http://127.0.0.1:9306/blog/about/
```

Expected: Hugo exits with code 0 and every HTTP response includes `200 OK`. Start `hugo server --port 9306` first if it is not already running.

- [ ] **Step 2: Inspect desktop and mobile presentation**

Open `http://localhost:9306/blog/` in a browser at desktop width, then at 390px width. Confirm the profile hero fills the first view, avatar/title/subtitle do not overlap, the down-arrow is visible, and the update list starts below the hero. Use the theme toggle in both views and confirm text and borders retain readable contrast.

- [ ] **Step 3: Push the intended commits and wait for Pages**

Run:

```powershell
git status --short
git -c http.version=HTTP/1.1 push
gh run watch --repo QuAnnan2019/blog --exit-status
```

Expected: the worktree is clean before push and the Pages workflow concludes with `success`.

- [ ] **Step 4: Verify the public home page and routes**

Run:

```powershell
curl.exe -I https://quannan2019.github.io/blog/
curl.exe -I https://quannan2019.github.io/blog/posts/
curl.exe -I https://quannan2019.github.io/blog/notes/
curl.exe -I https://quannan2019.github.io/blog/tags/
curl.exe -I https://quannan2019.github.io/blog/about/
```

Expected: every response includes `HTTP/1.1 200 OK`.
