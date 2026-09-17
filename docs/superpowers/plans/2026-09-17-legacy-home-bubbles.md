# Legacy Home Bubbles Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the homepage Canvas Nest effect with the legacy-style upward bubble animation.

**Architecture:** A small local browser script creates and animates one canvas only inside the full-screen homepage header. A local stylesheet defines its visual layer. Butterfly's existing `inject` setting loads both files and disables its Canvas Nest extension.

**Tech Stack:** Hexo 8.1.2, Butterfly 5.7.0, browser Canvas 2D API, YAML.

---

## File Structure

- Create: `D:\\project\\hexo-modern\\source\\js\\home-bubbles.js` - homepage-only Canvas animation.
- Create: `D:\\project\\hexo-modern\\source\\css\\home-bubbles.css` - canvas positioning and interaction guard.
- Modify: `D:\\project\\hexo-modern\\_config.butterfly.yml` - replace Canvas Nest with the two local assets.

### Task 1: Configure Local Assets

**Files:**
- Modify: `D:\\project\\hexo-modern\\_config.butterfly.yml`

- [ ] **Step 1: Replace Canvas Nest configuration**

Delete the `canvas_nest` block and replace the current `inject.head` list with the local bubble stylesheet. Add the local bubble script to `inject.bottom`:

```yaml
inject:
  head:
    - <link rel="stylesheet" href="/blog/css/home-bubbles.css">
  bottom:
    - <script defer src="/blog/js/home-bubbles.js"></script>
```

- [ ] **Step 2: Confirm the prior effect is no longer generated**

Run:

```powershell
npx hexo generate
Select-String -Path .\public\index.html -Pattern 'canvas-nest' -Quiet
```

Expected: generation succeeds and the search returns `False`.

### Task 2: Implement the Bubble Canvas

**Files:**
- Create: `D:\\project\\hexo-modern\\source\\js\\home-bubbles.js`
- Create: `D:\\project\\hexo-modern\\source\\css\\home-bubbles.css`

- [ ] **Step 1: Add the canvas stylesheet**

Create `source/css/home-bubbles.css`:

```css
#home-bubbles {
  height: 100%;
  inset: 0;
  pointer-events: none;
  position: absolute;
  width: 100%;
  z-index: 0;
}

#page-header > :not(#home-bubbles) {
  position: relative;
  z-index: 1;
}
```

- [ ] **Step 2: Add the native animation script**

Create a DOM-ready IIFE that exits unless `#page-header.full_page` exists, the viewport is at least `769px` wide, and `prefers-reduced-motion` is not enabled. It must create `canvas#home-bubbles`, use `ResizeObserver` to match the header dimensions, maintain `Math.ceil(width * 0.2)` bubbles, and redraw them with `requestAnimationFrame`.

Each bubble uses the legacy defaults when reset: random x-coordinate, y-coordinate between the canvas bottom and 100px below it, alpha between `0.1` and `1.09`, radius between `1px` and `4px`, and speed between `0` and `1px` per frame. On each frame, move upward, decrease alpha by `0.0005`, draw a filled white circle, and reset faded bubbles. Pause while `document.hidden` is true and resume on `visibilitychange`.

- [ ] **Step 3: Build the site**

Run:

```powershell
npx hexo generate
git diff --check
```

Expected: Hexo exits successfully and Git reports no whitespace errors.

### Task 3: Verify the Local Experience

**Files:**
- Generated: `D:\\project\\hexo-modern\\public\\index.html`

- [ ] **Step 1: Assert generated markers**

Run:

```powershell
$pageHtml = Get-Content -Raw .\public\index.html
"hasBubbleScript=$($pageHtml -match '/blog/js/home-bubbles.js')"
"hasBubbleStyles=$($pageHtml -match '/blog/css/home-bubbles.css')"
"hasCanvasNest=$($pageHtml -match 'canvas-nest')"
```

Expected: `hasBubbleScript=True`, `hasBubbleStyles=True`, and `hasCanvasNest=False`.

- [ ] **Step 2: Validate the running homepage**

Open `http://localhost:4000/blog/` and confirm one `#home-bubbles` canvas is inside `#page-header`, has `pointer-events: none`, and has nonzero dimensions.

- [ ] **Step 3: Confirm non-home pages stay clear**

Open `http://localhost:4000/blog/about/` and confirm `document.getElementById('home-bubbles') === null`.

- [ ] **Step 4: Commit the implementation**

Run:

```powershell
git add _config.butterfly.yml source/js/home-bubbles.js source/css/home-bubbles.css docs/superpowers/plans/2026-09-17-legacy-home-bubbles.md
git commit -m "feat: restore legacy home bubbles"
```

Expected: the commit contains only the bubble configuration, assets, and implementation plan. Do not push; the user requested a local preview first.
