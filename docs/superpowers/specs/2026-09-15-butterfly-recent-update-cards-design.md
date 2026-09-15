# Butterfly Recent Update Cards Design

## Goal

Replace the homepage's plain recent-updates list with a Hugo-native,
Butterfly-inspired alternating cover-card list while retaining LoveIt as the
site theme.

## Scope

The homepage continues to show the six newest regular pages from the `posts`
and `notes` sections. Its current heading remains. Each update becomes one
linked visual card containing:

1. A type label of `博客` or `笔记`.
2. The publication date.
3. The title.
4. A plain-text summary.
5. At most two tags when the page has tags.
6. A cover area.

## Cover Resolution

The card checks a page's `featuredImage` Front Matter value first. When it is
present, the cover area uses that image. When it is absent, the card renders a
local CSS-only fallback marked by the page type: a cool, restrained treatment
for notes and a distinct warm treatment for posts.

No image is copied from the former Hexo repository for this change. When an
old article is later migrated, its selected image can be migrated with that
article and referenced through `featuredImage`.

## Layout And Interaction

On desktop, each card has a 42-percent cover and a text area. Consecutive cards
alternate their cover between left and right, matching Butterfly's homepage
rhythm. On viewports at or below 680px, each card becomes a single column with
the cover above its text, so the reading order stays predictable.

The cover and title are links to the same page. Tag chips link to their Hugo
taxonomy pages. There is no redundant read-more button, external image fetch,
animation effect, or third-party JavaScript.

## Technical Approach

`layouts/home.html` keeps the sorted combined page collection and delegates the
recent-update card markup to a focused partial. The partial owns the type,
summary, tag limit, featured-image check, and alternating index class.

Project-level Sass in `assets/css/_custom.scss` styles only the new card
classes. It uses existing LoveIt Sass variables and `[theme=dark]` overrides,
keeping system-following light and dark mode behavior intact.

## Verification

Build the production site with Hugo, verify that `featuredImage` and fallback
variants render correctly, and confirm all card and taxonomy links keep the
`/blog/` GitHub Pages base path. Inspect both the desktop alternating layout
and the mobile stacked layout, including a no-horizontal-overflow check.
