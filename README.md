# Ibad — Performance Video Editor & AI Creative Producer

Portfolio site built with Vite + React 19, GSAP (ScrollTrigger, SplitText, Flip), Lenis smooth scroll and a custom liquid-glass UI.

```bash
npm install
npm run dev      # http://localhost:5173
npm run build    # production build in /dist
npm run preview  # serve the build
```

## Where things live

| Path | What |
| --- | --- |
| `src/data/site.js` | Name, email, availability, socials, platforms, hero reel, AI Studio media |
| `src/data/projects.js` | The 13 case studies (copy, hooks, script breakdown, media timings) |
| `src/data/strategy.js` | Hook strategy + the homepage Hook Lab (which hook test it shows) |
| `src/data/services.js`, `process.js` | Services (each previews one real ad), process, AI workflow copy |
| `src/sections/*` | Homepage sections, in page order (see `src/pages/Home.jsx`) |
| `src/pages/*` | Home, All work (`/work`), Case study (`/work/:slug`), 404 |
| `src/components/*` | Shared UI: `LiquidGlass`, `Button`, `VerticalVideo`, `ScrubVideo`… |
| `src/styles/tokens.css` | Colours, type scale, spacing, motion tokens |
| `scripts/media.mjs` | Builds every web asset for a project from its master (`npm run media`) |
| `media-inbox/` | Your original ad exports (not deployed, not in git) |
| `public/media/work/<id>/` | Encoded per-project media: poster, preview, full, scrub, beat stills, hook clips |
| `public/media/ai/` | AI Studio assets — the 3D character built from your photos with Higgsfield |
| `public/media/frames/` | Stills pulled from the ads for the Manifesto, glow and Iteration grid |

## Adding a new ad

1. Drop the master export into `media-inbox/` (9:16 MP4, any length). For a hook test, drop every variation too.
2. Add a `project({...})` entry in `src/data/projects.js` — copy an existing one:
   - `media: { id, src, poster }` — a folder name, the master's file name, and the second to use as the cover frame (`live: true` for real, non-AI footage).
   - `hook.seconds` — where the hook ends (the case-study scrub covers exactly that window).
   - `breakdown[].at` — the second each beat still is pulled from.
   - `hooks[]` (hook tests only) — each opening's file and `dur`, the second where the shared body starts.
   - Add its slug to `ORDER` at the bottom; the first six are featured on the homepage.
3. Run `npm run media -- <id>` — posters, previews, the full ad, the hook scrub, beat stills and hook clips are cut for you (ffmpeg ships with the project, nothing to install).

`placeholderMode` in `src/data/site.js` stays `false`: real ads carry their own burned-in captions, so the site doesn't draw any over them.

## Contact form

The form validates and shows a success state but does not send anything yet. Wire it to your form backend (Formspree, Resend, a serverless function…) where the `TODO` is in `src/sections/Contact.jsx` — the payload is already built as `FormData`, including the optional brief upload.

## Motion & accessibility

- The site follows the operating system's *reduce motion* setting by default. In that mode scroll-driven storytelling (pinned sections, scrubbed video) stays, while smooth-scroll inertia, parallax, loops and large entrance moves are removed.
- Visitors can switch between **Full** and **Reduced** motion from the footer (the choice is remembered). When the OS asks for reduced motion, a one-time notice offers the full experience.
- On Windows, *Settings → Accessibility → Visual effects → Animation effects* (or "Adjust for best performance") turns reduced motion on for every browser.
- Liquid-glass refraction uses SVG filters inside `backdrop-filter`, which only Chromium renders; Safari and Firefox get a frosted-glass fallback.

## Deploying

Any static host works. SPA fallbacks are included for Netlify (`public/_redirects`) and Vercel (`vercel.json`) so deep links like `/work/corbel-the-trainer` resolve.

The encoded media is about 350 MB. A few full-length ads are 24–34 MB each — fine on Netlify and Vercel, but over Cloudflare Pages' 25 MB per-file limit; on that host, move the `full.mp4` files to a video host (Bunny, Cloudflare Stream, Mux) and point `full` at those URLs.
