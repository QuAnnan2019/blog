# Chinese Profile Design

## Goal

Turn the current LoveIt starter site into a Chinese-first personal knowledge blog with a compact profile presentation. The site continues to follow the visitor's system light or dark preference.

## Scope

- Keep Simplified Chinese as the only published language for now.
- Do not add an English route or a language-switching control.
- Add one original, locally stored illustration avatar to the home profile.
- Replace starter English labels, profile copy, section titles, and starter content with Chinese equivalents.
- Preserve the existing Blog, Notes, Tags, and About navigation structure.

## Profile Presentation

LoveIt's home profile remains enabled. It displays:

- Site and header title: `安南的博客`.
- A concise Chinese description for the site.
- A simple original illustration avatar with short dark hair on a neutral background.
- No social account links until the owner chooses which accounts to publish.

The avatar is stored under `static/images/` and referenced with a site-relative URL. Replacing the image at the same path later changes the avatar without further configuration changes.

## Theme Behavior

`defaultTheme` remains `auto`. LoveIt selects light or dark mode from the visitor's system preference and retains its manual theme toggle. No custom colors, layout overrides, or theme source changes are required.

## Content Model

The current sample pages remain in their existing locations, but their front matter and visible prose change to Chinese:

- `content/_index.md` supplies the home page metadata.
- `content/posts/` contains publishable blog posts.
- `content/notes/` contains shorter knowledge notes.
- `content/about/_index.md` contains the personal introduction.

Future English content, when needed, will be written as deliberately maintained translations rather than machine-generated at build time.

## Verification

- Run `hugo --gc --minify` and confirm a successful build.
- Preview the home page in a browser with system light and dark modes.
- Confirm the avatar loads from its local static path.
- Confirm all navigation targets render and that no English starter copy remains on the published Chinese pages.
- Commit and push only the intended source and asset changes; GitHub Pages deploys from `main`.
