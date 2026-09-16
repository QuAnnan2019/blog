# Latest Hexo + Butterfly Bootstrap

## Goal

Create a clean local starter site at `D:\project\hexo-modern` with the latest stable Hexo and the latest Butterfly theme. This is an isolated preview project; it must not modify or deploy over the existing Hugo site.

## Scope

The first stage installs and configures only the framework, theme, and minimal site settings. It includes an empty/default content structure suitable for local preview. No old content, old configuration, old dependency lockfile, custom widgets, or old deployment scripts are migrated.

## Project Boundaries

- `D:\project\hexo-modern` is a new Git repository with its own package manifest and lockfile.
- The old project at `D:\project\hexo\blog` remains read-only reference material.
- Never read, copy, stage, commit, or deploy anything beneath `D:\project\hexo\blog\peizhi\annnan.com_nginx`; it contains sensitive certificate material.
- Do not modify `D:\project\hugo`, its remote repository, its workflows, or its published Pages site during this stage.

## Architecture

- Install the latest stable `hexo`, `hexo-cli`, required current generators/renderers, and `hexo-theme-butterfly` from their official package/source distribution.
- Keep normal Hexo content in `source/` and generated output in `public/`. Generated output and dependency directories are ignored by Git.
- Select Butterfly in the root `_config.yml`.
- Keep site-specific Butterfly overrides in a root `_config.butterfly.yml`, without editing files inside `themes/butterfly/`.
- Initial configuration uses Simplified Chinese and the `/blog/` base path. The theme uses its system-following light/dark presentation when supported by the current theme release.

## Local Workflow

1. Install dependencies from the clean package manifest.
2. Run `hexo clean` followed by `hexo generate` to validate a production build.
3. Run `hexo server` and inspect the homepage locally, normally at `http://localhost:4000/blog/`.
4. Resolve setup errors before any GitHub configuration is added.

## Deferred Work

- Content and page migration: posts, about, tags, archive, friends, gallery, bangumi, and all other legacy pages.
- Butterfly component customization beyond the small initial theme overrides.
- Social profile links and personal branding content.
- GitHub Actions and GitHub Pages publication. This is a separate approval point after local visual acceptance; it will replace the current Hugo Pages output only when explicitly requested.

## Verification

- Confirm the installed Hexo and Butterfly versions are current at setup time.
- Confirm `hexo clean && hexo generate` exits successfully.
- Confirm a local server returns the rendered homepage under `/blog/`.
- Confirm Git status contains only intended bootstrap files and never includes generated output, dependencies, or sensitive legacy files.
