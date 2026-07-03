---
name: scroll-driven-landing
description: >-
  Scroll-driven animated landing page style — Loader text reveal, pinned hero
  image zoom, word-stagger sections, alternating service blocks, parallax decor,
  smooth scroll. Stack-agnostic: adapt motion and layout to the user's existing
  project (React, Vue, Svelte, Next, Astro, vanilla, etc.). Use for agency,
  portfolio, or brand marketing sites; warm minimal typography; or when the user
  mentions scroll pin, hero zoom on scroll, loader reveal, or premium landing
  motion.
license: MIT
---

# Scroll-Driven Landing Pages

Universal **motion + layout + visual grammar** for premium marketing landings. **Adcker.com** is the reference look; tokens are swappable per brand.

Reference: [reference.md](reference.md) · Patterns: [examples.md](examples.md)

**This skill does not prescribe a tech stack.** Read the user's project first, then implement the same motion specs with whatever they already use.

## Two layers (keep separate)

| Layer | Universal (keep) | Brand-specific (swap) |
|-------|------------------|-------------------------|
| Motion | Loader → Hero gate, pin+zoom, scrub reveals, parallax | — |
| Structure | Loader / Hero / Statement / Services / About / Footer | Section count, copy |
| Visual | Oversized display type, generous whitespace | Colors, fonts, decor symbol |
| Implementation | Match project's framework, animation lib, CSS approach | — |

Default visual variant = **Adcker**: warm `#F5F2EC` bg, Anton + Inter + Space Mono, `)` decor at opacity 0.12. Replace tokens for other brands; keep motion specs.

## Step 0 — Adapt to the existing project

Before writing code:

1. **Detect stack** — framework (React/Vue/Svelte/Next/Astro/HTML), animation library (GSAP, Framer Motion, CSS scroll-driven, anime.js, none), CSS (Tailwind, CSS modules, global tokens), smooth-scroll lib if any.
2. **Follow project conventions** — file layout, naming, i18n, routing, asset imports.
3. **Map capabilities** — use the project's tools; do not introduce a new stack unless the user asks or the project is empty.

| Capability needed | If project has… | Otherwise… |
|-------------------|-----------------|------------|
| Scroll-scrub timeline | GSAP ScrollTrigger, Motion scroll(), CSS `animation-timeline: scroll()` | Native scroll + `transform` on `scroll` event (last resort) |
| Pin section while scrolling | ScrollTrigger pin, Motion `useScroll` + fixed positioning | `position: sticky` + scroll-linked transforms |
| Smooth scroll | Lenis, Locomotive, project smooth-scroll | Native scroll (still valid) |
| Time-based loader | Any tween lib or CSS `@keyframes` | CSS transitions |
| Component lifecycle cleanup | Framework hooks / `onDestroy` | Manual teardown on unmount |

**Empty repo?** Pick the stack the user names; if none, use whatever the repo template already implies. Do not default to a specific stack from this skill.

## Build order

1. Static layout + CSS tokens (no scroll animation)
2. Loader + loading state
3. Hero pin/zoom alone (scrub linked to scroll; see [reference.md](reference.md))
4. Loader complete + assets ready → hero entrance gate
5. Section scroll reveals
6. Smooth scroll (if project uses it) + layout refresh on resize (debounce 150ms)
7. Critical image preload during Loader
8. Decor parallax + hover micro-interactions
9. Mobile breakpoints + `prefers-reduced-motion: reduce`

**Never wire all animations before layout is stable.**

## Page flow

Loader (~2.4s) → Hero (title + image/video, scroll zoom) → Statement (word stagger) → Services (3+ alternating blocks) → About CTA → Footer index `(01)…(05)`

## State machine (logic, not framework)

Four gates — implement with project state (store, refs, class flags, etc.):

```
isLoading     → Loader visible
loaderDone    → Loader exit animation finished
assetsReady   → Critical images/fonts preloaded
heroReady     → loaderDone AND assetsReady → allow Hero entrance
```

Preload critical assets on init; on failure still set `assetsReady` so the page is not blocked. Defer hero entrance until `heroReady`.

## Motion specs (summary)

| Block | Key params |
|-------|------------|
| Loader | lines `y:100%→0`, stagger `0.12s`, exit at `1.9s` slide up |
| Hero | scroll span `120%` viewport, scrub ~1s lag (desktop), pin, scale `2.4`, title fade `y:-60` |
| Statement | word stagger `0.03s`, enter when section top hits `75%` viewport, once |
| Services | title/image/body stagger, parallax `y:±8%`, alt L/R grid |
| Footer | index stagger `0.1s`, label opacity `0.4` |

Full numbers, easing, breakpoints → [reference.md](reference.md)  
Markup/CSS patterns → [examples.md](examples.md)

## Animation rules (stack-agnostic)

- Prefer `transform` + `opacity` only (hero `border-radius` exception)
- Scroll-scrub hero: linear easing with scroll (`ease: none` equivalent)
- One-shot section reveals: fire once when trigger crosses viewport threshold
- On resize: debounce 150ms, recalculate scroll triggers / sticky layout
- **`prefers-reduced-motion: reduce`**: skip pin, scrub, parallax; shorten or skip Loader; keep content readable

## Brand customization checklist

When adapting to a new client, ask or infer:

1. **Loader lines** — 5–7 short phrases (brand story)
2. **Hero** — display title split into animatable words; image or video
3. **Services** — 2–4 blocks with title, image, paragraph, CTA
4. **Tokens** — background, ink, accent, display/body/mono fonts
5. **Decor** — optional symbol (Adcker uses `)`); parallax applies to decor layer
6. **Assets** — follow project asset conventions; preload list at page entry

## Anti-patterns

- Introducing GSAP/Lenis/React when the project uses something else
- Copying reference file paths into unrelated repos
- Animating before layout is stable · hero entrance before preload completes
- Inline layout styles that fight the project's CSS system
- CDN-only production images when the project uses local assets
- Ignoring `prefers-reduced-motion`

## Reference

Visual + motion reference: [adcker.com](https://adcker.com/). Implement the same *behavior* and *rhythm*, not necessarily the same libraries.
