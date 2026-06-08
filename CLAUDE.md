# WitwatX — Web Design Guidelines

## Project Overview

**Claude Kwartet Pro — Denksysteem** is a vanilla HTML/CSS/JS single-page application implementing a five-agent AI reasoning pipeline. No build tooling, no npm dependencies, no frameworks — one `index.html` file with embedded CSS and JS.

## Architecture

- Single file: `index.html` (HTML + CSS + JS, no bundler)
- Language: Dutch (UI labels, comments, prompts)
- API: Anthropic Claude via direct `fetch` calls (model `claude-sonnet-4-20250514`)
- State: `localStorage` for API key, theme, session history

---

## Design System

### Color Tokens (CSS custom properties in `:root`)

| Token | Dark | Light | Usage |
|---|---|---|---|
| `--bg` | `#08080a` | `#faf8f3` | Page background |
| `--surface` | `#101014` | `#ffffff` | Cards, panels |
| `--surface2` | `#16161c` | `#f5f2eb` | Inputs, nested surfaces |
| `--border` | `#1e1e28` | `#e8e2d4` | Subtle dividers |
| `--border2` | `#2a2a38` | `#d8d0bc` | Interactive borders |
| `--text` | `#e2e2f0` | `#1a1a20` | Body text |
| `--muted` | `#5a5a72` | `#707080` | Secondary text, placeholders |
| `--faint` | `#28283a` | `#c8c0a8` | Decorative elements |
| `--gold` | `#e8c46a` | `#b8902a` | Primary accent, CTA |
| `--gold2` | `#c9a84c` | — | Gold variant |
| `--danger` | `#f87171` | — | Errors, destructive |

### Agent Colors (invariant across themes)

| Token | Value | Agent |
|---|---|---|
| `--c1` | `#60a5fa` | Vraagsteller (Question Asker) — Blue |
| `--c2` | `#c084fc` | Beantwoorder (Answerer) — Purple |
| `--c3` | `#fb923c` | Beoordelaar (Evaluator) — Orange |
| `--c4` | `#4ade80` | Verbeteraar (Improver) — Green |
| `--c5` | `#f472b6` | Verdieper (Deepener) — Pink |

Each agent color has a paired `--cN-bg` background token at `rgba(..., 0.06)` (dark) / `rgba(..., 0.08)` (light).

### Typography

Three font families, each with a strict role:

| Family | Role | Weights |
|---|---|---|
| `Cormorant Garamond` serif | Body content, headers, story-telling | 300, 400, 600; italic variants |
| `Syne` sans-serif | UI chrome (buttons, labels, body font) | 400–800 |
| `JetBrains Mono` monospace | Metadata, timestamps, labels, codes | 300–500 |

Typography rules:
- Fluid heading: `clamp(2rem, 6.5vw, 3.5rem)` for `<h1>`
- Card body text: `1.1rem` / `line-height: 1.85` (Cormorant Garamond)
- Label / monospace text: `0.7–0.78rem` with `letter-spacing: 0.1–0.15em` and `text-transform: uppercase`
- Never mix font families within a single semantic role

### Spacing & Layout

- Max content width: `820px`, centered
- Container padding: `2rem 1.2rem 5rem`
- Gap rhythm: `0.3rem` (tight) → `0.5rem` → `0.7rem` → `1rem` → `1.4rem` → `2rem` → `2.5rem`
- Mobile breakpoint: `600px` (flex direction changes to column)

### Border Radius Scale

| Size | Usage |
|---|---|
| `6px` | Agent pills |
| `7px` | Small action buttons |
| `8px` | Inputs, small cards |
| `9px` | Main input field |
| `10px` | History items, inner elements |
| `12px` | Progress bar wrapper |
| `14px` | Primary cards, input box, modals |
| `999px` | Pills (chips, score tags, progress bar fill) |

### Shadows & Effects

- Glassmorphic modal overlay: `backdrop-filter: blur(8px)` + `rgba(0,0,0,0.7)` background
- Gold glow on hover (CTA): `box-shadow: 0 4px 20px rgba(232,196,106,0.25)`
- Gold focus ring on inputs: `box-shadow: 0 0 0 3px rgba(232,196,106,0.08)` + `border-color: var(--gold)`
- Crown icon glow: `filter: drop-shadow(0 0 20px rgba(232,196,106,0.4))`
- SVG noise texture overlay: `opacity: 0.6`, `pointer-events: none`, `z-index: 0`

### Transitions

| Duration | Usage |
|---|---|
| `0.15s` | Micro-interactions (chips, action buttons) |
| `0.2s` | Standard hover (icon buttons, inputs) |
| `0.3s` | Theme switch (background, color) |
| `0.5s cubic-bezier(0.16,1,0.3,1)` | Card entrance (`riseIn`) |

---

## Component Patterns

### Icon Button (`.icon-btn`)
```css
/* 38×38px, 10px radius, surface background */
/* Hover: gold border + gold color + translateY(-1px) */
/* Active: gold background, dark text */
```

### Card (`.card.cN`)
Each card has three layers:
1. Outer border at `rgba(--cN, 0.15)`
2. Card head at `rgba(--cN, 0.05)` with agent label in `--cN` color
3. Card body using Cormorant Garamond, `1.1rem`, `line-height: 1.85`

Card entrance: `animation: riseIn 0.5s cubic-bezier(0.16,1,0.3,1) both`

### Chip (`.chip`)
Transparent background, `border-radius: 999px`, muted color → gold on hover.

### Score Tag (`.score-tag`)
Pill-shaped, JetBrains Mono, three variants:
- `.high` → green (`#4ade80`)
- `.mid` → gold (`var(--gold)`)
- `.low` → danger (`var(--danger)`)

### Loader (`.loader-card`)
Three `5px` gold dots, staggered `pulse` animation (0s, 0.2s, 0.4s delay).

### Progress Bar
4px tall, `linear-gradient(90deg, c1→c2→c3→c4→c5)`, `transition: width 0.5s ease`.

### Modal
`backdrop-filter: blur(8px)`, max-width `600px`, `border-radius: 14px`.

### Agent Pill (`.agent-pill.aN`)
`JetBrains Mono 0.7rem`, `border-radius: 6px`, uses agent color for text/border/background.

---

## Interaction Guidelines

- All interactive elements get `cursor: pointer`
- Hover states on clickable surfaces: `border-color → var(--gold)`, subtle lift (`translateY(-1px)`) or slide (`translateX(3px)`)
- Disabled states: `opacity: 0.35`, `cursor: not-allowed`, `transform: none`
- Focus states: gold border + subtle gold glow ring
- No `outline: none` without a visible replacement

---

## Theme System

- Toggle via `data-theme` attribute on `<html>` element
- Persisted in `localStorage` as `"theme"` key
- Light mode only overrides surface, border, text, muted, faint, and gold tokens
- Agent colors (`--c1`–`--c5`) are theme-invariant
- Background transitions use `transition: background 0.3s, color 0.3s` on `body`

---

## Code Conventions

- CSS variables for all colors — never hardcode hex values except `#08080a` (dark base, used as button text color on gold backgrounds)
- Keep all styles in the `<style>` block in `index.html`; do not add external CSS files
- JS is inline in `<script>` at bottom of `index.html`
- No external JS dependencies — use native browser APIs only
- API key stored in `localStorage` as `"apiKey"`; never log or expose it
- Session history stored in `localStorage` as `"history"` (JSON array, max 20 entries)
- Use `fetch` with `stream: true` for SSE streaming from the Anthropic API

---

## Dos and Don'ts

**Do:**
- Use CSS custom properties from the token list above
- Follow the border-radius scale — pick the closest matching value
- Use Cormorant Garamond for content, Syne for UI, JetBrains Mono for metadata
- Add `riseIn` animation to any new card-like element
- Apply gold hover/focus treatment to interactive elements
- Keep the noise texture and glassmorphic modal overlay

**Don't:**
- Introduce npm packages, build tools, or external frameworks
- Add new font families
- Use hardcoded colors outside the token system
- Change the five agent colors — they carry semantic meaning
- Skip focus/hover states on interactive elements
- Add new files without a strong reason; prefer editing `index.html`
