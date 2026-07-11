# MCMXCI.a.D Archive — Deployment Guide

This folder contains everything needed to host your CD collection site on
Netlify, with cover art downloaded once and cached locally instead of
being fetched live from iTunes on every visitor's browser.

## What's in this folder

- `index.html` — the site itself
- `collection.json` — your 369 albums (artist, album, year, genre, significance)
- `fetch-covers.js` — Node script that downloads cover art once
- `manifest.json` — created automatically by the script (maps each album to its local image)
- `images/` — created automatically, holds the downloaded .jpg covers
- `package.json` / `netlify.toml` — tells Netlify how to build the site

## Option A: Let Netlify do everything (recommended, easiest)

1. Create a free account at netlify.com if you don't have one.
2. Put this whole folder into a GitHub repository (create a new repo, upload
   these files, commit).
3. In Netlify: **Add new site → Import an existing project → connect to
   GitHub → pick your repo**.
4. Netlify will read `netlify.toml` automatically. Build command and
   publish folder are already set — just click **Deploy**.
5. On first deploy, Netlify runs `fetch-covers.js` as part of the build,
   which downloads all cover art into `/images` and writes `manifest.json`
   — this takes a few minutes the first time (369 albums, ~0.3s delay
   between each to stay polite to Apple's API).
6. Every future deploy re-runs the script, but it **skips any cover
   already downloaded**, so later builds are fast — it only fetches
   covers for newly added albums.

That's it — your live site will serve cached local images instead of
calling iTunes from every visitor's browser.

## Option B: Run it yourself first, then upload

If you'd rather not rely on Netlify's build step (e.g. you want to review
the images before publishing):

1. Install Node.js (v18 or newer) on your computer: https://nodejs.org
2. Open a terminal in this folder and run:
   ```
   node fetch-covers.js
   ```
3. Watch it download covers into `/images` and create `manifest.json`.
4. Once it finishes, drag this entire folder into Netlify's
   **"Deploys" → "Drag and drop your site output folder here"** box
   on your site's dashboard. No build step needed this time since the
   images are already there.

## Adding new albums later

Just add new entries to `collection.json` (same format as the existing
ones) and redeploy. The script only downloads art for albums that don't
already have a cached image, so it stays fast.

## Notes

- If a cover can't be found for a particular album (unclear titles,
  obscure compilations, etc.), the site automatically falls back to
  a live iTunes lookup in the visitor's own browser for that one album
  only — so nothing breaks, it just won't be pre-cached.
- The `images/` folder and `manifest.json` are meant to be committed to
  your repo (or included in your manual upload) so they persist between
  deploys.
