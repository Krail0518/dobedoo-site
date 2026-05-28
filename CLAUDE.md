# CLAUDE.md — Do Be Doo Be Doo brand site

Single-page brand site for **Do Be Doo Be Doo**, a premium craft nano-infused
(hemp-derived Delta-9 THC, 5mg/can) lemonade brand, made in Florida. Marketing
one-pager, age-gated 21+.

## Files

- `index.html` — the entire site. 1122 lines, ~685 KB. **Self-contained**: no
  build step, no framework, no JS dependencies. Only external resource is Google
  Fonts (CDN `<link>` in the head). Everything else (CSS, SVG, the label image)
  is inline.
- `lemonade label 3.png` — ~2 MB source label artwork. A copy of this is inlined
  as a **base64 PNG data-URI on line 391** (the `background-image` of
  `.can__label`). The PNG file itself is *not* referenced by the page at runtime;
  the page uses the inlined copy.

There is no package.json, no server, no tooling. To view: open `index.html` in a
browser (or serve the folder statically).

## Editing gotchas (read before changing the file)

- **The base64 blob lives on line 391** as one enormous single line. Read the file
  in two ranges (1–390 and 392–end) to avoid pulling the blob into context. Line
  numbers below assume the current file.
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
- `--brush` **Caveat Brush** — script accents ("your", "vibe", "it's a vibe")

Colors (`:root`, lines 12–28):
- `--bg #0a0a0a`, `--bg-2 #111`, dark theme throughout
- `--ink #f7f3e9` (+ `--ink-dim`, `--ink-mute`)
- `--gold #f7b500` / `--gold-hot #ffc933` — primary accent
- `--raspberry #7c3aed` (purple), `--strawberry #dc2626` (red)
- **Arnold Palmer green is `#15803d`** but is hardcoded, see Known Issues.

## The can mockup (the central visual technique)

Each can is a stack of absolutely-positioned divs inside `.can` (`.can--hero` 300px
wide, `.can--card` 240px). CSS at lines ~355–489. Layer order:

1. `.can__top` / `.can__rim` / `.can__top-shade` — aluminum lid via linear gradients
   + elliptical `border-radius:50%/100%`.
2. `.can__body` — clips the label (`overflow:hidden`). Contains:
   - `.can__label` — the inlined base64 label as `background-image`,
     `background-size:auto 100%`, `background-position:38% center`. This shows a
     vertical slice of the wide label so it reads as "wrapped" around the cylinder.
   - `.can__tint` + `.can__tint2` — **flavor recolor.** `--tint` uses
     `mix-blend-mode:hue`, `--tint2` uses `mix-blend-mode:color` (extra saturation).
     Both share a vertical `mask: linear-gradient(...)` that is **transparent over
     the 54%–90% vertical band** so the lemons in that band stay yellow while the
     gold areas above/below recolor. Modifiers: `--rasp`, `--straw`, `--arnold`.
   - `.can__shade` — left/right darkening gradient = cylindrical curvature.
   - `.can__sheen` — narrow vertical highlight near center.
   - `.can__fruit` — absolutely-positioned **inline SVG fruit** (raspberry circle
     clusters, strawberries with seeds + leaves, tea leaves for Arnold Palmer) plus
     a colored text pill naming the fruit. Positions (left/top/width/height %) are
     hand-tuned to sit over the lemons.
3. `.can__bottom` / `.can__bottom-shade` — aluminum base.

**The Original/Classic Lemonade can has no tint or fruit layers** — it's the native
gold label as-is.

Hero "fan" of 4 cans (`.hero__cans`, CSS ~454–489): Arnold Palmer `.can--far-left`,
Raspberry `.can--left`, Original `.can--center` (raised, front), Strawberry
`.can--right`, positioned via `translateX` + `rotate`. Responsive: far-left hides
<680px, both sides hide <480px (center only).

## Page structure (top → bottom)

1. **Age gate** `#gate` — fixed overlay, "Are you 21+?". JS uses `sessionStorage`
   key `verified`; "Yes" sets it and removes `.hidden`; "No" redirects to google.com.
2. **Nav** `.nav` — sticky, blurred. Brand wordmark, links (Flavors / The Craft /
   Story / Lab), "Find a Store" CTA, mobile burger (links hide <900px; burger is
   decorative — no menu wired up).
3. **Hero** `.hero` `#`(top) — eyebrow, headline with brush accent, sub copy, two
   CTAs, the 4-can fan, then a meta strip (Dose / Volume / Tested / Made In).
4. **Marquee** `.marquee` — infinite-scroll tagline strip, CSS `@keyframes slide`.
5. **Flavors** `#flavors` `.products` — 4 `<article class="flavor flavor--*">` cards
   (`--orig`, `--rasp`, `--straw`, `--arnold`), each with a `.can--card` mockup,
   name, description, and specs (THC / Calories / Size).
6. **Craft** `#craft` `.craft` — 4 pillars (Nano Infused, Precision Dosed,
   Third-Party Tested, Clean Label) with inline SVG icons.
7. **Story** `#story` — two-column philosophy text, drop-cap first letter.
8. **Facts** `#lab` `.facts` — Supplement Facts nutrition panel + Certificate of
   Analysis callout button.
9. **CTA** `#find` `.cta` — email signup form. Submit is faked inline (clears input,
   sets button text to "Cheers ✦"); no backend.
10. **Footer** `.foot` — brand, Shop / Brand / Connect link columns, legal
    disclaimer, copyright.

## JavaScript (bottom of file, ~lines 1108–1119)

Two small things, no libraries:
- `IntersectionObserver` adds `.in` to `.rise` elements → fade/slide-up reveal on scroll.
- Age-gate sessionStorage logic (see Age gate above).

## Known issues (verified, not yet fixed)

1. **`--arnold` CSS variable is undefined.** `var(--arnold)` is referenced at
   line 213 (`.flavor--arnold` accent) and lines 583 & 942 (the "Arnold Palmer"
   name-pill `background`), but `--arnold` is never declared in `:root`. Those
   resolve to invalid/transparent, so the Arnold Palmer card's accent color (hover
   border, tag pill, name `.acc`, spec values) and the pill background are broken.
   The green only appears via the hardcoded `#15803d` in `.can__tint--arnold` /
   `.can__tint2--arnold`. **Fix:** add `--arnold:#15803d;` to `:root`.
2. **Malformed div, line 704:** `<div class="hero__meta    <div class="hero__meta
   rise in d3">` — a botched find/replace left a duplicated/nested opening tag, so
   the hero meta strip's `class` attribute is mangled. **Fix:** replace with a
   single `<div class="hero__meta rise in d3">`.

## Conventions

- Copy spells out industry acronyms in user-facing text (e.g. "Certificate of
  Analysis", not "COA"). Keep this when editing.
- BEM-ish class naming (`.block__element--modifier`).
- All styling is in the single `<style>` block; all markup follows it. Keep the
  file self-contained — no external CSS/JS files.
