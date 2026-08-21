# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The official website for LokiFRAK (PT Lokakarya Kreativitas Indonesia FRAK), an Indonesian comic publisher, served at https://comics.lokifrak.com/. It is a Hugo static site (Hugo Extended ≥ 0.146.0; CI pins 0.164.0) with a custom in-repo theme, Tailwind CSS v4, and Sveltia CMS for content editing.

## Commands

```bash
npm install          # required once before any Hugo build (Tailwind CLI comes from node_modules)
hugo server          # dev server with live reload at http://localhost:1313
hugo --minify --gc   # production build into public/
```

There is no test suite or linter; a clean `hugo` build is the verification step. Hugo Extended must be on PATH — it is not an npm dependency.

## Deployment

Two independent pipelines both trigger on push to `main`:

- **GitHub Actions** (`.github/workflows/hugo.yaml`) builds and deploys to GitHub Pages (build time zone `Asia/Jakarta`).
- **GitLab CI** (`.gitlab-ci.yml`) builds and rsyncs `public/` to a server via SSH (credentials in GitLab CI variables).

Changes to build steps usually need to be mirrored in both files.

## Architecture

### Theme and styling

The theme is `panelfrak`, developed inside this repo at `themes/panelfrak/` (not a submodule). Layouts live in `themes/panelfrak/layouts/`; there are no root-level layout overrides.

Tailwind CSS v4 is compiled by Hugo itself via the `css.TailwindCSS` pipe in `layouts/_partials/css.html` — there is no separate Tailwind watch process and no `tailwind.config.js`. Configuration is CSS-first in `themes/panelfrak/assets/css/input.css` (`@theme`, `@custom-variant`, `@plugin`). Class detection works through Hugo build stats: `hugo.yaml` enables `buildStats`, mounts `hugo_stats.json` into `assets/notwatching/` (watch-disabled to avoid rebuild loops), and `input.css` declares `@source "hugo_stats.json"`. Consequence: **Tailwind only sees class names that appear in templates/content** — dynamically composed class strings won't be emitted.

Dark mode is class-based (`@custom-variant dark (.dark &)`). An inline script in `layouts/baseof.html` manages the light/dark/system preference (persisted in `localStorage` under `theme`, exposed as `window.__setTheme`/`window.__getTheme`, syncs with UI via a `theme-updated` event). Alpine.js (loaded from CDN in `_partials/js.html`) handles interactive components like the theme dropdown. Fonts are Inter (body, `--font-sans`) and Plus Jakarta Sans (headings, `--font-heading`); the dark theme uses cyan/blue/purple "sci-fi glow" utilities defined in `input.css`.

### Content model

Two main sections, each with its own archetype, layout pair, and Sveltia collection:

- **`content/posts/`** — blog posts. Filename convention `YYYYMMDD-slug.md` (enforced by the CMS slug template); rendered URL is `/posts/:year/:month/:title/` via the `permalinks` config.
- **`content/publications/`** — comic books, with rich front matter: `series`, `authors`, `illustrators`, `editors`, `genres` (all taxonomy lists), plus ISBNs, pricing (`print_mrp_java`, `ebook_mrp`), a nested `physical` object (bind/paper/print), and a `links` list of `{ref, link}` purchase links. Note the date format: publications use `date: 09 May 2026` (DD MMMM YYYY, no time), unlike posts which use full datetimes. The genre field is `genres` (plural) — a singular `genre` key was deliberately removed.

Seven taxonomies are declared in `hugo.yaml`: categories, tags, genres, authors, illustrators, editors, series. Adding a front matter list field that should generate browse pages means adding it there; taxonomy pages render via `themes/panelfrak/layouts/_default/taxonomy.html`.

The homepage hero carousel is driven by `data/hero.yaml`, not content files.

### Sveltia CMS

`admin/` is mounted to `static/admin` and serves Sveltia CMS (loaded from CDN in `admin/index.html`), backed by the `lokifrak/website` GitHub repo. `admin/config.yml` defines the editing schema for pages, posts, publications, and the hero data file. **The three schema sources must stay in sync**: `admin/config.yml` collections, `archetypes/{posts,publications}.md`, and the templates that consume the fields (`themes/panelfrak/layouts/publications/single.html` etc.). When adding or renaming a front matter field, update all three. Setup docs are in `admin/SETUP.md` and `admin/GITHUB_SETUP.md`.

Media uploads live in `uploads/` (mounted to `static/uploads`, referenced as `/uploads/...` in front matter). Filenames may contain spaces (e.g. `Logic & Lumen 1 cover.png`).
