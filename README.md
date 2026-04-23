# Aspect — Motion Design Studio Site

Static portfolio site for Aspect Design.

## Structure

```
.
├── index.html              # Home: intro, client ticker, project grid, contact
├── sonic-4.html            # Sonic 4 project detail page
├── favicon.ico
└── assets/
    ├── brand/              # Aspect logo SVGs
    ├── logos/              # Client studio logos (ticker)
    └── sonic/              # Sonic 4 project stills
```

## Running locally

```
npx live-server --port=8080
```

Open http://127.0.0.1:8080.

## Video hosting

Videos stream from Vimeo via iframe embeds:

- Homepage reel → https://vimeo.com/1185792568
- Sonic 4 project → https://vimeo.com/1175290670

No local video files are needed. To swap videos, update the `src` on the `<iframe>` in the respective HTML file.

## Deploying

Any static host works (Netlify, Vercel, Cloudflare Pages, GitHub Pages). No build step.
