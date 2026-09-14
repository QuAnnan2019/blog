# Butterfly Home Widgets Design

## Goal

Bring the most useful visual and information-density traits of the former
Butterfly homepage into the current Hugo site without replacing LoveIt or
introducing Hexo runtime dependencies.

## Scope

The first migration adds a Butterfly-inspired widget area between the profile
hero and the existing recent-updates section on the homepage.

It contains three Hugo-native cards:

1. An announcement card with a small announcement icon and configurable
   welcome text.
2. A site-data card with linked counts for posts, notes, and tags.
3. A tag-cloud card whose tags are generated from Hugo's tag taxonomy.

The homepage retains its full-screen profile hero and existing recent-updates
list. A later, separate change may restyle the recent-updates list as
Butterfly-inspired article cards.

## Layout And Visual Direction

The cards appear immediately after the hero scroll target and before the
recent-updates heading. On wider screens, the data and tag cards may share a
row when the available width permits; on small screens they stack in source
order.

The implementation references Butterfly's sidebar language rather than
copying its runtime: concise headline rows with icons, independent card
surfaces, restrained radius and shadow, compact statistics, and a dense tag
cloud. Colors, typography, header behavior, and system-following light/dark
mode remain owned by LoveIt.

## Data And Configuration

- The announcement text is a Hugo site parameter, with a Chinese default.
- Post count comes from regular pages in the `posts` section.
- Note count comes from regular pages in the `notes` section.
- Tag count and tag links come from the Hugo `tags` taxonomy.
- Empty taxonomies are handled by omitting the relevant links or displaying a
  concise empty state; no client-side data fetching is used.

## Explicit Exclusions

This first migration does not include page views, visitor counts, runtime,
word counts, comments, music, animated effects, mouse effects, Pjax, or other
third-party scripts from the old Hexo site. It also does not migrate source
files, credentials, certificates, private keys, or historical configuration
from the old repository.

## Technical Approach

The homepage override remains in `layouts/home.html`. Small Hugo partials may
be introduced under `layouts/partials/home/` to keep each widget independently
readable. Styling stays in the project-level custom Sass entry point and uses
LoveIt's existing Sass variables for light and dark themes.

The reference Butterfly theme is Apache-2.0 licensed. The Hugo implementation
will be newly written; it will not include Pug templates or Hexo JavaScript.

## Verification

Run a production Hugo build, inspect the generated homepage locally at desktop
and mobile widths, and verify that post, note, and tag links use the GitHub
Pages `/blog/` base path correctly. Confirm that the widget area works in both
system-derived light and dark themes.
