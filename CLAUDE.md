# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A static personal portfolio site (HTML + CSS only) — no JavaScript, no build step, no package manager, no tests, no linter. The project is a deliberate front-end learning exercise (`README.md`: "um portfólio desenvolvido com o objetivo de aprimorar meus conhecimentos em front-end"). Site content and commit messages are in Portuguese (pt-BR).

Remote: `https://github.com/GuilhermeAlencarFRC/Portf-lio.git`, default branch `main`.

## Running / developing

There is nothing to install or build. To preview, open `index.html` directly in a browser. To serve over HTTP (recommended for some CSS behaviors), use any static server, e.g.:

- `python -m http.server 8000` from the repo root, then visit `http://localhost:8000/`
- or the VS Code "Live Server" extension

## File layout

- `index.html` — single page. Loads `index.css` *and* `StyleSubsystems/MoreThan.css` directly via `<link>`. Contains the `<header>` (with the animated "More than …" title block using `#titulo` / `#flip-titulo`) and the `#sobre-mim` section.
- `index.css` — global styles (body background of stacked radial gradients on near-black, typography via Google Fonts Roboto 700) and the `#sobre-mim` rule. Also contains a stray `@import` (see "Known issues" below).
- `StyleSubsystems/MoreThan.css` — the rotating-word flip animation. The four color stops (rose / violet / emerald / cyan) and the `@keyframes show` that cycles `margin-top` are scoped to `#flip-titulo` and its children. The animation assumes exactly four child `<div>`s (one per word) and will break visually if the count changes without retuning the keyframe margins.

## Architecture: StyleSubsystems pattern

CSS is being intentionally split into per-feature "subsystem" folders under `StyleSubsystems/`. The current convention (see commit `48f4ae3 MUDANÇA: adaptação para subsistema (CSS)`):

- One folder per feature, named in PascalCase plural (`StyleSubsystems/`, `MoreThan.css`).
- The subsystem stylesheet is loaded directly from `index.html` via a `<link>` to `StyleSubsystems/<Name>.css`, and the rules inside it use a feature-scoped root id (e.g. `#titulo`, `#flip-titulo`).
- The parent `index.css` should hold only global / cross-cutting rules and the Google Fonts import — feature styling belongs in its subsystem file, not duplicated in `index.css`.

When adding a new feature: create `StyleSubsystems/<Name>.css`, link it from `index.html` after `index.css`, and namespace its selectors under a unique id.

## Known issues

- `index.css:1` contains `@import url("StyleSubsystem/MoreThan.css");` — the directory is actually `StyleSubsystems` (plural), and the stylesheet is also already linked from `index.html:9`. So the `@import` resolves to nothing and is redundant either way. Recent commit `c1d0a6f correção: "MoreThan" duplicado em index.css` indicates the duplication was noticed; the `@import` line still needs to be removed to actually fix it.
- `StyleSubsystems/MoreThan.css:24` uses `height: top;` which is not a valid CSS value (it has no effect; height is inherited/auto from the element). Likely a typo for `height: 50px;` (matching the parent `#flip-titulo` height) or for an explicit pixel value.
- The hard-coded `margin-right: 850px;` on `#titulo` (`MoreThan.css:13`) is a fragile layout choice that will not adapt to viewport width.
