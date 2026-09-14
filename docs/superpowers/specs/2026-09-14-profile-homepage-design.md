# Profile Homepage Design

## Goal

Replace the default LoveIt home layout with a Chinese-first personal-card landing page. The first viewport introduces the site; recent knowledge content begins only after the visitor scrolls down.

## Scope

- Preserve LoveIt's header, search, appearance toggle, footer, and all existing content routes.
- Override only the HTML home template and add site-local styles; do not modify the vendored LoveIt theme.
- Keep the configured system-following light and dark mode.
- Use the existing local avatar and the site's configured name and subtitle.
- Do not render social icons until a public link is configured.

## Layout

### First Viewport

The home page has a personal-card hero that fills the visible area below the fixed header. Its centered content, in order, is:

1. Circular avatar using `params.home.profile.avatarURL`.
2. Site title using `params.home.profile.title`.
3. Subtitle using `params.home.profile.subtitle`.
4. A compact down-arrow link targeting the updates section.

The hero has no background image, decorative cards, statistics, or empty social controls. This keeps the visual hierarchy focused on the personal introduction and works in both appearance modes.

### Content Below the Hero

An `id="recent-updates"` section begins below the fold. It renders the six most recently dated regular pages from the `posts` and `notes` sections together. Each item displays a localized type label, title, publication date, and a short plain-text summary, then links to its full page.

The section remains a compact list rather than a grid of cards. A final text link routes to the existing Blog or Notes navigation paths rather than introducing new content systems.

## Responsive Behavior

- Desktop: the hero's minimum height is the viewport height minus the header, with content vertically centered.
- Mobile: the hero keeps comfortable vertical padding and uses the same single-column arrangement; title and subtitle wrap without overlap.
- The down-arrow has an accessible label and honors the browser's reduced-motion preference by avoiding forced animated scrolling.

## Implementation Boundaries

- `layouts/home.html` overrides LoveIt's `home.html` through Hugo's normal layout lookup.
- `assets/css/_custom.scss` supplies only styles for the custom home classes and is automatically loaded by LoveIt's existing `style.scss` import.
- The existing `hugo.toml` profile fields remain the source of title, subtitle, avatar, and social-link visibility.
- No JavaScript, external API, analytics, comment system, or database is added.

## Verification

- A production Hugo build succeeds.
- The home page has one visible avatar, title, subtitle, and down-arrow anchor.
- The updates list contains both posts and notes when both sections have published content.
- The existing Blog, Notes, Tags, and About routes continue to return HTTP 200.
- Light and dark system preferences keep readable contrast and the home layout does not overflow at mobile width.
