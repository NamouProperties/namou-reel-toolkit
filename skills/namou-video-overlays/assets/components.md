# Namou Video Overlay Components

Thirteen reusable overlay patterns. The first four came out of the Nadim ad (the first build with this kit); the rest were added by later builds. Each pattern is independent — mix and match per scene.

All patterns assume a 9:16 (1080×1920) HyperFrames composition with a base `<video muted>` element on `data-track-index="0"` and an `<audio>` on `data-track-index="1"`. Overlays start at `data-track-index="2"` and go up. Always set `class="clip"` on overlay elements with timing.

For full motion principles, color palette, and type scale, see [`design.md`](./design.md). For a full worked composition, see [`examples/nadim-ad-1/index.html`](./examples/nadim-ad-1/index.html) (a code reference — its media files are not included).

## Contents

| # | Pattern | Reach for it when |
|---|---|---|
| 1 | Image inset card (top-right) | showing a render, partner logo, or location photo |
| 2 | Big-stat lower-third (dark forest) | one big number under the speaker |
| 3 | Big-stat card (white, editorial) | a headline stat with a sign, like "+30%" |
| 4 | Forest-gradient CTA closer | the last 4–5s of every video |
| 5 | Arrow callout | pointing at something in the footage |
| 6 | Big-stat hero card with LIVE indicator | a live or current figure (variant of 3) |
| 7 | JV alignment chips with connectors | showing what each party brings to a deal |
| 8 | Top-corner audience badge | labelling who the video is for |
| 9 | Feature cascade card | a short list of features, revealed in sequence |
| 10 | Outcome ticker (white card, checkmark rows) | build, hold, or rent outcomes |
| 11 | Rank badge (countdown) | the "#N" marker in a top-N countdown |
| 12 | Ranked area card (image + name + tag) | the area itself in a countdown, pairs with 11 |
| 13 | **Green pin drop + plot polygon (map call-out)** | **showing where a land plot sits — the land-reel workhorse** |

---

## Pattern 1 — Image inset card (top-right)

**Use for:** showing a related image (project render, partner brand, location photo) while the speaker mentions it. The card MUST NOT cover the speaker's face — anchor it to the top-right corner at 320×426px.

**Components:** image fill + forest-green gradient over the bottom 55% + small DM Sans uppercase label + Instrument Serif italic name in mint.

```html
<div
  id="wynn-card"
  class="clip"
  data-start="6.5"
  data-duration="5.0"
  data-track-index="2"
>
  <img src="wynn-casino.png" alt="..." crossorigin="anonymous" />
  <div class="wynn-tag">
    Anchor resort
    <em>Wynn Al Marjan Island</em>
  </div>
</div>
```

```css
#wynn-card {
  position: absolute;
  top: 60px; right: 40px;
  width: 320px; height: 426px;
  border-radius: 20px;
  overflow: hidden;
  z-index: 2;
  box-shadow: 0 18px 44px rgba(0, 0, 0, 0.45);
  background: #000;
}
#wynn-card img { width: 100%; height: 100%; object-fit: cover; }
#wynn-card::after {
  content: "";
  position: absolute; left: 0; right: 0; bottom: 0;
  height: 55%;
  background: linear-gradient(
    to top,
    rgba(0, 61, 46, 0.92) 0%,
    rgba(0, 61, 46, 0.65) 45%,
    rgba(0, 61, 46, 0) 100%
  );
  pointer-events: none;
}
#wynn-card .wynn-tag {
  position: absolute;
  left: 18px; right: 18px; bottom: 18px;
  z-index: 2;
  color: #FDFDFD;
  font-family: "DM Sans", sans-serif;
  font-weight: 500;
  font-size: 17px;
  letter-spacing: 0.06em;
  text-transform: uppercase;
}
#wynn-card .wynn-tag em {
  display: block;
  font-family: "Instrument Serif", serif;
  font-style: italic;
  font-size: 36px;
  text-transform: none;
  letter-spacing: 0;
  margin-top: 4px;
  line-height: 1.02;
  color: #3FE6A8; /* --mint */
}
```

```js
// Entrance: slide DOWN from above (vertical in)
tl.from("#wynn-card", { y: -540, opacity: 0, duration: 0.7, ease: "power3.out" }, dataStart + 0.1);
tl.from("#wynn-card .wynn-tag", { y: 24, opacity: 0, duration: 0.5, ease: "power2.out" }, dataStart + 0.45);
// Exit: slide OUT to right (horizontal out), 0.6s before clip ends
tl.to("#wynn-card", { x: 500, opacity: 0, duration: 0.5, ease: "power2.in" }, dataStart + dataDuration - 0.6);
```

---

## Pattern 2 — Big-stat lower-third (dark forest)

**Use for:** a single big number with a context label, sitting under the speaker. Forest green BG with mint accent number. Often pairs with Pattern 1 in the same scene.

```html
<div
  id="hotels-bar"
  class="clip"
  data-start="5.0"
  data-duration="7.0"
  data-track-index="3"
>
  <div class="num">28</div>
  <div class="label">
    Hotels opening
    <em>by 2030</em>
  </div>
</div>
```

```css
#hotels-bar {
  position: absolute;
  left: 60px; right: 60px; bottom: 200px;
  z-index: 3;
  background: #003D2E; /* --bg-deep */
  color: #FDFDFD;
  border-radius: 20px;
  padding: 36px 44px;
  display: flex; align-items: center; gap: 32px;
  box-shadow: 0 18px 50px rgba(0, 0, 0, 0.35);
}
#hotels-bar .num {
  font-family: "DM Sans", sans-serif;
  font-weight: 700;
  font-size: 132px;
  line-height: 1;
  color: #3FE6A8; /* --mint */
  font-variant-numeric: tabular-nums;
}
#hotels-bar .label {
  font-family: "DM Sans", sans-serif;
  font-weight: 500;
  font-size: 30px;
  line-height: 1.15;
  letter-spacing: 0.04em;
  text-transform: uppercase;
}
#hotels-bar .label em {
  display: block;
  font-family: "Instrument Serif", serif;
  font-style: italic;
  font-size: 38px;
  text-transform: none;
  letter-spacing: 0;
  margin-top: 6px;
  color: #3FE6A8; /* --mint */
}
```

```js
// Entrance: rise UP from below (vertical in)
tl.from("#hotels-bar", { y: 80, opacity: 0, duration: 0.65, ease: "power3.out" }, dataStart + 0.1);
// Count-up animation (deterministic, no Math.random):
tl.from("#hotels-bar .num", {
  textContent: 0,
  duration: 1.1,
  ease: "expo.out",
  snap: { textContent: 1 },
}, dataStart + 0.5);
tl.from("#hotels-bar .label", { x: -24, opacity: 0, duration: 0.55, ease: "power2.out" }, dataStart + 0.4);
// Exit: slide OUT to left (horizontal out), 0.6s before clip ends
tl.to("#hotels-bar", { x: -1100, opacity: 0, duration: 0.5, ease: "power2.in" }, dataStart + dataDuration - 0.6);
```

---

## Pattern 3 — Big-stat card (white, editorial)

**Use for:** a single big stat that deserves hero weight. Number + sign (e.g. "+30%") in Namou accent green on a clean white card. Carries more editorial weight than Pattern 2.

```html
<div
  id="price-stat"
  class="clip"
  data-start="14.6"
  data-duration="6.4"
  data-track-index="4"
>
  <div class="num">
    <span class="sign">+</span><span id="price-num">0</span>%
  </div>
  <div class="label">
    Land prices
    <span class="sub">Ras Al Khaimah · Last 3 years</span>
  </div>
</div>
```

```css
#price-stat {
  position: absolute;
  left: 60px; right: 60px; bottom: 200px;
  z-index: 3;
  background: #FDFDFD; /* --bg */
  color: #121212; /* --ink */
  border-radius: 20px;
  padding: 44px 48px;
  box-shadow: 0 18px 50px rgba(0, 0, 0, 0.18);
}
#price-stat .num {
  font-family: "DM Sans", sans-serif;
  font-weight: 700;
  font-size: 220px;
  line-height: 0.9;
  color: #00B073; /* --accent */
  font-variant-numeric: tabular-nums;
  letter-spacing: -0.02em;
}
#price-stat .num .sign {
  font-size: 140px;
  vertical-align: top;
  line-height: 1;
}
#price-stat .label {
  font-family: "DM Sans", sans-serif;
  font-weight: 500;
  font-size: 32px;
  line-height: 1.2;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  margin-top: 12px;
}
#price-stat .label .sub {
  display: block;
  font-weight: 400;
  font-size: 24px;
  color: #555555; /* --ink-muted */
  letter-spacing: 0.02em;
  margin-top: 6px;
}
```

```js
// Entrance: rise UP from below (vertical in)
tl.from("#price-stat", { y: 100, opacity: 0, duration: 0.7, ease: "power3.out" }, dataStart + 0.1);
tl.fromTo("#price-num", { textContent: 0 }, {
  textContent: 30, // target number — change per use
  duration: 1.3,
  ease: "expo.out",
  snap: { textContent: 1 },
}, dataStart + 0.5);
tl.from("#price-stat .label", { y: 20, opacity: 0, duration: 0.5, ease: "power2.out" }, dataStart + 0.4);
// Exit: slide OUT to right (horizontal out), 0.6s before clip ends
tl.to("#price-stat", { x: 1100, opacity: 0, duration: 0.5, ease: "power2.in" }, dataStart + dataDuration - 0.6);
```

---

## Pattern 4 — Forest-gradient CTA closer

**Use for:** end-of-video CTA. Sits over the bottom-third of the frame with a deep forest gradient that fades upward. Italic Instrument Serif pull-quote line (with one emphasis word in mint) + accent-green pill button. Should run 4–5 seconds — long enough to read AND tap.

```html
<!-- Gradient shade (sits behind the text) -->
<div
  id="cta-shade"
  class="clip"
  data-start="25.0"
  data-duration="5.0"
  data-track-index="5"
></div>

<!-- Text stack -->
<div
  id="cta-stack"
  class="clip"
  data-start="25.2"
  data-duration="4.8"
  data-track-index="6"
>
  <div class="pull">
    <span>Walk</span> the plots, live.
  </div>
  <div class="pill">Book a video walkthrough</div>
</div>
```

```css
#cta-shade {
  position: absolute;
  left: 0; right: 0; bottom: 0;
  height: 1100px;
  z-index: 1;
  background: linear-gradient(
    to top,
    rgba(0, 61, 46, 0.98) 0%,
    rgba(0, 61, 46, 0.96) 55%,
    rgba(0, 61, 46, 0.82) 72%,
    rgba(0, 61, 46, 0.35) 88%,
    rgba(0, 61, 46, 0) 100%
  );
  pointer-events: none;
}
#cta-stack {
  position: absolute;
  left: 60px; right: 60px; bottom: 240px;
  z-index: 3;
  text-align: center;
}
#cta-stack .pull {
  font-family: "Instrument Serif", serif;
  font-style: italic;
  font-size: 96px;
  line-height: 1.05;
  color: #FDFDFD;
  text-shadow: 0 4px 24px rgba(0, 0, 0, 0.55);
}
#cta-stack .pull span {
  color: #3FE6A8; /* --mint emphasis word */
}
#cta-stack .pill {
  display: inline-block;
  margin-top: 32px;
  padding: 22px 48px;
  background: #00B073; /* --accent */
  color: #FDFDFD;
  font-family: "DM Sans", sans-serif;
  font-weight: 600;
  font-size: 40px;
  letter-spacing: 0.06em;
  text-transform: uppercase;
  border-radius: 999px;
  box-shadow: 0 12px 32px rgba(0, 176, 115, 0.45);
}
```

```js
// Entrance only — no exit. Pattern 4 is the closer; it holds until the video ends.
tl.from("#cta-shade", { opacity: 0, duration: 0.6, ease: "power2.out" }, dataStart + 0.1);
tl.from("#cta-stack .pull", { y: 40, opacity: 0, duration: 0.7, ease: "power3.out" }, dataStart + 0.3);
tl.from("#cta-stack .pill", { y: 30, opacity: 0, scale: 0.92, duration: 0.55, ease: "back.out(1.6)" }, dataStart + 0.9);
```

---

## Pattern 5 — Arrow callout

**Use for:** drawing the viewer's eye to a real element in the frame (laptop screen, product, sign, person's gesture) while a paired label explains what they're looking at. Animated SVG arrow that **draws itself** from a label to a target point. Built in `nadim-ad-2` to point at Nadim's laptop while introducing the "Live Deal Sessions" platform.

**Anatomy:** a `<svg>` overlay covering the full frame (1080×1920) + a `<path>` for the curve + a `<polygon>` arrowhead at the endpoint. Stroke-dash animation makes it draw.

```html
<!-- Full-frame SVG overlay (pointer-events: none) -->
<svg
  id="my-arrow"
  class="clip"
  viewBox="0 0 1080 1920"
  data-start="12.0"
  data-duration="3.5"
  data-track-index="4"
  data-layout-allow-overflow
>
  <path
    id="my-arrow-path"
    d="M 660 230 C 700 430, 620 820, 510 1100"
    stroke-dasharray="1200"
    stroke-dashoffset="1200"
  />
  <polygon
    id="my-arrow-head"
    class="arrow-head"
    points="510,1100 488,1064 526,1064"
    opacity="0"
  />
</svg>

<!-- Pair with a label at the arrow's origin -->
<div id="my-arrow-label" class="clip" data-start="11.8" data-duration="3.7" data-track-index="5" data-layout-allow-overflow>
  <div class="small">Introducing</div>
  <div class="big">Live Deal Sessions</div>
</div>
```

```css
#my-arrow { position: absolute; inset: 0; width: 1080px; height: 1920px; z-index: 9; pointer-events: none; }
#my-arrow path {
  fill: none;
  stroke: #3FE6A8; /* mint */
  stroke-width: 5;
  stroke-linecap: round;
  stroke-linejoin: round;
  filter: drop-shadow(0 4px 12px rgba(0, 176, 115, 0.45));
}
#my-arrow .arrow-head { fill: #3FE6A8; stroke: none; }
```

```js
// Path stroke-dashoffset draws the line; arrowhead pops in once line completes
tl.fromTo("#my-arrow-path",
  { strokeDashoffset: 1200 },                     // start hidden
  { strokeDashoffset: 0, duration: 0.85, ease: "power2.inOut" },
  dataStart + 0.55,
);
tl.fromTo("#my-arrow-head",
  { opacity: 0, scale: 0.6, transformOrigin: "510px 1083px" },
  { opacity: 1, scale: 1, duration: 0.25, ease: "back.out(2)" },
  dataStart + 1.35,
);
// Exit: arrow fades, label slides off (horizontal — motion rule)
tl.to(["#my-arrow-path", "#my-arrow-head"], { opacity: 0, duration: 0.4, ease: "power2.in" }, dataStart + dataDuration - 0.55);
```

**Tuning the path:**
- `M x1 y1` — origin (anchor near the label)
- `C cx1 cy1, cx2 cy2, x2 y2` — cubic Bezier curve through two control points to the endpoint
- `stroke-dasharray="1200"` should be ≥ the path length (~1100 for this curve). Set both `dasharray` and `dashoffset` to the same value to start hidden, animate offset to 0 to draw.
- Arrowhead `<polygon>` points: form a triangle pointing at `(x2, y2)`. Use 3 vertices — tip + two base corners. Adjust `transformOrigin` in the GSAP scale-in to match the tip coordinates.

**Don'ts:**
- Don't draw an arrow across the speaker's face. Route the curve through clear zones (sky, desk, side margins).
- Don't make the arrow longer than ~1200px on screen — visual noise. If you need to point from one side to the other, use two shorter arrows.
- Don't use `infinite` repeat on the dash animation — breaks the capture engine.

---

## Pattern 6 — Big-stat hero card with LIVE indicator (variant of Pattern 3)

**Use for:** a single big stat that needs to feel **live / computed in real time** (e.g. an ROI projection). Same Pattern 3 white card, but with a pulsing mint dot in the header to communicate "this is live data." Built in `nadim-ad-2` for the +28% / AED 1.9M ROI hero moment.

```html
<div id="roi-card" class="clip" data-start="31.6" data-duration="4.2" data-track-index="8" data-layout-allow-overflow>
  <div class="header"><span class="live-dot"></span>For Investors · Live ROI</div>
  <div class="num">
    <span class="sign">+</span><span id="roi-num">0</span>%
  </div>
  <div class="label">
    ROI projected
    <span class="sub">on a sample RAK plot — <strong>AED 1.9M</strong></span>
  </div>
</div>
```

The only addition over Pattern 3 is the **live-dot** in the header:

```css
#roi-card .header {
  display: flex; align-items: center; gap: 14px;
  /* ... same as Pattern 3 header */
}
#roi-card .header .live-dot {
  width: 14px; height: 14px;
  border-radius: 50%;
  background: #00B073; /* accent green */
}
```

**Note:** no infinite CSS pulse animation — HyperFrames forbids `infinite` keyframes (breaks deterministic capture). The static dot reads as "live" because of the colored fill + the "LIVE" word in the header label. If you want a single pulse, animate it via GSAP with a finite `repeat` count calculated from the clip duration.

GSAP entry tweens are identical to Pattern 3.

---

## Pattern 7 — JV alignment chips with connectors

**Use for:** showing a chain of 3–4 named roles being aligned (joint venture parties, workflow steps, supply chain). Each role is a chip; arrows draw between them; an italic caption underneath emphasizes the outcome ("Aligned. *One room.*"). Built in `nadim-ad-2` for the LANDOWNER → INVESTOR → DEVELOPER → OPERATOR moment.

```html
<div
  id="jv-row"
  class="clip"
  data-start="36.2"
  data-duration="6.3"
  data-track-index="9"
  data-layout-allow-overflow
>
  <div class="jv-chip" id="jv-1">Landowner</div>
  <div class="jv-arrow" id="jv-a1">→</div>
  <div class="jv-chip" id="jv-2">Investor</div>
  <div class="jv-arrow" id="jv-a2">→</div>
  <div class="jv-chip" id="jv-3">Developer</div>
  <div class="jv-arrow" id="jv-a3">→</div>
  <div class="jv-chip" id="jv-4">Operator</div>
</div>

<div id="jv-caption" class="clip" data-start="39.5" data-duration="3.0" data-track-index="10">
  Aligned. <span>One room.</span>
</div>
```

```css
#jv-row {
  position: absolute;
  left: 60px; right: 60px; bottom: 280px;
  z-index: 8;
  display: flex; align-items: center; justify-content: space-between;
  gap: 12px;
}
#jv-row .jv-chip {
  flex: 0 0 210px;
  height: 96px;
  background: #003D2E;
  border: 2px solid #3FE6A8;
  border-radius: 16px;
  display: flex; align-items: center; justify-content: center;
  font-family: "DM Sans", sans-serif;
  font-weight: 700;
  font-size: 22px;
  letter-spacing: 0.06em;
  text-transform: uppercase;
  color: #3FE6A8;
  box-shadow: 0 12px 32px rgba(0, 61, 46, 0.45);
}
#jv-row .jv-arrow {
  flex: 0 0 28px;
  font-size: 36px;
  color: #3FE6A8;
  text-align: center;
  opacity: 0; /* GSAP fades these in between chip entries */
}
#jv-caption {
  position: absolute;
  left: 60px; right: 60px; bottom: 160px;
  z-index: 8;
  text-align: center;
  font-family: "Instrument Serif", serif;
  font-style: italic;
  font-size: 54px;
  color: #FDFDFD;
  text-shadow: 0 4px 24px rgba(0, 0, 0, 0.6);
}
#jv-caption span { color: #3FE6A8; }
```

```js
// Chips stagger up, arrows fade in between them (alternating)
tl.from("#jv-1", { y: 80, opacity: 0, duration: 0.45, ease: "back.out(1.5)" }, dataStart + 0.1);
tl.to("#jv-a1", { opacity: 1, duration: 0.3, ease: "power2.out" }, dataStart + 0.4);
tl.from("#jv-2", { y: 80, opacity: 0, duration: 0.45, ease: "back.out(1.5)" }, dataStart + 0.65);
tl.to("#jv-a2", { opacity: 1, duration: 0.3, ease: "power2.out" }, dataStart + 0.95);
tl.from("#jv-3", { y: 80, opacity: 0, duration: 0.45, ease: "back.out(1.5)" }, dataStart + 1.2);
tl.to("#jv-a3", { opacity: 1, duration: 0.3, ease: "power2.out" }, dataStart + 1.5);
tl.from("#jv-4", { y: 80, opacity: 0, duration: 0.45, ease: "back.out(1.5)" }, dataStart + 1.75);

// Caption lands after all 4 chips
tl.from("#jv-caption", { y: 30, opacity: 0, duration: 0.55, ease: "power3.out" }, dataStart + 3.4);

// Exit: chips slide right, caption fades — horizontal out per motion rule
tl.to(["#jv-1", "#jv-2", "#jv-3", "#jv-4", "#jv-a1", "#jv-a2", "#jv-a3"],
  { x: 1200, opacity: 0, duration: 0.55, ease: "power2.in" },
  dataStart + dataDuration - 0.6,
);
tl.to("#jv-caption", { opacity: 0, duration: 0.4, ease: "power2.in" }, dataStart + dataDuration - 0.5);
```

**Layout math:** four 210px chips + three 28px arrows + small flex gaps = 960px (fits the 60px-padded frame). For 5 chips, drop chip width to ~170px. For 3 chips, widen to ~280px.

---

## Pattern 8 — Top-corner audience badge

**Use for:** addressing a specific audience segment ("FOR BROKERS", "FOR INVESTORS") that's named in the script. Compact badge top-right corner. Can be sequenced (one badge slides out, the next slides in) to handle dual-audience opening lines. Built in `nadim-ad-2`.

```html
<div id="badge-brokers" class="clip aud-badge" data-start="1.0" data-duration="3.5" data-track-index="2" data-layout-allow-overflow>
  <div class="small">For</div>
  <div class="big">Brokers</div>
</div>
<div id="badge-investors" class="clip aud-badge" data-start="4.8" data-duration="5.2" data-track-index="3" data-layout-allow-overflow>
  <div class="small">For</div>
  <div class="big">Investors</div>
</div>
```

```css
.aud-badge {
  position: absolute;
  top: 80px; right: 60px;
  width: 400px; height: 130px;
  z-index: 10;
  background: #003D2E;
  border-radius: 18px;
  border-left: 5px solid #3FE6A8;
  padding: 22px 28px;
  box-shadow: 0 18px 44px rgba(0, 0, 0, 0.45);
  display: flex; flex-direction: column; justify-content: center;
}
.aud-badge .small {
  font-family: "DM Sans", sans-serif;
  font-weight: 500;
  font-size: 22px;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: #3FE6A8;
}
.aud-badge .big {
  font-family: "DM Sans", sans-serif;
  font-weight: 700;
  font-size: 60px;
  letter-spacing: 0.02em;
  color: #FDFDFD;
  margin-top: 4px;
}
```

```js
// Slide DOWN in from above, slide RIGHT out (vertical-IN, horizontal-OUT motion rule)
tl.from("#badge-brokers", { y: -260, opacity: 0, duration: 0.55, ease: "power3.out" }, 1.1);
tl.from("#badge-brokers .small", { y: 14, opacity: 0, duration: 0.4, ease: "power2.out" }, 1.35);
tl.from("#badge-brokers .big", { y: 22, opacity: 0, duration: 0.5, ease: "power2.out" }, 1.45);
tl.to("#badge-brokers", { x: 540, opacity: 0, duration: 0.45, ease: "power2.in" }, 3.95);
// (Repeat the same pattern for #badge-investors with offset start time)
```

**For sequential audience badges:** make sure the first badge's exit completes BEFORE the next badge's entry begins (don't overlap them at the same position). Use different `data-track-index` values so they're treated as independent clips.

---

## Pattern 9 — Feature cascade card

**Use for:** a dense list of features mentioned rapid-fire in the script ("we cover X, Y, Z, A, B, C, ..."). Each feature is a small mint pill that pops into a forest-green container, ending with a bold "ALL IN ONE CALL"–style emphasis line. Built in `nadim-ad-2` for the 8-feature platform rundown.

```html
<div id="feature-card" class="clip" data-start="15.6" data-duration="9.9" data-track-index="6" data-layout-allow-overflow>
  <div class="header">What we cover — one call</div>
  <div class="chips">
    <div class="chip" id="ch-1">Plots</div>
    <div class="chip" id="ch-2">Location</div>
    <!-- … up to ~8 chips -->
  </div>
  <div class="all-in-one" id="ch-all">All In One Call<em>.</em></div>
</div>
```

```css
#feature-card {
  position: absolute;
  left: 60px; right: 60px; bottom: 80px;
  z-index: 8;
  background: #003D2E;
  border-radius: 22px;
  padding: 32px 36px;
  box-shadow: 0 18px 50px rgba(0, 0, 0, 0.40);
}
#feature-card .header {
  font-family: "DM Sans", sans-serif;
  font-weight: 500;
  font-size: 22px;
  letter-spacing: 0.14em;
  text-transform: uppercase;
  color: #3FE6A8;
  margin-bottom: 20px;
}
#feature-card .chips { display: flex; flex-wrap: wrap; gap: 12px; }
#feature-card .chip {
  padding: 12px 22px;
  border: 1.5px solid #3FE6A8;
  border-radius: 999px;
  font-family: "DM Sans", sans-serif;
  font-weight: 600;
  font-size: 26px;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  color: #3FE6A8;
  background: rgba(63, 230, 168, 0.06);
  white-space: nowrap;
}
#feature-card .all-in-one {
  margin-top: 26px;
  padding-top: 24px;
  padding-bottom: 8px;
  border-top: 1px solid rgba(255, 255, 255, 0.12);
  font-family: "DM Sans", sans-serif;
  font-weight: 700;
  font-size: 42px;
  line-height: 1.2;
  color: #FDFDFD;
  text-align: center;
  letter-spacing: 0.04em;
  text-transform: uppercase;
}
#feature-card .all-in-one em {
  font-family: "Instrument Serif", serif;
  font-style: italic;
  font-weight: 400;
  text-transform: none;
  color: #3FE6A8;
  margin-left: 4px;
}
```

```js
// Card slides up, header fades in, chips cascade in via stagger
tl.from("#feature-card", { y: 120, opacity: 0, duration: 0.55, ease: "power3.out" }, dataStart + 0.1);
tl.from("#feature-card .header", { y: 18, opacity: 0, duration: 0.4, ease: "power2.out" }, dataStart + 0.35);

// Stagger 0.95s per chip works for 8 chips in ~9s. Tune per script density.
tl.from(
  ["#ch-1", "#ch-2", "#ch-3", "#ch-4", "#ch-5", "#ch-6", "#ch-7", "#ch-8"],
  { scale: 0.7, opacity: 0, duration: 0.35, ease: "back.out(1.5)", stagger: 0.95 },
  dataStart + 0.5,
);
// "ALL IN ONE CALL" emphasis lands after the last chip
tl.from("#ch-all", { y: 18, opacity: 0, duration: 0.55, ease: "power3.out" }, dataStart + 8.4);
// Exit
tl.to("#feature-card", { x: -1140, opacity: 0, duration: 0.5, ease: "power2.in" }, dataStart + dataDuration - 0.65);
```

**Density rule:** 6–8 chips is the sweet spot. Fewer than 6 feels sparse; more than 8 makes the card too tall and the cascade too rushed. Calculate stagger spacing as `(script_segment_duration - 1.5) / chip_count`.

---

## Pattern 10 — Outcome ticker (white card with checkmark rows)

**Use for:** listing 3 outcomes/benefits with the same audience header ("For Brokers — stronger presentation, faster decisions, protected commission"). White editorial card with mint circle checkmarks. Built in `nadim-ad-2` for the broker outcome moment.

```html
<div id="broker-card" class="clip outcome-card" data-start="25.7" data-duration="5.7" data-track-index="7" data-layout-allow-overflow>
  <div class="header">For Brokers</div>
  <div class="row" id="br-1"><div class="check">✓</div>Stronger Client Presentation</div>
  <div class="row" id="br-2"><div class="check">✓</div>Faster Decisions</div>
  <div class="row" id="br-3"><div class="check">✓</div>Protected Commission</div>
</div>
```

```css
.outcome-card {
  position: absolute;
  left: 60px; right: 60px; bottom: 80px;
  z-index: 8;
  background: #FDFDFD;
  color: #121212;
  border-radius: 22px;
  padding: 32px 36px;
  box-shadow: 0 18px 50px rgba(0, 0, 0, 0.18);
}
.outcome-card .header {
  font-family: "DM Sans", sans-serif;
  font-weight: 600;
  font-size: 24px;
  letter-spacing: 0.14em;
  text-transform: uppercase;
  color: #00B073;
  margin-bottom: 22px;
}
.outcome-card .row {
  display: flex; align-items: center; gap: 22px;
  padding: 12px 0;
  font-family: "DM Sans", sans-serif;
  font-weight: 500;
  font-size: 30px;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  color: #121212;
}
.outcome-card .row .check {
  flex: 0 0 48px; width: 48px; height: 48px;
  border-radius: 50%;
  background: #00B073;
  color: #FDFDFD;
  display: flex; align-items: center; justify-content: center;
  font-size: 26px; font-weight: 700;
}
```

```js
// Card lifts up, header lands, rows tick in with check icons popping
tl.from("#broker-card", { y: 120, opacity: 0, duration: 0.55, ease: "power3.out" }, dataStart + 0.1);
tl.from("#broker-card .header", { y: 16, opacity: 0, duration: 0.4, ease: "power2.out" }, dataStart + 0.3);
tl.from(
  ["#br-1", "#br-2", "#br-3"],
  { x: -40, opacity: 0, duration: 0.45, ease: "power2.out", stagger: 1.45 },
  dataStart + 0.5,
);
tl.from(
  ["#br-1 .check", "#br-2 .check", "#br-3 .check"],
  { scale: 0.3, opacity: 0, duration: 0.35, ease: "back.out(2)", stagger: 1.45 },
  dataStart + 0.55,
);
// Exit
tl.to("#broker-card", { x: 1140, opacity: 0, duration: 0.5, ease: "power2.in" }, dataStart + dataDuration - 0.6);
```

**Stagger rule:** 1.45s per row works for a ~5.7s clip with 3 rows (last row ticks in around 3.5s, leaves ~2s for the eye to land before exit). For 4 rows in the same window, tighten stagger to ~1.1s.

---

## Meta safe zones — where overlays can live

Paid social (Meta Reels / Stories / FB Reels) overlays its own UI on the video. Boxes placed in the bottom third get **covered by Meta's caption, username, CTA button, and action rail**. Boxes too close to the top get clipped by the top chrome. Plan every overlay around these zones (1080×1920 frame):

| Zone | Y range | Use for |
|---|---|---|
| **Top danger** | 0 – ~220px | Avoid — Meta's top chrome |
| **Top strip** | ~220 – speaker's hairline | Small badges only (Pattern 8, platform labels). Compact, single-purpose. |
| **Speaker's face** | hairline – chin | **Never cover.** This is the talking head. |
| **Mid-zone** | chin – ~1250px | **The workhorse.** All bigger cards (feature cascade, outcome ticker, stat hero, JV row) go here — over the speaker's chest/torso, below the face, above Meta's UI. |
| **Bottom danger** | ~1250 – 1920px | Avoid for content boxes — Meta's caption + CTA + action rail. |

**Placement rules:**
- Anchor mid-zone cards with `top:` (not `bottom:`), centered around the middle of the chin-to-1250 band. For `nadim-ad-2` (chin ≈ y=836) the cards sit at `top: 860–950`.
- Anchor top-strip badges at `top: ~230–250` — clears Meta's top chrome, sits above the speaker's head.
- Cards have solid backgrounds, so overlapping the speaker's torso or a laptop/prop is fine — only transparent text (italic captions) needs a clean background behind it. Position transparent text over the speaker's clothing, not over a busy prop.
- **Exception — the CTA closer (Pattern 4)** may stay anchored to the bottom. Its forest gradient overlaps Meta's UI zone, but Meta's own CTA button sits there too — they reinforce rather than fight. This is the one overlay allowed in the bottom danger zone.
- Verify with `renderSeek(t)` in the preview (not plain `seek(t)` — plain seek doesn't always update clip visibility). Screenshot each beat and confirm no box covers the face and no box sits in Meta's bottom third.

## Rules of thumb

- **Don't crowd.** Three overlay moments in a 30-second ad is the right density. Five is too many.
- **No timeline overlaps in the same lane.** Same `data-track-index` clips cannot share time — use different indices.
- **Anchor each pattern to a spoken line.** Patterns 1+2 typically pair together. Pattern 3 stands alone. Pattern 4 is always the closer.
- **Count-ups must be deterministic.** Always use GSAP `textContent` tween with `snap: { textContent: 1 }`. Never `Math.random` or `Date.now`.
- **Validate before render.** Run `npx hyperframes lint && npx hyperframes validate && npx hyperframes inspect` after every change. Contrast warnings on clipped (out-of-window) elements are known false positives.
- **Preview frame check.** Spin up `npx hyperframes preview --port 3003`, seek to each pattern's hero frame via `window.__player.seek(t)`, screenshot. Confirm no face overlap and brand consistency before render.
- **One pattern, one purpose.** A scene either highlights an image (Pattern 1) OR a stat (2 or 3) OR closes (4). Don't stack a stat card under the CTA shade.

## Motion rule — vertical IN, horizontal OUT

Every overlay (except Pattern 4, the closer) **must have both an entrance and an exit animation**. Letting a clip just snap to hidden at the end of its window looks unprofessional. Pattern:

- **Entrance:** slide in **vertically** — top→down or bottom→up depending on the overlay's anchor point. Use `gsap.from()` with `y` offset and `ease: "power3.out"` over 0.6–0.7s.
- **Exit:** slide out **horizontally** — left or right, off-screen. Use `gsap.to()` with `x` offset and `ease: "power2.in"` over 0.5s, timed to the **last 0.6s** of the clip's window (so `data-start + data-duration - 0.6`).

Direction recipes that work:
- Top-corner overlays (Pattern 1, Wynn card): enter from **top** (`y: -540`), exit to **right** (`x: 500`).
- Lower-third overlays (Patterns 2, 3): enter from **bottom** (`y: 80–100`), exit to **left or right** (`x: ±1100`). Alternate the exit direction for variety when stacking patterns in sequence.
- Pattern 4 (CTA): no exit — it holds until the video ends.

The HyperFrames framework still snaps the clip to `visibility: hidden` at the end of its `data-duration` window, but if your exit tween has already moved the element off-screen and faded it to opacity 0, the snap is invisible. Make sure the exit completes BEFORE `data-start + data-duration`.

**Example exit tween (paste at the end of each non-CTA pattern's GSAP block):**

```js
// Slide out to the right, 0.5s before the clip ends
tl.to(
  "#your-overlay-id",
  { x: 1100, opacity: 0, duration: 0.5, ease: "power2.in" },
  dataStart + dataDuration - 0.6,
);
```

Use `x: -1100` to exit left instead. The exact value just needs to be enough to push the element fully past the 1080px frame edge.

---

## Pattern 11 — Rank badge (countdown)

**Use for:** the "#N" marker in a ranked countdown ("top 3 areas", "5 best plots"). A bold rounded-square badge with a big number + small "RANK" label. Anchor it **top-left** (`top: 250px; left: 60px`) so it balances a Pattern-12 area card on the top-right, both in the clear top-strip above the speaker. Built in `rak-areas-ranked` (RAK areas countdown).

**Climax variant (`.top`):** for the **#1** beat, invert the colors — mint fill with forest number — so the winner reads brighter/hotter than the lower ranks. Lower ranks stay forest-fill + mint number.

```html
<div id="rank-3" class="clip rank-badge" data-start="10.6" data-duration="9.6" data-track-index="3" data-layout-allow-overflow>
  <div class="n">3</div>
  <div class="lab">Rank</div>
</div>
<!-- #1 climax — add .top for the inverted look -->
<div id="rank-1" class="clip rank-badge top" data-start="29.1" data-duration="11.4" data-track-index="7" data-layout-allow-overflow>
  <div class="n">1</div>
  <div class="lab">Rank</div>
</div>
```

```css
.rank-badge {
  position: absolute;
  top: 250px; left: 60px;
  width: 158px; height: 158px;
  z-index: 6;
  background: #003D2E;            /* --bg-deep */
  border: 2px solid #3FE6A8;      /* --mint */
  border-radius: 26px;
  box-shadow: 0 16px 40px rgba(0, 61, 46, 0.5);
  display: flex; flex-direction: column;
  align-items: center; justify-content: center;
}
.rank-badge .n {
  font-family: "DM Sans", sans-serif;
  font-weight: 700; font-size: 104px; line-height: 0.8;
  color: #3FE6A8;                 /* --mint */
  font-variant-numeric: tabular-nums;
}
.rank-badge .lab {
  font-family: "DM Sans", sans-serif;
  font-weight: 600; font-size: 18px;
  letter-spacing: 0.22em; text-transform: uppercase;
  color: #3FE6A8; margin-top: 6px;
}
/* #1 inverted — mint fill, forest number */
.rank-badge.top { background: #3FE6A8; border-color: #3FE6A8; box-shadow: 0 16px 44px rgba(63, 230, 168, 0.55); }
.rank-badge.top .lab,
.rank-badge.top .n { color: #003D2E; }
```

```js
// Drop in from top (vertical IN), number pops, exit left (horizontal OUT)
tl.from("#rank-3", { y: -280, opacity: 0, duration: 0.55, ease: "power3.out" }, dataStart + 0.1);
tl.from("#rank-3 .n", { scale: 0.4, opacity: 0, duration: 0.5, ease: "back.out(2)" }, dataStart + 0.35);
tl.to("#rank-3", { x: -560, opacity: 0, duration: 0.45, ease: "power2.in" }, dataStart + dataDuration - 0.55);
```

**Note:** the badge and its paired area card share the same `data-start`/`data-duration` (they enter and leave together), but live on **different `data-track-index` values** so they're independent clips. Inverted-`.top` small "RANK" label is forest-on-mint (~3.8:1) — borderline for the contrast checker but fine in practice (the big number carries it); the validator only flags it at out-of-window sample timestamps.

---

## Pattern 12 — Ranked area card (image + name + tag)

**Use for:** the area/option image in a countdown — a Pattern-1 image inset reused with a baked-in name + descriptor, anchored **top-right** to pair with a Pattern-11 rank badge top-left. Same **320×426** size as the Pattern-1 Wynn card (keeps the kit consistent; crop source photos to that 3:4 ratio). Built in `rak-areas-ranked` for the Al Nakheel / Al Mai'reed / RAK Beach District cards.

**Important — do NOT use `overflow: hidden` on the card.** Round the `<img>` and the `::after` gradient directly instead. With `overflow: hidden`, the layout inspector flags the bottom tag text as `clipped_text` (it treats the card box as the text's clipping box) — a hard error. Rounding the children instead clears it and looks identical.

```html
<div id="area-1" class="clip area-card" data-start="29.1" data-duration="11.4" data-track-index="8" data-layout-allow-overflow>
  <img src="rakbd.png" alt="RAK Beach District" crossorigin="anonymous" />
  <div class="area-tag">
    <span class="name">RAK Beach District</span>
    <span class="desc">Wynn-facing · $5B resort</span>
  </div>
</div>
```

```css
.area-card {
  position: absolute;
  top: 250px; right: 40px;
  width: 320px; height: 426px;
  z-index: 5;
  border-radius: 20px;
  background: #000;
  box-shadow: 0 18px 44px rgba(0, 0, 0, 0.45);
  /* NO overflow: hidden — see note above */
}
.area-card img { width: 100%; height: 100%; object-fit: cover; border-radius: 20px; }
.area-card::after {
  content: "";
  position: absolute; left: 0; right: 0; bottom: 0; height: 60%;
  background: linear-gradient(to top, rgba(0,61,46,0.94) 0%, rgba(0,61,46,0.68) 45%, rgba(0,61,46,0) 100%);
  border-radius: 0 0 20px 20px;
  pointer-events: none;
}
.area-card .area-tag { position: absolute; left: 18px; right: 18px; bottom: 16px; z-index: 2; }
.area-card .area-tag .name {
  display: block;
  font-family: "Instrument Serif", serif; font-style: italic;
  font-size: 34px; line-height: 0.96; color: #3FE6A8;   /* --mint */
}
.area-card .area-tag .desc {
  display: block;
  font-family: "DM Sans", sans-serif; font-weight: 500;
  font-size: 14px; letter-spacing: 0.04em; text-transform: uppercase;
  color: #FDFDFD; margin-top: 6px;
}
```

```js
// Drop in from top (vertical IN), tag rises, exit right (horizontal OUT)
tl.from("#area-1", { y: -540, opacity: 0, duration: 0.7, ease: "power3.out" }, dataStart + 0.15);
tl.from("#area-1 .area-tag", { y: 24, opacity: 0, duration: 0.5, ease: "power2.out" }, dataStart + 0.55);
tl.to("#area-1", { x: 560, opacity: 0, duration: 0.5, ease: "power2.in" }, dataStart + dataDuration - 0.6);
```

**Type note:** keep `.name` at ~34px so the longest names ("RAK Beach District") wrap to a clean two lines inside 320px without crowding the descriptor. Short names ("Al Nakheel") sit on one line.

---

## Pattern 13 — Green pin drop + plot polygon (map call-out)

**Use for:** marking a specific land plot on an aerial/satellite shot — a forest-green location pin drops onto the parcel and a mint polygon traces + fills the plot boundary at low opacity so the building/ground still shows through. Built in `labor-camp-fujairah` for the satellite zoom-in landing.

**Anatomy:** one full-frame `<svg>` (1080×1920, `pointer-events:none`) holding a `<polygon>` (the plot) + a `<g>` pin (teardrop path + dot). Polygon coords are picked off the *landing* frame (grab it with ffmpeg and read the parcel corners). Best used on the near-static end of a zoom — it does NOT perfectly track a moving push-in.

```html
<svg id="plot-svg" class="clip" viewBox="0 0 1080 1920" data-start="5.4" data-duration="2.3" data-track-index="5" data-layout-allow-overflow>
  <polygon id="plot-poly" points="108,576 799,461 896,1248 86,1267" />
  <g id="pin-wrap">
    <g transform="translate(505 905)">
      <path class="pin-body" d="M0 0 C -26 -40 -42 -66 -42 -92 A 42 42 0 1 1 42 -92 C 42 -66 26 -40 0 0 Z" />
      <circle class="pin-dot" cx="0" cy="-92" r="15" />
    </g>
  </g>
</svg>
```

```css
#plot-svg { position: absolute; inset: 0; width: 1080px; height: 1920px; z-index: 6; pointer-events: none; }
#plot-poly {
  fill: #3FE6A8; fill-opacity: 0.22;            /* mint, low opacity so the plot shows through */
  stroke: #3FE6A8; stroke-width: 4; stroke-linejoin: round;
  stroke-dasharray: 3000; stroke-dashoffset: 3000;   /* dasharray ≥ polygon perimeter; draw-on */
  filter: drop-shadow(0 0 14px rgba(0, 176, 115, 0.35));
}
.pin-body { fill: #00B073; stroke: #FDFDFD; stroke-width: 5; filter: drop-shadow(0 8px 18px rgba(0,0,0,0.45)); }
.pin-dot { fill: #FDFDFD; }
```

```js
// GOTCHA: the clip system does NOT suppress an SVG's fill before its window — gate the whole SVG opacity yourself.
tl.set("#plot-svg", { opacity: 0 }, 0);
tl.to("#plot-svg", { opacity: 1, duration: 0.25, ease: "power1.out" }, 5.45);
tl.from("#pin-wrap", { y: -360, opacity: 0, duration: 0.7, ease: "back.out(1.4)" }, 5.6);     // pin drops
tl.to("#plot-poly", { strokeDashoffset: 0, duration: 0.9, ease: "power2.inOut" }, 5.75);       // outline draws
tl.fromTo("#plot-poly", { attr: { "fill-opacity": 0 } }, { attr: { "fill-opacity": 0.22 }, duration: 0.7, ease: "power2.out" }, 5.95);
tl.to("#plot-svg", { opacity: 0, duration: 0.35, ease: "power2.in" }, 7.3);                    // fade out (exit)
```

**Notes:**
- `stroke-dasharray` must be ≥ the polygon perimeter (sum the side lengths). 3000 covers a ~3000px parcel quad.
- The pin's `<g transform="translate(x y)">` sets the tip at `(x, y)` — aim the tip at the building/plot centre on the landing frame.
- This pattern is the exception to "trust the clip system for visibility" — always add the `tl.set(..., {opacity:0}, 0)` gate or the fill leaks in from t=0.

---

## Future components (not yet built)

When new patterns emerge, document them here with the same structure (use case, HTML, CSS, GSAP entry).

**Built (back-ported from `nadim-ad-2`, 2026-05-18):**
- ✅ Pattern 5 — Arrow callout (SVG draw-on)
- ✅ Pattern 6 — Live-stat hero (Pattern 3 variant with LIVE indicator)
- ✅ Pattern 7 — JV alignment chips with connectors
- ✅ Pattern 8 — Top-corner audience badge
- ✅ Pattern 9 — Feature cascade card
- ✅ Pattern 10 — Outcome ticker (white card + checkmark rows)

**Built (back-ported from `rak-areas-ranked`, 2026-05-29):**
- ✅ Pattern 11 — Rank badge (countdown; `.top` inverted variant for #1)
- ✅ Pattern 12 — Ranked area card (image inset + name/tag; no `overflow:hidden`)

**Built (back-ported from `labor-camp-fujairah`, 2026-06-02):**
- ✅ Pattern 13 — Green pin drop + plot polygon (full-frame map call-out on an aerial/satellite shot)

**Still to build:**
- Before/after slider (mid-frame split with horizontal reveal)
- Pricing table (multi-row data card with hover-style emphasis)
- Animated underline / marker sweep on a single word (Pattern 4 enhancement)
