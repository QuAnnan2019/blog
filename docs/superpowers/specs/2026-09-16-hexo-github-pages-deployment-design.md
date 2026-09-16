# Hexo GitHub Pages Deployment

## Goal

Publish the current modern Hexo + Butterfly project to `https://quannan2019.github.io/blog/` using GitHub Actions, replacing the existing Hugo output only after preserving its source history on a remote backup branch.

## Scope

- Add one GitHub Actions workflow that checks out this repository including the Butterfly submodule, installs locked dependencies, builds Hexo, and deploys `public/` through GitHub Pages.
- Point the project remote at `https://github.com/QuAnnan2019/blog.git`.
- Preserve the remote's current `main` commit on `hugo-backup-20260916` before replacing `main` with this project.
- Switch the repository Pages build type to GitHub Actions when necessary.

## Boundaries

- Do not alter `D:\project\hugo`; its source remains a local fallback.
- Do not migrate old Hugo files into the new project.
- Do not commit generated `public/`, `node_modules`, local screenshots, credentials, or certificate material.
- Keep the live site path `/blog/`; the root Hexo configuration already defines `url: https://quannan2019.github.io` and `root: /blog/`.

## Workflow

On a push to `main` or a manual dispatch, the workflow checks out the pinned Butterfly submodule, sets up Node 24, runs `npm ci`, executes `npx hexo generate`, uploads `public/` as the Pages artifact, then deploys it using GitHub's Pages deployment action. The workflow has only `contents: read`, `pages: write`, and `id-token: write` permissions.

## Release Sequence

1. Validate a local clean build.
2. Commit the workflow and push the existing remote `main` SHA to `hugo-backup-20260916`.
3. Push this new history to remote `main` with force-with-lease, since it intentionally replaces the separate Hugo repository history.
4. Set Pages to use GitHub Actions if it is not already configured.
5. Confirm the Pages workflow succeeds and the published URL returns the Hexo homepage.

## Failure Handling

- If remote backup creation fails, do not replace `main`.
- If the replacement push fails, leave remote `main` unchanged and report the Git error.
- If the Actions build or deployment fails, keep `hugo-backup-20260916` as the rollback source and inspect only the workflow log before making a focused correction.
- Rollback consists of force-pushing `hugo-backup-20260916` back to `main` only with explicit user approval.

## Verification

- Local `npx hexo clean && npx hexo generate` succeeds before push.
- Remote backup branch resolves to the former `main` SHA.
- The Actions run completes successfully.
- `https://quannan2019.github.io/blog/` returns the Chinese Hexo/Butterfly homepage with `/blog/` asset paths.
