# Portfolio site — how to run it, fill it, and put it online

No build step, no dependencies, no server required. Every page is plain HTML,
one stylesheet, and a little vanilla JavaScript.

---

## 1. Run it

Double-click `index.html`. That's it — it works from `file://`, which is why the
gallery manifests are `.js` files rather than `.json` (browsers block local
`fetch()` of JSON, but a `<script>` tag is fine).

If you'd rather serve it properly while editing:

```
cd site
python -m http.server 8000
```

then open `http://localhost:8000`.

---

## 2. Where things live

```
site/
├─ index.html                  research index / landing
├─ about.html
├─ contact.html
├─ INSTRUCTIONS.md             this file — delete before publishing
│
├─ research/
│  ├─ cmb.html                 CMB diffusion component separation
│  ├─ spherex-hetdex.html      SPHEREx × HETDEX stacking
│  └─ kanchenjunga.html        ArcGIS StoryMap (live embed)
│
├─ art/
│  ├─ index.html               art landing
│  ├─ blender.html             ← reads assets/data/art-blender.js
│  ├─ motion.html              ← reads assets/data/art-motion.js
│  └─ digital.html             ← reads assets/data/art-digital.js
│
└─ assets/
   ├─ css/main.css             all styling, one file
   ├─ js/site.js               nav, scrollspy, SVG plotting helper
   ├─ js/gallery.js            gallery renderer + lightbox
   ├─ data/art-*.js            YOU EDIT THESE — gallery manifests
   ├─ figures/                 the four CMB figures from Roar Collab
   └─ art/
      ├─ blender/              YOU DROP FILES HERE
      ├─ motion/
      └─ digital/
```

---

## 3. Filling the art galleries

Two steps per room.

**Step one — drop the files in.**

| Page | Folder | Formats |
|---|---|---|
| Blender | `assets/art/blender/` | `.jpg` `.png` `.webp` |
| Motion | `assets/art/motion/` | `.mp4` (H.264) — plays everywhere. `.webm` is smaller but doesn't work in Safari. |
| Digital | `assets/art/digital/` | `.jpg` `.png` `.webp` |

**Step two — list them in the manifest.**

Open `assets/data/art-blender.js` and add one object per piece inside `items`:

```js
window.ART_BLENDER = {
  items: [
    {
      src:     "../assets/art/blender/monastery.jpg",
      full:    "../assets/art/blender/monastery-4k.jpg",
      title:   "Monastery at altitude",
      caption: "Cycles, 2048 samples, procedural snow shader.",
      tags:    ["Cycles", "Procedural", "2026"]
    },
    {
      src:   "../assets/art/blender/second-piece.jpg",
      title: "Second piece"
    }
  ]
};
```

Field reference:

| Field | Required | What it does |
|---|---|---|
| `src` | **yes** | Thumbnail / main file. Path starts `../` because it's relative to the page in `art/`. |
| `full` | no | Larger file opened in the lightbox. Omit and the lightbox uses `src`. |
| `type` | no | `"image"` by default. Use `"video"` for anything in the motion room. |
| `poster` | no | Video only — a still shown before playback. |
| `title` | no | Shown under the thumbnail. |
| `caption` | no | One short line under the title. |
| `alt` | no | Alt text. Falls back to `title`. Worth writing for the still images. |
| `tags` | no | Array of short strings, rendered as pills. |

Order in the file is the order on the page. Add an entry and the empty state
disappears automatically — you don't touch the HTML.

**Sizing advice.** Thumbnails render at roughly 280–400 px wide on desktop and
serve at whatever resolution you upload, so export a ~1600 px-wide version for
`src` and point `full` at the 4K if you have one. Videos: keep under about
15 MB each or the page feels slow on mobile.

---

## 4. Placeholders to fill before this goes public

Search the whole folder for `todo` — every spot I couldn't fill is marked with
an orange dashed box, and there are five of them:

1. **`research/kanchenjunga.html` — methods paragraph.** The StoryMap is
   JavaScript-rendered so I couldn't read its contents. Two or three sentences
   on what was computed in ArcGIS Pro versus done as cartography in ArcGIS
   Online.
2. **`research/kanchenjunga.html` — layer inventory table.** Replace the
   placeholder rows with your real layers, sources and vintages.
3. **`research/spherex-hetdex.html` — ZODI section.** Sections 7–9 of
   `SPHEREx-HETDEX-ZODI-Spatial-Map.ipynb` had their outputs cleared before
   saving, so the zodipy Fall/Spring intensity ratio, the measured f-ratio, and
   the `F_clean` Spearman ρ are described but not quoted. Re-run those three
   cells and paste the numbers in.
4. **`about.html` — background section.** Coursework, teaching, outreach, talks,
   awards. It's deliberately thin right now.
5. **`contact.html` — links.** LinkedIn, Google Scholar, INSPIRE, arXiv author
   page. There's also a commented-out CV block: drop `cv.pdf` in the site root
   and uncomment it.

---

## 5. Two things to verify

**Units on the CMB reference table.** Your JSON files give `rmse_uk` as
54.43 / 54.38 / 72.88 µK for SMICA / NILC / Commander, but Table 6.1 of
`CMB_Post_review.pdf` lists the same quantity as 0.051 / 0.054 / 0.073 — a
factor of 1000 apart. The page currently shows the JSON values labelled µK. If
the paper is right and these are mK, edit the `uk:` values in the `REFS` object
near the bottom of `research/cmb.html`.

**Your title.** `about.html` says senior undergraduate, Schreyer Honors College,
BS expected December 2026, which is what the CMB manuscript's title page states.
Change it if that's out of date.

---

## 6. Editing the research pages

All data on those pages lives in JavaScript objects at the bottom of each file,
not scattered through the markup — so updating a number means editing one place.

In `research/cmb.html`:

| Object | Drives |
|---|---|
| `LOOP` | the closed-loop / open-loop hero toggle |
| `ARCH` | the seven architecture stages |
| `REFS` | SMICA / NILC / Commander metrics and latitude coverage |
| `ABL` | the ablation λ toggles |
| `FG` | the foreground generalisation plot |
| `NSIDES`, `PEAKS` | the patch-geometry calculator |

In `research/spherex-hetdex.html`:

| Object | Drives |
|---|---|
| `LINES`, `NOTES` | the raw / clean p-value hero |
| `REST`, `SPX_LO/HI` | the redshift reachability slider |
| `FUNNEL` | the sample-construction bars |
| `ZB` | the ZODI ecliptic-latitude profile |

The patch-geometry calculator computes θ_pix and ℓ_Nyq live from Nside rather
than reading a table, so it stays correct if you change the Nside list.

### Adding a fourth research page

Copy `research/kanchenjunga.html` (it's the simplest), change the `<title>`,
`pagehead`, and rail entries, and add a link to the `.nav` block in **every**
page — the nav is duplicated per file rather than injected, which is the price
of having no build step. There are nine files to update.

---

## 7. Publishing

**GitHub Pages** — the easiest route, and free:

```
cd site
git init
git add .
git commit -m "portfolio"
git branch -M main
git remote add origin https://github.com/ParagChettri12/<repo-name>.git
git push -u origin main
```

Then Settings → Pages → Source: `main` / root. Live at
`https://paragchettri12.github.io/<repo-name>/` within a minute or two.

If you name the repo `ParagChettri12.github.io` exactly, it serves from the bare
domain instead.

**Netlify / Cloudflare Pages** — drag the `site` folder onto their dashboard.
No build command, publish directory `.`.

Either way, delete `INSTRUCTIONS.md` first, or at least don't link to it.

**Note on the Kanchenjunga embed:** the ArcGIS iframe needs `https://` to load,
so it will look broken locally over `file://` in some browsers and work fine
once hosted.

---

## 8. The design system

**Prose on paper, instruments on plate.** Long-form argument sits on warm
off-white (`--paper`, #F5F4EF). Every interactive widget sits on a near-black
plate (`--plate`, #0D0F12). That alternation is the whole rhythm of the site —
it makes "this is something you read" and "this is something you poke" visually
distinct without any extra chrome.

Components don't know which context they're in. `.plate` simply redefines
`--bg / --fg / --dim / --rule / --acc-*` and every table, chip, metric, slider
and plot inside it re-skins itself. So to move a widget from paper to plate you
add one class — nothing else changes.

| Class | Effect |
|---|---|
| `.plate` | dark context, re-skins all children |
| `.bleed` | escapes the centred shell, goes edge-to-edge |
| `.wide` | breaks the text measure without going full width |
| `.plate .plate-inner` | re-centres content inside a bleeding plate |

**Accents.** `--cold` (#275F97) and `--warm` (#AE3E27) are a diverging pair from
CMB temperature colormaps. `--cold` marks anything below expectation —
undercoverage, nulls, subtracted foreground. `--warm` marks anything above it.
They're data ink, not decoration. Swap them for something arbitrary and that
logic breaks quietly. Each has a lighter variant (`--cold-p`, `--warm-p`) used
automatically inside plates, plus `--on-acc` for text sitting on an accent fill,
which flips between white and near-black by context.

**Type** is Newsreader (variable optical sizing, so display and body come from
one family) for reading, and Archivo for labels, controls and all numbers —
tabular figures are on everywhere numbers appear in a column. Loaded from Google
Fonts with `preconnect` and system fallbacks. To go fully offline, download both
families and replace the `<link>` block in each page's `<head>`.

**Accessibility.** Every text/background pair in both contexts clears WCAG AA
(4.5:1) — I checked them numerically and darkened two that didn't. Keyboard focus
is visible, `prefers-reduced-motion` is respected, plots carry `aria-label`
descriptions, and the layout holds down to a 360 px viewport.

### One thing I could not check

There is no browser in the environment I built this in, so **the layout has never
actually been rendered.** Everything is verified structurally — all internal links
resolve, every page's JavaScript executes without error, all interactive controls
were driven programmatically and assert correct output, contrast is computed — but
if something looks off in a real browser, that's why. Worth opening every page
once before you push.
