# Butterfly Style Restoration

## Goal

Replace the remaining fresh-install placeholder presentation with the user-approved visual language from the legacy Hexo Butterfly site, while staying compatible with Butterfly 5.7.0 and keeping the new project free of legacy plugins and content migration.

## Scope

- Reuse the legacy site's public site identity: title `你好吖~`, description/subtitle `coding`, and author `安南`.
- Reuse only two local legacy presentation assets: `source/img/xiaogong.jpg` as the sidebar avatar and `source/img/fengmian.png` as the home-page cover.
- Configure the native Butterfly navigation, left collapsible sidebar, announcement, recent-post, archive, and web-info widgets.
- Remove the generated `Hello World` post and create a minimal Chinese `/about/` page.
- Enable the native cover, typed home subtitle, and page-enter transition effects.

## Boundaries

- Do not migrate legacy posts, categories, tags, links, music files, or gallery/bangumi pages.
- Do not restore Live2D, music players, comments, visit counters, charts, Pjax, custom JavaScript/CSS injections, remote font/CDN scripts, mouse effects, particle backgrounds, or third-party APIs.
- Do not copy any deployment configuration, certificates, credentials, or files below `D:\project\hexo\blog\peizhi\annnan.com_nginx`.
- Do not alter the configured Pages URL or `/blog/` root path.

## Presentation Configuration

The root Hexo configuration owns site identity. The root `_config.butterfly.yml` owns theme overrides, so `themes/butterfly/` remains unmodified. The menu contains only working native routes: home, archive, tags, categories, and the new about page. The author card has no social follow button because no valid social URL has been supplied.

The sidebar appears on the left and is initially hidden behind Butterfly's existing control. It keeps the announcement, recent posts, archives, and site-info cards. The legacy blue color is retained as an accent through the native theme, rather than as a full-page forced color background.

## Effects

`index_img` uses the local cover image. The home subtitle uses Butterfly's built-in typing effect with the local `coding` text and no remote text source. `enter_transitions` remains enabled. No effect is installed that adds external requests or obscures reading.

## Content Flow

Hexo reads site identity from `_config.yml`, merges the Butterfly-specific settings from `_config.butterfly.yml`, and serves the two copied local assets from `/blog/img/`. The generated home page contains no sample post; until content migration begins, the sidebar represents a zero-post site and the About page supplies the only authored landing destination.

## Verification

- `npx hexo clean && npx hexo generate` completes without warnings about missing images, routes, or theme configuration.
- The local homepage loads the title, cover, and avatar under `/blog/`.
- The navigation links resolve to `/blog/`, `/blog/archives/`, `/blog/tags/`, `/blog/categories/`, and `/blog/about/`.
- The sidebar's author card has no invalid social follow link.
- The generated home page has no `Hello World` text.
