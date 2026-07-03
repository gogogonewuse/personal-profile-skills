# Blog Generator Skill

A composable skill that generates a polished personal blog landing page — built around a universal animation language with brand-swappable visual identity.

Rather than hard-coding one design, this skill separates what is **universal** (animation choreography, structure, interaction patterns) from what is **brand-specific** (colors, fonts, copy, decorative symbols). Swap the brand layer, keep the motion system.

---

## ✨ Features

- **Layered architecture** — universal core decoupled from brand identity
- **Cinematic motion system** — loader gating, pin + zoom hero, scrub reveals, parallax
- **Graceful degradation** — each capability falls back to native equivalents when a library is unavailable
- **Framework-agnostic by design** — matches the host project's framework, animation library, and CSS approach
- **Fully parameterized blocks** — every section exposes key tuning parameters

---

## 📑 Table of Contents

- [Architecture](#architecture)
- [Motion System](#motion-system)
- [Structure](#structure)
- [Visual Language](#visual-language)
- [Implementation](#implementation)
- [Capability Requirements & Fallbacks](#capability-requirements--fallbacks)
- [Block Parameters](#block-parameters)
- [Usage](#usage)
- [License](#license)

---

## Architecture

The skill is organized into three layers. The **universal** layer stays constant across any brand; the **brand-specific** layer is what you swap to re-skin the output.

| Layer | Universal (keep) | Brand-specific (swap) |
| --- | --- | --- |
| **Motion** | Loader → Hero gate, pin + zoom, scrub reveals, parallax | — |
| **Structure** | Loader / Hero / Statement / Services / About / Footer | Section count, copy |
| **Visual** | Oversized display type, generous whitespace | Colors, fonts, decor symbol |
| **Implementation** | Match project's framework, animation lib, CSS approach | — |

---

## Motion System

The motion system defines the choreography that gives every generated page its signature feel. Each phase builds on the previous one, creating a continuous narrative from first paint to final scroll.

1. **Loader → Hero gate** — A timed loader animation serves as the entry gate; once complete, it releases the hero section into view.
2. **Pin + zoom** — The hero pins in place while the viewport scales, producing a depth-driven transition.
3. **Scrub reveals** — Content blocks reveal in lockstep with scroll position, tied to a scrub timeline for a tactile feel.
4. **Parallax** — Layered elements move at differential speeds to reinforce depth.

---

## Structure

Every generated page is composed from the same six canonical sections. The brand layer controls how many sections appear and what copy they contain.

- **Loader** — First-paint gate animation
- **Hero** — Pinned, scalable hero with the primary statement
- **Statement** — Word-by-word staggered manifesto
- **Services** — Staggered title / image / body with parallax, alternating left/right grid
- **About** — Context and background
- **Footer** — Index-style staggered closing block

---

## Visual Language

The visual identity rests on two universal principles, onto which brand-specific tokens are layered.

- **Oversized display type** — Typographic hierarchy driven by scale
- **Generous whitespace** — Breathing room that emphasizes motion

Brand tokens to define: color palette, typeface stack, and a recurring decorative symbol.

---

## Implementation

The skill adapts to the host project rather than imposing a stack. It detects and matches:

- The project's **framework** (component lifecycle, hooks)
- The **animation library** in use (GSAP, Motion, native CSS)
- The **CSS approach** (atomic, modules, vanilla)

This keeps the generated output consistent with the surrounding codebase.

---

## Capability Requirements & Fallbacks

Each motion capability has a preferred implementation path and a graceful fallback when dependencies are missing. The fallback column shows what is used when the preferred tooling is unavailable.

| Capability needed | If project has… | Otherwise… |
| --- | --- | --- |
| Scroll-scrub timeline | GSAP ScrollTrigger, Motion `scroll()`, CSS `animation-timeline: scroll()` | Native scroll + transform on scroll event *(last resort)* |
| Pin section while scrolling | ScrollTrigger pin, Motion `useScroll` + fixed positioning | `position: sticky` + scroll-linked transforms |
| Smooth scroll | Lenis, Locomotive, project smooth-scroll | Native scroll *(still valid)* |
| Time-based loader | Any tween lib or CSS `@keyframes` | CSS transitions |
| Component lifecycle cleanup | Framework hooks / `onDestroy` | Manual teardown on unmount |

---

## Block Parameters

Each block exposes a set of key parameters that control its animation timing and behavior. These are the primary tuning knobs for the skill.

| Block | Key params |
| --- | --- |
| **Loader** | lines `y: 100% → 0`, stagger `0.12s`, exit at `1.9s` slide up |
| **Hero** | scroll span `120%` viewport, scrub `~1s` lag *(desktop)*, pin, scale `2.4`, title fade `y: -60` |
| **Statement** | word stagger `0.03s`, enter when section top hits `75%` viewport, once |
| **Services** | title/image/body stagger, parallax `y: ±8%`, alt L/R grid |
| **Footer** | index stagger `0.1s`, label opacity `0.4` |

---

## Usage

1. Invoke the skill with a brand configuration (colors, fonts, copy, decor symbol).
2. The skill generates the six canonical sections with universal motion applied.
3. Swap the brand layer at any time to re-skin without touching the animation core.

```yaml
# Example brand config
colors:
  background: "#0a0a0a"
  foreground: "#fafafa"
fonts:
  display: "Inter, sans-serif"
  body: "Inter, sans-serif"
decor_symbol: "✦"
copy:
  hero: "Your headline here"
  statement: "A word-by-word staggered manifesto."
```

---

## License

MIT
