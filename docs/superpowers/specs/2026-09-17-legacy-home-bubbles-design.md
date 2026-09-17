# Legacy Home Bubbles

## Goal

Replace the barely visible Canvas Nest lines with the legacy homepage bubble effect while preserving the modern Hexo and Butterfly setup.

## Behavior

- Run only when the homepage has the full-screen `#page-header`.
- Add one canvas inside that header after the page loads.
- Draw white, semi-transparent circles that start at random positions just below the header, rise at random speeds, fade, and restart from the bottom.
- Match the legacy visual defaults: radius `10`, density `0.2`, colour `rgba(255,255,255,0.4)`, and gradual alpha fade.
- Resize the canvas with the header and stop animation work while its page is not visible.
- Do not run on mobile widths, in reduced-motion mode, or on pages other than the homepage.

## Architecture

`source/js/home-bubbles.js` owns the canvas lifecycle and animation loop using browser-native APIs. `source/css/home-bubbles.css` positions the canvas within the full-screen header and prevents it from receiving pointer input. Butterfly's supported `inject` configuration loads both local files.

The Butterfly `canvas_nest` extension is disabled. Current today-poetry, click-to-show-text, loading, dark-mode, and sidebar behavior remain unchanged. No jQuery, remote decorative scripts, plugins, music players, comments, counters, or analytics are introduced.

## Verification

- `npx hexo generate` completes successfully.
- Generated homepage references the local bubble script and stylesheet but not Canvas Nest.
- A desktop browser inspection finds one `#home-bubbles` canvas under the full-screen homepage header, confirms pointer events are disabled, and observes non-transparent pixels over two animation frames.
- A non-home page has no bubble canvas.
