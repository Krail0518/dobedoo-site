# Do Be Doo Be Doo

Brand site for **Do Be Doo Be Doo** — premium craft nano-infused lemonade with 5 mg of hemp-derived Delta-9 THC per can. Made in Florida.

**Live:** [krail0518.github.io/dobedoo-site](https://krail0518.github.io/dobedoo-site/)

## What's in the site

- **Hero** — animated headline, four-can showcase, cursor-tracked spotlight + halo, gentle bob on the center can, ambient gold particles, starburst rays, scroll cue
- **Flavors** — Classic / Raspberry / Strawberry / Arnold Palmer, each as a CSS+SVG can mockup with flavor-specific fruit overlays (dome-cluster raspberries, heart-shaped strawberries with sepal star, two-leaves-and-a-bud tea sprigs)
- **The Craft** — four pillars (Nano Emulsified, 5MG Every Can, Independently Tested, Short Ingredient List)
- **Vibe Quiz** — three-question flow that scores answers and recommends a flavor
- **Story** — origin narrative with drop-cap
- **Lab** — full Supplement Facts panel + Certificate of Analysis link
- **CTA + Footer** — email signup, link columns, legal disclaimer

## Effect stack

19 layered animations / interactions running together:

cursor spotlight · breathing halo · floating center can · gold shimmer headline · scroll cue · card hover (lift + scale + glow + sheen + 3D tilt) · marquee glow + pause + scroll-velocity acceleration · nav underline wipe · scroll parallax (3 speeds) · sticky 5MG badge with spinning ring text · splash starburst · section heading underline reveal · custom cursor (dot + lerped ring + states) · scroll progress meter + readout · ambient particles · number counters · magnetic buttons · vibe quiz · Konami party mode · opt-in sound design · hero can cursor-tilt.

All respect `prefers-reduced-motion`. Touch devices skip cursor/particles/magnetic. Every scroll/pointer handler is rAF-throttled.

## Easter egg

Press **↑ ↑ ↓ ↓ ← → ← → B A** anywhere on the page for party mode.

## Files

| File | What |
|---|---|
| `index.html` | The whole site — self-contained: inline CSS, inline JS, inline SVG, base64-embedded label image. |
| `lemonade label 3.png` | Source label artwork (kept as a reference; the runtime uses the inlined base64 copy on line 391 of `index.html`). |
| `CLAUDE.md` | Project documentation for AI coding agents — brand system, the can mockup technique, page structure, conventions, known gotchas. |

## Editing

No build step. No package.json. No framework. Open `index.html` in a browser or serve the folder statically:

```bash
npx serve .
# or
python -m http.server 5173
```

Every commit to `main` re-publishes via GitHub Pages.

### Editing gotchas

- The base64 label blob lives on **line 391** of `index.html` as a single very long line. Avoid reading it into context — read line ranges 1–390 and 392–end instead.
- The cans are pure CSS/HTML constructs (no can image). Fruit overlays are inline SVG positioned over the lemon zone of the label.
- The tint band, label `background-position`, and every fruit-SVG position are hand-tuned to this specific label crop. If the label image is swapped, every overlay position needs re-tuning.

## Stack

HTML + CSS + vanilla JS. Web Audio API for the opt-in sound design. IntersectionObserver for reveals. CSS variables for cursor-driven effects. Two external resources: Google Fonts (Anton, Barlow, Barlow Condensed, Caveat Brush) loaded over CDN.

## Compliance

Site is age-gated 21+. Product contains hemp-derived cannabinoids. Sale and possession laws vary by jurisdiction — confirm legal status in your delivery area before launch.

## License

All rights reserved. Brand, copy, and assets © Do Be Doo Be Doo.
