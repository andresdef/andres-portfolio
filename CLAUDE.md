# Andrés de Fonollosa — portfolio site

Freelance portfolio for motion design, video edits and 3D design. Its job is to win paying clients.
The owner speaks Russian: **reply to him in Russian**, keep code and commit messages in English.
Contact everywhere on the site: Telegram https://t.me/andres_de_f (`@andres_de_f`).

## Hosting and deploy

- GitHub Pages serves the `main` branch root: **https://andresdef.github.io/andres-portfolio/**. This is the link clients get.
- Every push to `main` goes live in 1–2 minutes (owner then needs Ctrl+F5).
- Netlify (`andres-motion.netlify.app`) is dead (credits ran out). Do not use or mention it as live.
- Group changes and push in batches, not after every tiny edit.
- Planned: custom domain (owner is choosing one on Porkbun). When he has it: add a `CNAME` file with the domain and update any absolute links; he sets DNS and the Pages setting himself.

## Promoting v2 to the main site (owner's explicit goal)

The owner wants the finished experimental version to **replace the original site at the same link** (https://andresdef.github.io/andres-portfolio/), not live at `/v2/`. Do this only when he says v2 is ready, in one commit:
1. Copy `v2/index.html` over `index.html`.
2. In the new `index.html` replace every `../media/` with `media/` and remove `<meta name="robots" content="noindex">`.
3. Delete the `v2/` folder (or leave a tiny redirect page at `v2/index.html` to `../`).
4. Check with Playwright or a local server that videos load, the player opens, filters, calculator, order form and the RU/EN switch all work, then push to `main`.
5. Update this file: `index.html` is now the cinematic version, and remove the "keep both files in sync" rule.
Until then keep working in `v2/` and leave `index.html` untouched so clients always see a stable site.

## Files

- `index.html` — the live site. One self-contained file: inline CSS and several inline `<script>` blocks. No build step, no framework.
- `v2/index.html` — experimental "cinematic" version (preloader, glass lens over the name, dust particles, scroll-expanding showreel, statement fill, scrambling stats, shattering headings, custom cursor, magnetic buttons, grain, synthesized Web Audio UI sound behind the `#snd` toggle (off by default, ambient ducks while the player is open), WebGL liquid distortion over the hovered preview (fine pointer only), pinned horizontal work gallery at ≥900 px wide via `#work.hg-on`). `noindex`. Media paths are `../media/...`. Live at `/andres-portfolio/v2/`. The owner likes it; it may replace `index.html` later.
  v2 was built from a copy of `index.html`, so content changes (works, prices, texts) must be made in **both** files until v2 is promoted.
- `media/` — for each work: `name.mp4` (light muted looping preview on the card), `name.jpg` (poster), `name-hq.mp4` (high quality with sound, used in the player). `don-coptilio` and `dream-baku-core` have no `-hq` yet (waiting for originals from the owner).

## How the page works (index.html)

- **Work cards**: `<article class="work" data-cat="…" data-title="…" style="--ar:W/H">` containing `<button class="frame" data-src="media/…-hq.mp4" data-ar="…">` with the preview `<video>`, plus title, duration, description, category and badge.
  - Categories (`data-cat` and filter `data-filter` keys, always English): `Motion Design`, `Edits`, `3D Models & Design`, `All`. Default filter is Motion Design, order of filters: Motion Design, Edits, 3D, All. Counts are computed from the markup.
  - Badges: `class="badge mono client"` → "Client work" for real clients (Don Coptilio, Yeay Corporation, Invizione Ad); `class="badge mono concept"` → "Concept · personal project" for self-initiated ads (ABB, ZARA, Korhan).
  - Card size comes from `--ar` and `--row-h`, so nothing is letterboxed or cropped.
  - Previews autoplay via an IntersectionObserver (`sync()` / `kick()`). **Never pause previews based on `prefers-reduced-motion`**: the owner's PC has it on, and that once broke autoplay for him.
- **Player**: `#stage` modal plays `data-src` with sound, Prev/Next/Close, title from `data-title`.
- **Pricing calculator** (`#calc`): `.types` buttons carry `data-base`, `data-step`, `data-max` (seconds), `data-name`. Base price covers up to 30 s, every extra 15 s adds one step, rush ×1.5, slider step 5 s. Motion design is first and selected by default.
  Current prices (EUR): Motion design from €105, step €40, max 40 s. Edit from €70, step €25, max 45 s. The header text "From €70 per video" and the button labels must match `data-base`.
- **Order form**: "Place an order" (`#ord`) opens `#order` (first/last name, phone, email, details, small Submit bottom-right). Sends to Web3Forms with the public access key in `var KEY` (delivers to the owner's email). The calculator estimate is included.
- **Language switch** (`#lang`, RU/EN): the HTML is in English; a script translates text nodes by exact trimmed match from the dictionary `D` (plus `Object.assign(D,{…})` blocks) and a MutationObserver translates dynamic text. Original English is kept on each node (`__en`) so switching back works. Choice is saved in localStorage; Russian is the default when the browser language is Russian.
  **Any new visible English text needs a Russian entry in the dictionary**, in both files. Splitting a text node into spans breaks the match; v2's heading animation restores the original nodes after animating for this reason.
  Fonts: Big Shoulders Display + Instrument Sans + IBM Plex Mono have no Cyrillic, so `[lang=ru]` switches to Oswald + Inter. v2 also uses Cormorant Garamond (has Cyrillic).
- Page always opens at the top (scroll restoration off, hash removed on load).

## Design rules from the owner

- Black and white only for the UI (the videos themselves are in color).
- Videos must play with sound in the player and must not show black bars.
- Keep it practical: whatever the effects, a client must easily see every video, the prices and how to order.
- Work titles and descriptions were written by the assistant from the frames; the owner may correct them.

## Adding a new work

1. Make the preview (muted, ~960 px wide, short loop) and poster:
   `ffmpeg -i in.mp4 -an -vf scale=960:-2 -c:v libx264 -crf 28 -preset slow -movflags +faststart media/name.mp4`
   `ffmpeg -ss 1 -i in.mp4 -frames:v 1 -vf scale=960:-2 -q:v 3 media/name.jpg`
2. Make the HQ file with sound:
   `ffmpeg -i in.mp4 -vf scale=-2:1080 -c:v libx264 -crf 20 -preset slow -c:a aac -b:a 192k -movflags +faststart media/name-hq.mp4`
3. Copy an existing `<article class="work">`, set `data-cat`, `data-title`, `--ar` / `data-ar` (16:9 = 1.7778, 9:16 = 0.5625), duration, description, badge.
4. Add the Russian translation of the description to the dictionary. Do it in `index.html` and `v2/index.html`.
5. When replacing a file under the same name, add `?v=N` to its URLs to bust caches.

## Ideas the owner may ask for next

UI sound design (ambient + hover/click sounds behind a "Sound on" toggle), WebGL liquid distortion on card hover, pinned horizontal work gallery, a three.js 3D object in the hero, a case-study page per work, a marquee that reacts to scroll speed.
