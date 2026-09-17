# Legacy Home Bubbles

## Goal

Replace the barely visible Canvas Nest lines with the legacy homepage bubble effect while preserving the modern Hexo and Butterfly setup.

## Behavior

- Load the legacy bubble script on each page, where it attaches one canvas to the page's `#page-header`.
- Preserve the legacy rendering and movement parameters unchanged: radius `10`, density `0.2`, and colour `rgba(255,255,255,0.4)`.
- Load the old primary jQuery URL, retain the old secondary jQuery URL as a fallback, then load the old `chocolate.js` script in that order.

## Architecture

Butterfly's supported `inject.bottom` configuration loads the same jQuery and `chocolate.js` resource chain used by the old site. `chocolate.js` owns the Canvas lifecycle and attaches its canvas to `#page-header`.

The Butterfly `canvas_nest` extension is disabled. Current today-poetry, click-to-show-text, loading, dark-mode, and sidebar behavior remain unchanged. The only restored remote decoration is the old site's jQuery plus `chocolate.js` bubble chain; music players, comments, counters, analytics, and unrelated old custom scripts remain excluded. The primary jQuery mirror currently returns `403` to this environment, so the old secondary mirror remains necessary for the chain to load.

## Verification

- `npx hexo generate` completes successfully.
- Generated homepage references both old jQuery URLs and `chocolate.js`, but not Canvas Nest or local bubble assets.
- A desktop browser inspection finds the legacy `#canvas` under `#page-header` and observes non-transparent pixels over two animation frames.
