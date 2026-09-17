# Butterfly Interactive Components

## Goal

Restore the selected visual and interactive character of the legacy Butterfly site using Butterfly 5.7.0's native configuration, while retaining the modern site's local assets, deployment model, and excluded legacy plugins.

## Scope

- Restore the global blue site background using `#49b1f5`; primary reading content remains in Butterfly's existing white content surfaces.
- Preserve the existing local cover, left collapsible avatar sidebar, announcement, recent-post, archive, web-info, footer, theme control, and page-enter transition configuration.
- Enable Butterfly's native loading animation, reading mode, and right-side controls.
- Restore the legacy upward bubble background on the desktop homepage only.
- Restore native click-to-show-text using the legacy ten Chinese civic-value words on desktop only.
- Configure the home subtitle typewriter to request its content from the official 今日诗词 service using Butterfly source mode `3`.

## External Dependency Boundary

The 今日诗词 subtitle source and Butterfly's default jsDelivr-hosted `clickShowText` extension are the enabled external resources. The bubble effect is served locally. All are non-essential decoration: the page must still render if a request is slow or unavailable. The cover, avatar, loading animation, and navigation continue to use local theme files and browser code.

## Configuration

All theme overrides remain in `D:\project\hexo-modern\_config.butterfly.yml`; no files under `themes/butterfly/` are modified. A small local stylesheet and native Canvas script at `source/css/home-bubbles.css` and `source/js/home-bubbles.js` are injected through Butterfly's supported `inject` settings. They recreate the old rising white bubbles without jQuery and prevent the canvas from intercepting pointer input. The root `_config.yml`, source content, GitHub Actions workflow, `/blog/` URL, and submodule reference remain unchanged.

The bubble effect runs only on the full-screen homepage header, stays disabled on mobile and when reduced motion is requested, and keeps pointer input available to the page. The click effect remains desktop-only. The existing system-following dark mode remains enabled. Placeholder `coding` copy is empty in site metadata, the sidebar, and subtitle fallback values; source mode `3` continues to show 今日诗词.

## Exclusions

- Do not restore Live2D, music players, comments, counters, charts, Pjax, arbitrary third-party script/CSS injections, remote font loading, analytics, or any former plugin package. Disable Butterfly's default Busuanzi counters explicitly.
- Do not add any social link or profile URL.
- Do not alter existing legacy content boundaries or copy additional legacy files.

## Verification

- `npx hexo clean && npx hexo generate` completes successfully.
- Generated home HTML contains native configuration markers for the online poetry typewriter and local effect scripts.
- Local browser inspection shows the loading transition, title/subtitle area, current navigation, sidebar control, reading control, and no invalid social button.
- Desktop screenshot confirms the blue background remains behind readable content and effects do not obscure the header or navigation.
- The deployed homepage returns `200` and retains the current local cover/avatar paths.
