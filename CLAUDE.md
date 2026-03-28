# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with
code in this repository. As we work, remember to update this file with any/all
helpful information.

## Commands

```bash
pnpm dev        # Start dev server at localhost:4321
pnpm build      # Build production site to ./dist/
pnpm preview    # Preview production build locally
```

## Architecture

This is an **Astro 6** static site (personal portfolio). The project uses pnpm and requires Node >=22.12.0.

- `src/pages/` — File-based routing; each `.astro` file becomes a route
- `public/` — Static assets served as-is
- TypeScript strict mode is enabled via `astro/tsconfigs/strict`

No UI framework (React/Vue/etc.) or CSS framework is currently configured.
Integrations would be added in `astro.config.mjs`.

## Design System/Ethos

This can be found `.claude/DESIGN.md`. Stick to this religiously. Do not stray
from the ideas set here.


## Initial examples.

Created in Google Stitch, the designs can be found in `.claude/page_examples`.
The code for each page uses tailwind, which i would like to avoid. Therefore
be sure to rewrite these rules as CSS utilizing the most modern techniques.
