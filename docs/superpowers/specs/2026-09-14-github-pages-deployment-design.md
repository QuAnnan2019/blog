# GitHub Pages Deployment Design

## Goal

Publish the Hugo site in `D:\project\hugo` publicly through GitHub Pages from the repository `QuAnnan2019/blog`.

## Deployment Model

- The GitHub repository will be made public.
- The `main` branch will contain Hugo source, theme files, content, and the GitHub Actions workflow.
- A GitHub Actions workflow runs for pushes to `main` and manual dispatches.
- The workflow installs Hugo Extended, builds the site with the production URL, uploads the generated `public/` directory as a Pages artifact, and deploys it with GitHub's official Pages deployment action.
- GitHub Pages serves the deployment at `https://quannan2019.github.io/blog/`.

## Configuration Changes

- Change the Hugo `baseURL` from the local development URL to `https://quannan2019.github.io/blog/`.
- Preserve local preview behavior by using Hugo's normal `hugo server` command, which appends its local port to the configured URL.
- Add `disableKinds = ["taxonomy"]` only if the theme cannot build taxonomy pages in CI. The initial workflow will not suppress site features.
- Keep generated `public/` and `resources/` directories ignored by Git.

## Workflow Responsibilities

The workflow file at `.github/workflows/deploy-pages.yml` has one build job and one deploy job:

1. Build job checks out the repository, sets up the specified Hugo Extended version, runs `hugo --gc --minify`, and uploads `public/`.
2. Deploy job downloads the Pages artifact through `actions/deploy-pages` and targets GitHub's Pages environment.

The workflow uses the minimum required token permissions: `contents: read`, `pages: write`, and `id-token: write`. It does not use third-party deployment credentials or secrets.

## Failure Handling

- A Hugo build failure stops the workflow before any deployment occurs.
- A Pages deployment failure leaves the previously published version intact.
- GitHub's Actions logs provide the build and deployment failure details.
- The repository keeps the current vendored LoveIt theme until Git HTTPS can reliably clone the theme as a submodule; this does not affect GitHub Pages builds.

## Verification

- Confirm the repository is public and the local `main` branch has `origin` set to `https://github.com/QuAnnan2019/blog.git`.
- Push the deployment workflow and source to GitHub.
- Confirm the workflow run completes successfully.
- Confirm the Pages URL returns the deployed home page and its Posts, Notes, Tags, and About navigation paths.
- Confirm `hugo --gc --minify` passes locally using the production `baseURL`.
