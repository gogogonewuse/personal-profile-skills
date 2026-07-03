# Scroll-Driven Landing — Tokens & Timelines

Stack-agnostic numeric specs. Map these values to whatever animation/scroll API the project uses.

## Color tokens (Adcker default)

| Token | Value | Use |
|-------|-------|-----|
| `--bg` | `#F5F2EC` | Page background |
| `--ink` | `#1A1A1A` | Primary text |
| `--ink-soft` | `#6B6B6B` | Body secondary |
| `--accent` | `#FF4D2E` | Hover / links |
| `--img-bg` | `#E8E3DA` | Image placeholder |

## Typography scale

| Level | Desktop | Mobile | Line-height |
|-------|---------|--------|-------------|
| Hero | `clamp(4rem, 18vw, 22rem)` | `clamp(3rem, 16vw, 6rem)` | 0.85 |
| Section title | `clamp(3rem, 9vw, 8rem)` | `clamp(2.5rem, 12vw, 4rem)` | 0.9 |
| Body | `clamp(1.1rem, 1.6vw, 1.5rem)` | `1.05rem` | 1.5 |
| Label/index | `0.85rem` / `0.8rem` | monospace utility face | 1.2 |

## Spacing

- Container max: `1400px`
- Horizontal pad: `clamp(1.5rem, 5vw, 5rem)`
- Section gap: `clamp(6rem, 16vh, 14rem)`
- Hero image initial width: `40vw`, border-radius `8px` → `0` on zoom

## Loader timeline (~2.4s total)

| Time | Event |
|------|-------|
| 0.00s | Overlay visible |
| 0.10s | Line 1 reveal starts |
| +0.12s | Each subsequent line stagger |
| 0.7s | Per-line duration, decelerating ease (e.g. power3.out) |
| 1.80s | All lines visible (hold) |
| 1.90s | Container exit `translateY: 0 → -100%` |
| 2.40s | `onComplete` → hide Loader, allow Hero entrance |

**Reduced motion:** skip or cut to ~0.5s; show content immediately.

## Hero scroll-linked animation

Pin the hero section while the user scrolls through a dedicated scroll distance.

| Parameter | Value |
|-----------|-------|
| Trigger | Hero section |
| Start | When hero top meets viewport top |
| Scroll distance | `120%` of viewport height |
| Pin | Yes — hero stays fixed during distance |
| Scrub | ~1s smoothing on desktop; instant on mobile (<768px) |

| Property | From | To | Easing |
|----------|------|-----|--------|
| Image scale | 1 | 2.4 (1.8 mobile) | linear with scroll |
| Image translateY | 0 | -10vh | linear with scroll |
| Border radius | 8px | 0 | linear with scroll |
| Title opacity | 1 | 0 | linear with scroll |
| Title translateY | 0 | -60px | linear with scroll (starts ~30% through) |

## Hero entrance (after Loader + preload gate)

| Target | Values |
|--------|--------|
| Title words | `y: 60→0`, `opacity: 0→1`, stagger `0.08s`, duration `1s`, decelerating ease |
| Image | `scale: 1.15→1`, `opacity: 0→1`, duration `1.2s`, delay `0.2s` |
| Decor | `opacity: 0→0.12`, duration `0.8s`, delay `0.4s` |

## Statement

- Enter when section top reaches `75%` of viewport; play once
- Words: `y: 30→0`, `opacity: 0→1`, stagger `0.03s`, decelerating ease
- Link: fade in after words; arrow hover `translateX(8px)`

## Service block

- Enter when section top reaches `70%` of viewport; play once
- Title: `y: 80→0`, duration `0.9s`
- Image: `scale: 1.2→1`, duration `1.1s`, overlap title by ~0.75s
- Body: `y: 40→0`, overlap image by ~0.85s
- Parallax (optional): image `y: -8%→8%` over section scroll span; half range on mobile
- Layout: odd sections image-left, even image-right (≥1024px); single column below

## Footer

- Index items: `( 01 )` … `( 05 )`, monospace, opacity 0.4
- Reveal: `y: 30→0`, stagger `0.1s`, enter at `85%` viewport

## Decor parallax

- Decor elements: opacity `0.12`, absolute positioned
- Over section scroll span: `y: ±5%`, alternating slight `x` shift
- Scrub with scroll; skip when reduced motion

## Smooth scroll (optional)

If the project already uses smooth scrolling:

- Wire scroll position updates into scroll-linked animations
- On resize (debounce 150ms): refresh smooth-scroll instance + remeasure all scroll triggers

If no smooth scroll: native scroll is fine; keep the same motion numbers.

## Responsive breakpoints

| Breakpoint | Behavior |
|------------|----------|
| ≥1024px | Full layout, alternating service grids |
| 768–1023px | Single column services, scaled type |
| <768px | Hero scale 1.8, instant scrub, parallax halved |

## Easing reference (language-agnostic)

| Name in spec | Character |
|--------------|-----------|
| decelerating / power3.out | Fast start, soft landing |
| power2.out | Moderate deceleration |
| linear with scroll | No ease curve — value tied 1:1 to scroll progress |
