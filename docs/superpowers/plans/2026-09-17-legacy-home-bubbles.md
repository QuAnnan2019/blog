# Legacy Home Bubbles Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Restore the exact legacy `chocolate.js` bubble implementation in place of Canvas Nest.

**Architecture:** Butterfly injects the same two jQuery URLs and `chocolate.js` URL that the legacy configuration used. The old script creates its own Canvas under `#page-header`; no local replacement script or stylesheet is shipped.

**Tech Stack:** Hexo 8.1.2, Butterfly 5.7.0, legacy jQuery, legacy `chocolate.js`, YAML.

---

## File Structure

- Modify: `D:\\project\\hexo-modern\\_config.butterfly.yml` - disable Canvas Nest and inject the legacy resource chain.
- Modify: `D:\\project\\hexo-modern\\docs\\superpowers\\plans\\2026-09-17-legacy-home-bubbles.md` - record the implemented legacy path.

### Task 1: Restore the Original Resources

- [x] Remove the `canvas_nest` block and local bubble asset references from `_config.butterfly.yml`.
- [x] Add these legacy resource entries in their original order:

```yaml
inject:
  bottom:
    - <script type="text/javascript" src="https://unpkg.zhimg.com/jquery@latest/dist/jquery.min.js"></script>
    - <script defer src="https://npm.elemecdn.com/jquery@latest/dist/jquery.min.js"></script>
    - <script data-pjax defer src="https://npm.elemecdn.com/tzy-blog/lib/js/theme/chocolate.js"></script>
```

The first mirror is preserved for fidelity; the second legacy mirror is required because the first returns `403` in this environment.

### Task 2: Generate and Verify

- [x] Run `npx hexo generate` and `git diff --check`.
- [x] Check `public/index.html` contains both old jQuery URLs and `chocolate.js`, while `canvas-nest`, `home-bubbles.js`, and `home-bubbles.css` are absent.
- [ ] Load `http://localhost:4000/blog/` and confirm the legacy script creates `#canvas` under `#page-header`.
- [ ] Commit the configuration and documentation; push only after user approval.
