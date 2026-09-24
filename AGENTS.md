# AGENTS.md

Guidance for AI coding agents (OpenCode, Claude Code, etc.) working in this repository.

## Project Overview

Minimal-UI is a minimalist, single-file CSS framework for rapid prototyping of dark-mode web interfaces, inspired by the s17 Labs aesthetic. It ships one stylesheet plus a demo/documentation page — there is no build system.

- Language/stack: plain CSS (single file, `minimal-ui.css`), vanilla HTML demo page
- Toolchain: none — zero dependencies, no package.json, no bundler; consumers load the CSS via jsDelivr CDN and bring their own Font Awesome 7 + Google Font "Aldrich"
- Author/maintainer: yungsamd17 (https://github.com/yungsamd17)

## Build & Verify

There is no build, test, or lint step. Verification is manual:

```bash
# open index.html (the demo/docs page) in a browser and eyeball components
xdg-open index.html   # or serve it: python3 -m http.server
```

- The repo has no CI. Changes merged to `main` go live immediately: the README quick start pins the CDN to `@main` (`cdn.jsdelivr.net/gh/s17labs/minimal-ui@main/minimal-ui.css`), so any push to `main` is served to consumers right away.
- Because nothing validates changes automatically, review CSS edits carefully (syntax, selector collisions) before merging.

## Architecture

```
minimal-ui.css    # the entire framework: variables, reset, components (~1.5k lines)
index.html        # live demo + documentation page showcasing every component
s17-icon.svg      # brand icon used by the demo page
README.md         # quick-start snippet (CDN link), required external fonts/icons
```

Key patterns:

- All design tokens are CSS custom properties under `:root`, prefixed `--mu-` (e.g. `--mu-accent`, `--mu-surface-raised`, `--mu-space-2`). Components must consume these tokens, not hardcode colors or spacing.
- Class names are short, framework-style utilities/components (`.container`, `.btn`, `.btn-accent`, `.card`, `.alert`, `.badge`, `.accordion`, ...). Keep new names consistent with existing variants (color/size modifiers appended with `-`).
- Dark theme by default; the accent color `#ff4136` is part of the s17 Labs brand identity.

## UI Conventions

- Single dark design system driven by the `--mu-*` tokens; do not introduce a second theme or inline styles inside `minimal-ui.css`.
- Headings use the "Aldrich" font, body text Verdana — both loaded externally by the consuming page, never bundled here.
- Demo additions belong in `index.html` only; keep the framework file free of demo-specific styles.

## Commit Messages

Format: `type(scope): short imperative summary` — lowercase after type, no trailing period.
Keep commits atomic — one logical change per commit.

| Type | Use for |
|---|---|
| `feat` | new user-facing feature |
| `fix` | bug fix |
| `refactor` | code change that neither fixes nor adds behavior |
| `style` | formatting/UI polish without logic change |
| `test` | adding or fixing tests |
| `docs` | documentation only |
| `chore` | build, deps, CI, tooling |
| `release` | version bump / release tagging |

Scope is a short area name for this project (e.g. `css`, `demo`, `docs`).
Use plain `type:` only when a change genuinely spans everything (rare).

- Never add a `Co-authored-by` / `Signed-off-by` trailer for the same identity
  that authors the commit — a self co-author is a redundant duplicate. Only
  credit a genuinely different human co-author, and only when asked. No AI
  co-author trailers in commits either; AI attribution stays only in the PR body.
- Keep the body free of trailers entirely unless explicitly asked for one.
  When squash-merging via `gh pr merge --squash`, pass an explicit
  `--subject` and an empty `--body ""` so GitHub doesn't re-inject branch
  trailers or auto-credit the branch author as a co-author.

## Agent Guardrails

- Never commit or push directly to `main`; all changes land through pull requests.
- Never open a PR unless the developer explicitly asks for it.
- One concern per change. If the description says "also", split it into another branch/PR.
- Do not commit secrets, keystores, or local-only files (e.g. `.and-code/`).
- When watching CI/bot feedback on your PRs: poll checks and comments newer than the last push,
  verify each bot finding against the source before "fixing" it, dismiss false positives with a
  written reason, and stop when checks are green on the latest commit.

## Pull Requests

All changes land on `main` through pull requests.

1. Create a branch off `main`: `<type>/<short-description>` (e.g. `feat/badge-variants`).
2. Commit there using the format from **Commit Messages**; keep commits atomic.
3. Push the branch and open a PR against `main`.

PR rules:

- One feature/fix per PR — small and focused beats large and thorough.
- Title follows the commit message format: `type(scope): short imperative summary`
  (e.g. `feat(css): add tooltip component`) — it becomes the squash-merge commit message.
- Body stays concise: what changed and why, bullet list of touched areas, testing checklist (tick before merge).
- Visual changes must include clear before/after screenshots (upload directly to GitHub — never commit PR-only screenshots).
- End the body with an AI attribution line stating exactly which model and agent made the changes,
  in this exact format:

  ```
  Built with {model} in the {agent} harness.
  ```

  Example: `Built with ox-alpha in the OpenCode harness.`

- Do **not** put AI attribution in GitHub Release notes — releases stay clean.

## Gotchas

- Never rename `minimal-ui.css`: the README quick-start link, the demo page, and every consumer's CDN URL depend on that exact filename.
- The version string appears in at least three places that must be bumped together: the header comment in `minimal-ui.css`, and the `v0.2.0` badges in `index.html`.
- The CDN serves straight from `main` (`@main` pin in the README) — merging to `main` is effectively a deployment; never merge broken CSS.
- Keep the framework dependency-free: Font Awesome and the Aldrich font are external requirements documented in the README, not things to vendor into the repo.
