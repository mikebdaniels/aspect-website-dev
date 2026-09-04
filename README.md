# Aspect — Motion Design Studio Site

Static portfolio site for Aspect Design. Live at **[teamaspectdesign.com](https://teamaspectdesign.com)**.

No framework, no build step. Plain HTML + CSS + a small amount of vanilla JavaScript. Deploy is `git push` — GitHub Pages serves whatever is on `main`.

## Structure

```
.
├── index.html                       # Homepage: hero reel, about, projects grid
├── reel.html                        # Motion Design Reel project page
├── sonic-4.html                     # One HTML file per project (14 total)
├── wicked-for-good.html
├── wicked.html
├── home-before-dark.html
├── bo4-teaser.html
├── bo6-story-so-far.html
├── bo7-story-so-far.html
├── furiosa.html
├── she-hulk.html
├── shazam-2.html
├── color-purple.html
├── until-dawn.html
├── nysm-2.html
├── CNAME                            # Custom domain (teamaspectdesign.com)
├── favicon.ico
└── assets/
    ├── brand/                       # Aspect logo SVGs
    ├── logos/                       # Client logos (used to be in a ticker)
    ├── sonic/                       # Sonic 4 stills + title_card.jpg
    ├── wicked/                      # Same pattern for every project
    ├── wicked-for-good/
    ├── home-before-dark/
    ├── bo4-teaser/
    ├── bo6-story-so-far/
    ├── bo7-story-so-far/
    ├── furiosa/
    ├── she-hulk/
    ├── shazam-2/
    ├── color-purple/
    ├── until-dawn/
    ├── nysm-2/
    └── reel/
```

Each `assets/<slug>/` folder holds `title_card.jpg` (the tile thumbnail) and `still_01.jpg` through `still_NN.jpg` (the stills grid).

## Running locally

```
npx serve --listen 8080 --no-clipboard
```

Open http://localhost:8080. Static server, no auto-reload — hit Ctrl+R after edits.

(Or use whatever static server you like. `python -m http.server 8080` works too.)

## Deploying

Push to `main`. GitHub Pages rebuilds and pushes to both `teamaspectdesign.com` and `mikebdaniels.github.io/aspect-website-dev/` in ~1–3 min. That's it.

```
git add .
git commit -m "what changed"
git push
```

## Video hosting

All videos live on Vimeo and load via iframe embed. The hero reel on the homepage uses Vimeo's background-video params (`?background=1&autoplay=1&muted=1&loop=1`) so it autoplays muted and loops with no controls. Project pages use normal Vimeo playback controls.

**Important:** every video's Vimeo privacy → embed setting must be **Anywhere** (or list `teamaspectdesign.com`), otherwise the player shows "Sorry, we're having a little trouble" or "change privacy settings" instead of the video.

To swap a video: just replace the video ID in the iframe `src` on the page.

## Adding a new project

1. Copy an existing project page (e.g. `sonic-4.html`) → rename it.
2. Update the video iframe src, title, category, description.
3. Drop stills in `assets/<new-slug>/still_01.jpg` … and a `title_card.jpg`.
4. Adjust the per-page stills grid CSS (`.still-item:nth-child(...)`) for heroes and paired rows so the row math is clean.
5. Add a project tile to the `.projects-grid` in `index.html`.
6. Re-run `docs/rebuild_related.py` (or ask Claude) to refresh every page's "More Work" section using the same-category-first picker.

## Source assets not in this repo

All AE project files, source videos, and exports live in `ae_projectFiles/` (with `_assets/`, `_exports/`, and per-project `.aep` files inside). Gitignored, so they don't get pushed — they stay in Dropbox. Ask Mike for the share link if you need them (usually you don't, unless you're extracting new stills).

## Custom domain

Domain `teamaspectdesign.com` is registered at GoDaddy. DNS points at GitHub Pages IPs (4 × `A` records on `@`, and `CNAME www → mikebdaniels.github.io`). HTTPS is auto-provisioned by GitHub via Let's Encrypt — no action needed. Repo → Settings → Pages shows the current status.
