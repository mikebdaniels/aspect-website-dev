# Project context — Aspect Website

Static portfolio site for Aspect Design. Live at **teamaspectdesign.com** (custom domain on GitHub Pages, HTTPS auto-provisioned). No framework, no build step, no server-side. Plain HTML + inline CSS + vanilla JS. Push to `main` → live in ~1–3 min.

## Repo layout

- `index.html` — homepage: hero reel (Vimeo background loop), About section, 15-tile project grid, hidden contact section.
- `reel.html` and `<slug>.html` — one file per project (15 total: reel + 14 projects). All follow the same template.
- `assets/<slug>/still_NN.jpg` — per-project stills (numbered, no gaps).
- `assets/<slug>/title_card.jpg` — the homepage tile thumbnail for that project.
- `assets/brand/logo_mark.svg` — Aspect logo.
- `assets/logos/` — old client-ticker logos, currently unused (ticker section was removed).
- `CNAME` — declares the custom domain to GitHub Pages. Do not edit or delete.
- `ae_projectFiles/` — gitignored. Source AE projects, videos, and stills exports. Lives locally in Dropbox.

## Page conventions

Every project page shares the same structure (inline `<style>` block, then body: nav → video-section → project-body/desc → stills-section → related-section → footer → scripts).

Repeated per-page — when editing one, be prepared to apply the same change across all 15 pages via a Python script rather than by hand. Common per-page selectors:

- `<h1 class="video-title">` — project title
- `<span class="video-category">Category | Client</span>` — pipe-separated (never `/`)
- `<p class="desc-primary">` — the one and only description paragraph
- `.video-wrap iframe` — Vimeo embed, standard params `?title=0&byline=0&portrait=0`, wrapped in `.video-wrap` with the correct `aspect-ratio` for the video
- `.stills-grid` — 6-column CSS grid; heroes span `1 / -1`, paired items span 3, defaults span 2 (three per row). Per-page CSS uses `:nth-child()` to place heroes and pairs; row math must land clean (no orphans)
- `.related-section` — three "More Work" tiles, picked by category-first (see helper script)

## Homepage tile pattern

```html
<article class="project-card" onclick="location.href='<slug>.html'" style="cursor:pointer;">
  <div class="card-thumb">
    <img src="assets/<slug>/title_card.jpg" alt="<Title>" loading="lazy" />
  </div>
  <div class="card-info">
    <p class="card-category">Category | Client</p>
    <h2 class="card-title"><Title></h2>
    <p class="card-desc">short one-liner</p>
  </div>
</article>
```

Tiles alternate `9fr 7fr` / `7fr 9fr` in list view. Mobile (<820px) collapses to single column via a specificity-boosted media rule.

## Common tasks

### Add a new project
1. `cp <existing>.html <new-slug>.html`
2. Update `<title>`, `.video-title`, `.video-category`, iframe `src` (video ID + aspect-ratio on `.video-wrap`), `.desc-primary`.
3. Create `assets/<new-slug>/`, drop stills as `still_01.jpg` … `still_NN.jpg`, and a `title_card.jpg`.
4. Rewrite the per-page `.stills-grid` CSS `:nth-child` positions for heroes and pairs so the row math is clean.
5. Rewrite the stills list in the page body to match the new count.
6. Add a `<article class="project-card">` tile to the `.projects-grid` in `index.html`.
7. Regenerate every page's "More Work" via the rebuild script (below).
8. Test locally, then push.

### Extract stills from a source video
Use ffmpeg. Standard pattern for N evenly-spaced frames from `T1` to `T2` seconds:

```bash
for i in $(seq 1 N); do
  TS=$(awk "BEGIN{printf \"%.3f\", T1 + ($i - 1) * ((T2 - T1) / (N - 1))}")
  NUM=$(printf "%02d" $i)
  ffmpeg -y -ss "$TS" -i "<video>" -frames:v 1 -q:v 3 "assets/<slug>/still_${NUM}.jpg" -loglevel error
done
```

Skip the first ~1s and the last ~5s to avoid black frames / promo end cards.

### Extract a title_card from a specific timestamp
```bash
ffmpeg -y -ss <sec> -i "<video>" -frames:v 1 -q:v 2 "assets/<slug>/title_card.jpg" -loglevel error
```

If the user provides a PNG thumbnail, convert to JPG with the same command (`ffmpeg` handles PNG→JPG automatically).

### Remove a user-marked-X still and re-number
When the user posts a screenshot of the stills grid with red X marks:
1. Identify the still numbers from position in the current CSS grid layout (row-by-row).
2. `rm` those `still_NN.jpg` files.
3. Rename remaining stills to be sequential 01..NN using a `_tmp_` prefix + rename dance to avoid overwrites.
4. Update the HTML list count and the CSS `:nth-child` hero/pair positions so the new count still has clean row math.

### Regenerate "More Work" (related) sections
Uses a category-first picker: same category → same client → homepage order. Always exactly 3 cards per page. See `docs/rebuild_related.py` if it exists locally, or recreate it: the regex must anchor on the last `</a>\s*</div>` (greedy match) — the naive non-greedy match `</div>` stops at the first `</div>` inside the first card's `.related-thumb` and produces mangled output.

### Reorder homepage tiles
Move `<article>` blocks around in `index.html`. There's no data-driven order — literal HTML order.

## Deploy

```bash
git add . && git commit -m "..." && git push
```

GitHub Pages rebuilds automatically. No CI. Look at repo → Actions tab to watch the "pages build and deployment" workflow. Live in 1–3 min after the green checkmark. Users may need a hard-refresh (Ctrl+Shift+R / ⌘+Shift+R) to see changes.

## Gotchas (learned during buildout)

- **Vimeo embed permissions.** Every video needs privacy → **Embed anywhere** on Vimeo's side, or the player shows "Sorry, we're having a little trouble" / "change privacy settings" instead of the video. All existing videos are set correctly; only new uploads need this flipped.
- **Wicked: For Good has dead CSS** for a custom local-video player that was replaced by a Vimeo iframe. Left intentionally per user request in case we want to re-implement. Don't remove it.
- **The About section has an em on "meets"** — `<em>meets</em>` renders in italic accent-color; preserve it when editing the headline.
- **File names still contain `-teaser` / `-story-so-far`** for the CoD pages even though titles were shortened. Renaming would ripple through every "More Work" link and tile. Left alone.
- **Nav-logo href is `/` everywhere** (not `index.html` or `#`) so clicking it from any page lands on the clean root URL.
- **The client-ticker section was removed** but its CSS remains in index.html (harmless orphan). Same for `.credits-section` CSS across project pages — DOM is gone, CSS lingers.
- **PROJECT_WRITEUPS.txt / docs/ / HANDOFF.md are gitignored** — they never get pushed. Regenerate PROJECT_WRITEUPS.txt on request; store in `docs/`.

## Never touch without asking

- Repo Settings → Pages (custom domain, HTTPS enforcement) — configured.
- `CNAME` file at repo root — GitHub Pages relies on this.
- GoDaddy DNS.
- Vimeo videos Mike uploaded — don't delete or change existing embed permissions.

## Preferred workflow style

- Read the user's request literally; don't over-interpret. When ambiguous, do the safe/reversible thing and flag the interpretation in the summary.
- For multi-file edits (touching all 15 pages), use a Python script rather than 15 separate Edit calls.
- Always verify by loading the page in the local preview browser after edits.
- End every turn with a short summary of what changed. Never claim to have pushed unless a `git push` actually ran successfully.
