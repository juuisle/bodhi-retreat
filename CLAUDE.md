# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A bilingual (Vietnamese / English) marketing & information site for **Bodhi Retreat** — a 7-day Phật Thất Buddhist retreat at Bodh Gaya (28 Dec 2026 – 3 Jan 2027). It is a static content site built on Astro, deployed to Netlify. There is no application backend: registration happens through embedded Google Forms, and contact is via email (`bodhi-retreat@web.de`).

The repo was scaffolded from the **Astro Netlify Platform Starter** template. Several files are leftover demo scaffolding, not part of the actual site — see "Starter leftovers" below before assuming a file is in use.

## Commands

```bash
npm install        # install deps (Node 18.20.8+)
npm run dev        # dev server at http://localhost:4321
npm run build      # production build to ./dist/
npm run preview    # serve the production build locally
npm run astro check   # type-check .astro files (there is no separate lint/test setup)
```

There is no test suite, ESLint config, or `lint` script. Formatting is enforced by Prettier (`.prettierrc`): 4-space indent, single quotes, no trailing commas, 160 print width (2-space indent for `.md`/`.mdx`/`.yaml`). VS Code formats on save.

## Architecture

- **Astro 6** static site, **React 19** islands (`@astrojs/react`), **Tailwind CSS 4** wired through the Vite plugin (`@tailwindcss/vite`) — there is **no `tailwind.config`**; theme tokens live in CSS. Output is server-rendered/static via the **Netlify adapter** (`@astrojs/netlify`).
- **Bilingual routing by directory.** Vietnamese is the default at the root (`src/pages/*.astro`); English is mirrored under `src/pages/en/`. The two trees are kept in sync by hand — when you add or change a page, update both. Home pages are the exception: VN home is `src/pages/index.astro`, EN home is `src/pages/en.astro` (a file, routing to `/en`), while the other EN pages live in the `src/pages/en/` directory.
- **Language is detected from the URL**, not from a config or framework i18n layer. `src/components/Header.astro` derives `isEn = Astro.url.pathname.startsWith('/en')` and swaps nav labels/links and the language toggle accordingly. Follow this pattern for any new bilingual UI.
- **Shared shell.** Every page wraps its content in `src/layouts/Layout.astro`, which takes a single `title` prop and renders `Header` + `<slot/>` + `Footer`. Note `<html lang="vi">` is hard-coded in the layout even for English pages.
- **Design system in CSS.** `src/styles/globals.css` is the single source of styling truth: it defines the Tailwind 4 `@theme` tokens (notably `--color-primary: #7b4a35`, the brown brand color), base element styles, and the `.btn` / `.btn-lg` / `.markdown` component classes. Reuse these tokens and classes rather than introducing new colors or ad-hoc button styles. Per-page animations are done with scoped `<style>` + a small inline `<script>` (see `index.astro`).
- **Images** are plain files in `public/images/`, grouped by section (`khoatu/` = retreat, `hanhhuong/` = pilgrimage, `hanhhuong_saukhoatu/` = post-retreat pilgrimage), referenced by absolute path like `/images/...`.

## Starter leftovers (demo scaffolding — not the real site)

These came from the template and are unrelated to the retreat site. Don't treat them as core, and prefer removing rather than extending them:

- `src/pages/blobs/`, `src/pages/edge/` — Netlify Blobs and Edge Function demo pages.
- `src/pages/api/blob.ts`, `blobs.ts`, `revalidate.ts` — demo API routes (Netlify Blobs / cache purge).
- `netlify/edge-functions/rewrite.js` — geo-redirect demo on `/edge`.
- `src/utils.ts`, `src/types.ts` — blob-shape generation helpers used only by the demos.
- `src/components/Markdown.astro`, `EdgeFunctionExplainer.astro`, `Diff.astro`, `Alert.astro`, `Logo.astro` — only `Header`/`Footer` are used by the actual pages (via `Layout`).

## Conventions

- Page titles double as SEO titles; keep them descriptive and translated to match the page's language.
- Registration / forms are external (Google Forms embedded via `<iframe>` or linked `forms.gle` URLs) — there is no form-handling code to wire up.
