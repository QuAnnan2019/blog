# Personal Knowledge Base and Blog Design

## Goal

Create a local-first personal website in `D:\project\hugo` using Hugo and the LoveIt theme. The site will publish long-form blog articles and maintainable knowledge notes from Markdown. The first milestone is a working local preview; GitHub Pages publishing follows after local review.

## Scope

- Use the upstream `dillonzq/LoveIt` theme as a Git submodule at `themes/LoveIt`.
- Keep the theme's provided layouts and styling. This project configures the theme; it does not build a replacement UI.
- Store blog posts in `content/posts/` and knowledge notes in `content/notes/`.
- Provide navigation for Home, Posts, Notes, Archives, Tags, and About.
- Seed the site with a minimal English sample post and sample note so the local preview has meaningful content.
- Configure local development with `hugo server --buildDrafts`.
- Defer GitHub Pages workflow and custom-domain setup until the local preview is approved.

## Repository Structure

```text
D:\project\hugo\
  archetypes\
  content\
    posts\
    notes\
    about\
  docs\superpowers\specs\
  themes\LoveIt\                  # Git submodule
  hugo.toml                        # Site and LoveIt configuration
  .gitignore
```

## Content Model

Each Markdown page will have Hugo front matter containing a title, publication date, draft status, categories, tags, and optional series. Posts are reader-facing essays; notes are concise, evolving references. Both content types participate in shared taxonomy pages and site search.

The homepage uses LoveIt's profile/home layout and shows recent content. Section pages separate Posts from Notes. The selected theme provides article tables of contents, archives, tags, search, responsive layouts, and colour-scheme support.

## Local Data Flow

1. The author writes a Markdown file in `content/posts` or `content/notes`.
2. Hugo combines the file, `hugo.toml`, and LoveIt submodule into static HTML.
3. `hugo server --buildDrafts` serves the result locally with live reload.
4. After approval, a future GitHub Actions workflow will perform the same build and publish its output to GitHub Pages.

## Failure Handling

- If Hugo is not installed, install the current Hugo Extended distribution before initializing the preview.
- Clone LoveIt as a pinned Git submodule so a theme update is deliberate and reversible.
- Keep custom configuration in `hugo.toml` and site content outside the submodule, preventing upgrades from overwriting authored work.
- Validate the site with `hugo --gc --minify`; treat broken links, invalid front matter, or theme build errors as setup defects to resolve before handoff.

## Verification

- Confirm `hugo version` reports an Extended build.
- Confirm the theme submodule is initialized and tracked by Git.
- Run `hugo --gc --minify` successfully.
- Run the local server and inspect the rendered home page, Posts, Notes, and one article page in a browser.
- Verify the generated navigation reaches each configured section and that the sample content renders without layout errors.

## Decisions

- Theme: LoveIt (`dillonzq/LoveIt`), managed as a Git submodule.
- Deployment order: local preview first, GitHub Pages second.
- Primary language: English for the initial site configuration and sample content; LoveIt's multilingual support remains available for a later Chinese section.
- Scope boundary: no custom theme development, authentication, comments, analytics, custom domain, or GitHub Pages deployment in this first milestone.
