# Scroll-Driven Landing — Patterns

Stack-agnostic markup, CSS, and logic. Implement animations with the project's own library.

## Loader — mask structure

Each line sits in an overflow-hidden mask so `translateY(100%) → 0` reveals upward.

```html
<div class="loader" aria-busy="true">
  <div class="loader__inner">
    <div class="loader__line-mask">
      <span class="loader__line">We craft</span>
    </div>
    <div class="loader__line-mask">
      <span class="loader__line">digital experiences</span>
    </div>
    <!-- 5–7 lines total -->
  </div>
</div>
```

```css
.loader {
  position: fixed;
  inset: 0;
  z-index: 9999;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--bg);
}
.loader__line-mask { overflow: hidden; line-height: 0.9; }
.loader__line {
  display: block;
  font-family: var(--font-display);
  font-size: clamp(2.5rem, 8vw, 6rem);
  text-transform: uppercase;
}
```

Animate `.loader__line` with project's tween API; exit whole `.loader` with `translateY(-100%)` at 1.9s.

## Hero — scroll pin + zoom (pseudocode)

```
ON heroReady:
  ENTRANCE: animate title words, image, decor (see reference.md)

ON scroll WHILE hero pinned:
  progress = scrollDistance / (viewportHeight * 1.2)   // 0 → 1
  image.scale       = lerp(1, 2.4, progress)           // 1.8 on mobile
  image.translateY  = lerp(0, -10vh, progress)
  image.radius      = lerp(8px, 0, progress)
  title.opacity     = lerp(1, 0, ease(progress, start: 0.3))
  title.translateY  = lerp(0, -60px, ease(progress, start: 0.3))
```

Pin = hero section fixed for `120vh` of scroll, then release.

## Word stagger — split text

```html
<p class="statement__text">
  <span class="statement__word">We&nbsp;</span>
  <span class="statement__word">believe&nbsp;</span>
  <!-- one span per word -->
</p>
```

```
WHEN section.top crosses 75% viewport (once):
  FOR EACH .statement__word with stagger 0.03s:
    from { opacity: 0, translateY(30px) }
    to   { opacity: 1, translateY(0) }
```

Works the same for About copy. Split on spaces; preserve `&nbsp;` between words.

## Service block — alternating grid

```html
<section class="service-section service-section--left">
  <span class="decor" aria-hidden="true">)</span>
  <div class="service-section__grid">
    <div class="service-section__media">
      <img src="…" alt="…" />
      <span class="service-section__click">
        <span>Click me</span>
        <span class="service-section__click-arrow">→</span>
      </span>
    </div>
    <div class="service-section__content">
      <span class="service-section__index">( 01 )</span>
      <h2 class="service-section__title">Beauty</h2>
      <p class="service-section__body">…</p>
    </div>
  </div>
</section>
```

```css
.service-section__grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: clamp(2rem, 5vw, 5rem);
  align-items: center;
}
.service-section--right .service-section__grid { direction: rtl; }
.service-section--right .service-section__grid > * { direction: ltr; }

@media (max-width: 1023px) {
  .service-section__grid { grid-template-columns: 1fr; }
  .service-section--right .service-section__grid { direction: ltr; }
}
```

```css
.service-section__media:hover .service-section__click { color: var(--accent); }
.service-section__media:hover .service-section__click-arrow {
  transform: translateX(8px);
}
```

## Asset preload (logic)

```
criticalAssets = [heroImage, …serviceImages]

ON page init:
  Promise.all(criticalAssets.map(preload)).then(set assetsReady).catch(set assetsReady)

function preload(src):
  return new Promise((resolve, reject) => {
    image = new Image()
    image.onload = resolve
    image.onerror = reject
    image.src = src
  })
```

Adapt to framework asset URLs (import paths, CMS URLs, `useAsset`, etc.).

## Decor parallax (logic)

```
FOR EACH decor element IN section:
  ON section scroll (scrub):
    decor.translateY = map(scrollProgress, -5%, +5%)
    decor.translateX = alternating slight shift per index
```

Attach one listener/trigger per section containing decor elements.

## Footer index

```html
<nav class="footer__nav" aria-label="Site index">
  <a href="#hero" class="footer__item">
    <span class="footer__index">( 01 )</span>
    <span class="footer__label">Hero</span>
  </a>
  <!-- ( 02 ) … ( 05 ) -->
</nav>
```

```css
.footer__index {
  font-family: var(--font-mono);
  font-size: 0.8rem;
  opacity: 0.4;
}
```

## Resize refresh (logic)

```
debounce 150ms on window resize:
  refresh all scroll triggers / remeasure pinned sections
  refresh smooth-scroll instance IF project uses one
```

## Reduced motion

```css
@media (prefers-reduced-motion: reduce) {
  .loader { display: none; } /* or instant */
  /* disable scroll-scrub classes; show final states */
}
```

Skip pin, scrub, and parallax in animation init when `prefers-reduced-motion: reduce` matches.

## Mapping examples (do not treat as defaults)

| Project already has | Implement hero pin+zoom with |
|---------------------|----------------------------|
| GSAP + ScrollTrigger | `pin: true`, `scrub: 1`, timeline |
| Framer Motion | `useScroll` + `useTransform` on progress |
| CSS scroll-driven | `@scroll-timeline` / `animation-timeline: scroll()` |
| Vue + @vueuse/motion | `v-motion` scroll variants or GSAP if already in repo |
| Plain HTML | `position: sticky` + scroll listener updating CSS vars |

Pick the row that matches the repo — never add a library just because it appears in this table.
