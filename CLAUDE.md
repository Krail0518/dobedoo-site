# CLAUDE.md — Do Be Doo Be Doo brand site

Single-page brand site for **Do Be Doo Be Doo**, a premium craft nano-infused
(hemp-derived Delta-9 THC, 5mg/can) lemonade brand, made in Florida. Marketing
one-pager, age-gated 21+.

**Live:** https://krail0518.github.io/dobedoo-site/
**Repo:** https://github.com/Krail0518/dobedoo-site (auto-deploys on push to `main`)

## Files

- `index.html` — the entire site. ~1860 lines, ~740 KB. **Self-contained**: no
  build step, no framework, no JS dependencies. Only external resource is Google
  Fonts (CDN `<link>` in the head). Everything else (CSS, SVG, the label image,
  the SVG favicon) is inline.
- `lemonade label 3.png` — ~2 MB source label artwork. A copy of this is inlined
  as a **base64 PNG data-URI on line 391** (the `background-image` of
  `.can__label`). The PNG file itself is *not* referenced by the page at runtime;
  the page uses the inlined copy.
- `og.svg` — source for the Open Graph social-preview image.
- `og.png` — 1200×630 PNG generated from `og.svg` via `npx sharp-cli`. Referenced
  from `<meta property="og:image">` in `index.html`. **Regenerate** with:
  `npx --yes -p sharp-cli sharp -i og.svg -o og.png resize 1200 630`
- `README.md` — public repo documentation.
- `.gitignore` — excludes the local `.claude/` settings folder.

## Editing gotchas (read before changing the file)

- **The base64 label blob lives on line 391** as one enormous single line. Read
  the file in two ranges (1–390 and 392–end) to avoid pulling the blob into
  context. Line numbers below assume the current file.
- The cans are **pure CSS/HTML constructs — there is no can image.** Don't go
  looking for can PNGs.
- The tint mask and fruit overlays are **hand-tuned to this specific label crop.**
  If the label image changes, the tint band, `background-position`, and every
  fruit-SVG position will need re-tuning (see below).

## Brand system

Fonts (CSS vars in `:root`, ~line 24):
- `--display` **Anton** — all big headlines, names, the brand wordmark, spec values
- `--cond` **Barlow Condensed** — eyebrows, labels, buttons, kickers (uppercase, tracked)
- `--body` **Barlow** — paragraph copy
- `--brush` **Caveat Brush** — script accents ("your", "vibe", "everything else second")

Colors (`:root`, lines 12–28):
- `--bg #0a0a0a`, `--bg-2 #111`, dark theme throughout
- `--ink #f7f3e9` (+ `--ink-dim`, `--ink-mute`)
- `--gold #f7b500` / `--gold-hot #ffc933` — primary accent
- `--raspberry #7c3aed` (deliberate violet, not pink)
- `--strawberry #dc2626` (true red — see "Strawberry tint" below)
- `--arnold #15803d` (Arnold Palmer green — declared in `:root` since 2026-05-28)

## The can mockup (the central visual technique)

Each can is a stack of absolutely-positioned divs inside `.can` (`.can--hero` 300px
wide, `.can--card` 240px). Layer order:

1. `.can__top` / `.can__rim` / `.can__top-shade` — aluminum lid via linear gradients
   + elliptical `border-radius:50%/100%`.
2. `.can__body` — clips the label (`overflow:hidden`). Contains:
   - `.can__label` — the inlined base64 label as `background-image`,
     `background-size:auto 100%`, `background-position:38% center`. Shows a
     vertical slice of the wide label so it reads as "wrapped" around the cylinder.
   - `.can__tint` + `.can__tint2` — **flavor recolor.** Most flavors use
     `mix-blend-mode:hue` (tint) + `mix-blend-mode:color` (tint2) to recolor the
     gold zones while preserving the lemon yellows via a vertical mask
     (transparent 54%–86%, opaque elsewhere). Modifiers: `--rasp`, `--straw`,
     `--arnold`.
   - **Strawberry tint exception:** `.can__tint--straw` uses
     `mix-blend-mode:multiply` with `#b91c1c`, and `.can__tint2--straw` uses
     `color` blend at `.7` opacity. Why: hue-blend preserves the label's luminosity,
     so applying red to bright gold rendered as pink/coral. Multiply darkens the
     gold zones to a true red. Don't change back to `hue` unless you want pink.
   - `.can__shade` — left/right darkening gradient = cylindrical curvature.
   - `.can__sheen` — narrow vertical highlight near center.
   - `.can__fruit` — absolutely-positioned **inline SVG fruit** plus a colored
     text pill. The SVGs use `preserveAspectRatio="xMidYMid meet"` (not `none`) —
     the container is ~60×125px (tall), so `none` stretches everything vertically
     into finger shapes. Keep `meet`.

  Fruit SVG designs:
  - **Raspberry** — compact dome cluster of drupelets (4 rows tapering up),
    soft drop shadow, highlight dots, small 4-leaf green sepal on top.
  - **Strawberry** — wide-shouldered teardrop body, side highlight curve, dimpled
    seed pattern (cream tear-drops), 5-point sepal star calyx on top.
  - **Arnold Palmer** — classic *Camellia sinensis* "two leaves and a bud" tea
    sprig (central stem, 4 lanceolate leaves, closed bud tip). 3 SVG instances
    per can: 2 large sprigs + 1 small accent.

3. `.can__bottom` / `.can__bottom-shade` — aluminum base.

**The Original/Classic Lemonade can has no tint or fruit layers** — it's the native
gold label as-is.

Hero "fan" of 4 cans (`.hero__cans`): Arnold Palmer `.can--far-left`,
Raspberry `.can--left`, Original `.can--center` (raised, front), Strawberry
`.can--right`, positioned via `translateX` + `rotate`. Responsive: far-left hides
<680px, both sides hide <480px (center only).

## Page structure (top → bottom)

1. **Age gate** `#gate` — fixed overlay, "Are you 21+?". JS uses `sessionStorage`
   key `verified`; "Yes" sets it and removes `.hidden`; "No" redirects to google.com.
2. **Nav** `.nav` — sticky, blurred. Brand wordmark, links (Flavors / The Craft /
   Your Vibe / Story / Lab), **sound toggle button** (speaker icon, opt-in audio),
   "Find a Store" CTA. Links hide <900px.
3. **Hero** `.hero` — eyebrow, headline (with gold gradient shimmer animation on
   accent words), sub copy, two CTAs, the 4-can fan with breathing halo + starburst
   behind center, ambient gold particles, scroll-cue mouse icon at bottom.
4. **Marquee** `.marquee` — infinite-scroll tagline strip. Duration controlled by
   `--marq-dur` CSS var; JS adjusts on scroll velocity (6s when scrolling fast,
   30s when idle).
5. **Flavors** `#flavors` `.products` — 4 `<article class="flavor flavor--*">` cards.
6. **Craft** `#craft` `.craft` — 4 pillars (Nano Emulsified / 5MG Every Can /
   Independently Tested / Short Ingredient List).
7. **Vibe Quiz** `#vibe` `.vibe-quiz` — 3-question quiz, scores answers to flavors
   via `data-flavors` attribute, reveals winner on a result card. Self-resets via
   "Try again". State held in JS closure.
8. **Story** `#story` — two-column philosophy text, drop-cap first letter.
9. **Facts** `#lab` `.facts` — Supplement Facts nutrition panel + "View Latest
   Certificate of Analysis" callout button.
10. **CTA** `#find` `.cta` — email signup form. Submit is faked inline (clears
    input, sets button text to "Cheers ✦"); no backend.
11. **Footer** `.foot` — brand, Shop / Brand / Connect link columns, legal
    disclaimer, copyright.

## Global overlay elements (outside any section)

- **Sticky 5MG badge** — fixed right-side circular stamp with spinning ring text
  ("PRECISION DOSED · LAB VERIFIED"). Springs in at `scrollY > 500`. Hidden <720px.
- **Custom cursor** — gold ring + white dot, `mix-blend-mode:difference`. Ring
  lerps toward the pointer; ring grows on `.cursor-hover` (any `a/button/.flavor`)
  and shrinks on `.cursor-press`. Disabled on touch + reduced-motion.
- **Scroll progress** — 3px gold meter on the left edge + vertical `% Scrolled`
  readout that fades in while scrolling, fades out 700ms after.

## The effect stack (JS-driven, all at the bottom of `index.html`)

All scroll/pointer handlers are rAF-throttled and `passive:true`. Reduced-motion
disables everything. Touch devices skip cursor / particles / magnetic.

1. **IntersectionObserver `.rise` reveals** — fade + slide-up + 6px blur unblur,
   `.85s` cubic-bezier; delay variants `.d1`/`.d2`/`.d3`.
2. **Age-gate sessionStorage** — same as before.
3. **Hero cursor spotlight** — sets `--mx`/`--my` on `.hero` from pointermove.
4. **Scroll parallax** — sets `--parallax-y` on `.hero__cans` / `.hero__title` /
   `.hero__top` at different speeds (-18%, -6%, -3% of scrollY).
5. **3D card tilt** — pointermove on each `.flavor`; sets `--rx`/`--ry`/`--gx`/`--gy`.
   Resets on pointerleave.
6. **Sticky 5MG badge reveal** — toggles `body.show-badge` past `scrollY > 500`.
7. **Custom cursor** — dot snaps; ring lerps at `0.18`. `cursor-hover` /
   `cursor-press` body classes drive size changes.
8. **Scroll progress meter** — updates `--progress` height %; vertical readout.
9. **Number counters** — IntersectionObserver on `.hero__meta .v`, animates from
   0 with `easeOutCubic`. "FLORIDA" gets typewriter effect (non-numeric).
10. **Magnetic buttons** — `.btn` / `.nav__cta` / `.gate__btn--yes` pull toward
    cursor within 110px radius.
11. **Ambient particles** — 28 randomly-positioned gold radial-gradient dots
    drifting upward across hero. JS-generated on load.
12. **Marquee acceleration** — measures `|dy/dt|`, remaps to 6–30s animation duration.
13. **Vibe Quiz** — click handler on `.vibe-quiz__panel`. Each option has
    `data-flavors="orig,straw"`; tallies, picks winner, renders result.
14. **Konami easter egg** — `↑↑↓↓←→←→ B A` toggles `body.party-mode` (rainbow
    text, wobbling cans, bouncing badge, rainbow nav, party banner).
15. **Sound toggle** — Web Audio API. Triangle tone on hover (1400 Hz, 0.04s,
    vol 0.018), sine tone on click (660 Hz, 0.09s). Persisted to `localStorage`
    key `dbd-sound`. Default off. Throttled to 50ms gap.
16. **Hero can cursor-tilt** — pointermove on `.hero`; sets `--tilt-x`/`--tilt-y`
    on `.hero__cans` for ±3°/±6° rotation toward cursor.

## Conventions

- Copy spells out industry acronyms in user-facing text (e.g. "Certificate of
  Analysis", not "COA"). Keep this when editing.
- BEM-ish class naming (`.block__element--modifier`).
- All styling is in the single `<style>` block; all markup follows it. Keep the
  file self-contained — no external CSS/JS files.
- Multiple distinct "layers" of CSS appended at the bottom (`WOW LAYER`,
  `NEXT-LEVEL LAYER`, `FURTHER NEXT-LEVEL`, `KONAMI PARTY MODE`). Keep adding
  to the bottom rather than weaving into the original CSS — easier to reason about.

## Deployment

GitHub Pages, source `main` branch / root. Every push to `main` triggers a rebuild
(typically 30–60s). Check status:

```bash
gh api repos/Krail0518/dobedoo-site/pages --jq '.status,.html_url'
```

## Resolved historical issues

These are fixed in the current code — kept here for context only:
- `--arnold` CSS var was undefined → now declared in `:root`.
- `<div class="hero__meta    <div class="hero__meta rise in d3">` malformed →
  collapsed to single tag.
- Strawberry tint rendered as pink/coral → switched to `multiply` blend mode.
- Strawberry/raspberry fruit SVGs looked stretched/wrong → switched
  `preserveAspectRatio` from `none` to `xMidYMid meet`, redesigned shapes.
- Arnold Palmer leaves looked like jalapeños → replaced with tea-sprig SVGs.
