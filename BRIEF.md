# KiiKO Forever Website — Build Brief

## Project Overview

Build a single-page HTML/CSS website for KiiKO, a Dublin/Tokyo-based electronic dream pop band. The site is a visual statement and a hub of outbound links. No framework, no build step. Pure HTML, CSS, and vanilla JS only.

The aesthetic is: **J-web chaos meets glitch art**. Think early-2000s Japanese band websites — dense, layered, overwhelming — but controlled. The chaos lives in the decorative layer. The functional layer stays clean and navigable.

---

## File Structure

Do not change this structure:

```
kiiko-forever/
├── index.html
├── css/
│   └── style.css
├── fonts/
│   ├── PP Kyoto - Extrabold v1.0/      → PPKyoto-Extrabold.woff2
│   ├── PP Kyoto - Light Italic v1.0/   → PPKyoto-LightItalic.woff2
│   ├── PP Kyoto - Light v1.0/          → PPKyoto-Light.woff2
│   └── PP Kyoto - Medium v1.0/         → PPKyoto-Medium.woff2
├── reference/
│   ├── reference-album-art.jpg         (visual reference for colour/type)
│   └── reference-sketch.png            (hand-drawn layout sketch)
```

---

## Fonts

The typeface is **PP Kyoto** in four weights. Register all four with `@font-face` in style.css. Use `woff2` format only. Use `font-display: swap`.

Font usage:
- **PP Kyoto Extrabold (800)** — "KiiKO Forever" first line in the katakana cascade
- **PP Kyoto Medium (500)** — all links (nav + footer), kaomoji cluster, all general text
- **PP Kyoto Light (300)** — Japanese katakana lines (キーコ・フォーエバー)
- **PP Kyoto Light Italic (300 italic)** — used exclusively for the doubled `ii` characters (see Double-I Rule)

---

## Layout — Mobile First, No Scroll

The page is a single viewport. **No scrolling.** Everything fits within `100vh`. White background (`#ffffff`).

### Main container

- **Mobile:** full viewport width
- **Desktop (min-width: 768px):** `max-width: 480px`, centred with `margin: 0 auto`. White gutters on either side.

---

## Two-Layer Architecture

The page has two visual layers within the main container:

### Layer 1 — Content layer (back, lower z-index)

Contains all interactive content: kaomoji cluster, press photo, nav links, footer. This layer is a **flexbox column filling 100vh**.

- **Width:** 80% of the main container
- **Position:** starts at 20% from the left edge (i.e., `margin-left: 20%`)
- **All text is right-aligned** (`text-align: right`)
- Uses `justify-content: space-between` to separate the top content group from the footer

**Top group** (tightly spaced, small gaps between items):

1. **Kaomoji cluster** — decorative, not links, small (10–11px), black, PP Kyoto Medium:
   ```
   ゜ﾟ**ﾟ
   . · ✦  . · * .
   (ﾉ◕ヮ◕)ﾉ*:･ﾟ✧
   ```

2. **Press photo** — 4:3 landscape ratio, fills 100% of the content layer width. No rounded corners. Placeholder: `background: #e0e0e0` with "Press Photo" centred in small grey type. `/* TODO: replace with actual press photo from Callum Browne */`

3. **Navigation links** — PP Kyoto Medium, black (`#000`), 16–18px, no underline. Each is a single `<a>` tag (word + kaomoji together):
   ```
   Liisten (♪´▽｀♪)       → https://open.spotify.com/artist/1d6oAwrCelcwZHgQk2ZHsY
   Watch (⌐■_■)           → https://www.youtube.com/@KiiKO_music
   Buy (づ ̄ ³ ̄)づ        → https://kiiko-ongaku.bandcamp.com/track/kiiko-forever
   Press m(_ _)m           → # (placeholder for EPK/OneDrive link)
   ```

**Footer** (pushed to bottom by `space-between`):

Right-aligned, PP Kyoto Medium, black, same size as nav links (16–18px). Each line is a single `<a>` tag (word + kaomoji together):
```
iinstagram [◉°]           → https://www.instagram.com/kiiko_ongaku/
TiikTok (〜￣▽￣)〜        → https://www.tiktok.com/@kiiko.music
Contact ✉(•ω•✉)          → mailto:kiiko.music@outlook.com
```

---

### Layer 2 — Katakana cascade (front, higher z-index)

A purely decorative text block, left-aligned from the left edge of the main container. It sits on top of the content layer. The press photo is visible behind it (the image extends under this layer).

- `pointer-events: none`, `user-select: none`
- `position: absolute`, `top: 0`, `left: 0`
- Generates enough lines to fill the full viewport height

**Content:**

```
KiiKO Forever          ← Line 1: PP Kyoto Extrabold, 13–14px
キーコ・フォーエバー    ← Line 2+: PP Kyoto Light, 13–14px
キーコ・フォーエバー
キーコ・フォーエバー
キーコ・フォーエバー
... (continues to fill viewport height)
```

- `line-height: ~1.1` — rows nearly touching, tightly packed
- One string per line. No horizontal repetition.
- The Japanese lines use **negative `letter-spacing`** so their rendered width matches the English "KiiKO Forever" line exactly. This creates a perfectly flush text block.

**Rainbow colouring — per character, full saturation:**

Each character is wrapped in a `<span>` with an inline colour style. Colours cycle through:

```
red:    #ff2d2d
orange: #ff8c00
yellow: #ffd700
green:  #00c04b
blue:   #0077ff
purple: #8b00ff
pink:   #ff69b4
```

The English line (line 1) uses **offset 0** — first character starts at red:
```javascript
colour = colours[charIndex % 7]
// K=red, i=orange, i=yellow, K=green, O=blue, (space)=purple, F=pink, o=red, r=orange, ...
```

All Japanese lines use **offset 5** — first character starts at purple:
```javascript
colour = colours[(charIndex + 5) % 7]
// キ=purple, ー=pink, コ=red, ・=orange, フ=yellow, ォ=green, ー=blue, エ=purple, バ=pink, ー=red
```

Generate this programmatically with JavaScript on `DOMContentLoaded`.

---

## The Double-I Rule

**Every English word containing the letter "i" or "I" has that letter replaced with "ii", always lowercase, rendered in PP Kyoto Light Italic.**

Rules:
- Lowercase `i` → `ii` (Light Italic)
- Uppercase `I` → `ii` (Light Italic, **lowercase** — not `II`)
- The surrounding word remains in its original weight/style
- The `ii` characters are always PP Kyoto Light Italic regardless of context

Implementation: create a JavaScript utility function `applyDoubleI(text)` that returns an HTML string with every `i` or `I` replaced by `<em class="double-i">ii</em>`.

```css
.double-i {
  font-family: 'PP Kyoto', sans-serif;
  font-weight: 300;
  font-style: italic;
}
```

**Special case — "KiiKO":** The band name already contains "ii" by design. Do not process it through `applyDoubleI`. Hardcode it: `K<em class="double-i">ii</em>KO`.

Applied to:
- `Listen` → `L<em class="double-i">ii</em>sten`
- `Instagram` → `<em class="double-i">ii</em>nstagram`
- `TikTok` → `T<em class="double-i">ii</em>kTok`

Not applied to:
- Japanese text (キーコ・フォーエバー)
- The background "KiiKO Forever" line (left as-is — "KiiKO" is already compliant, "Forever" has no i)

---

## Hover Effect

All links (nav + footer) use **per-character discrete rainbow colouring** on hover.

Each character in the link (including the kaomoji) is assigned a colour from the 7-colour palette, cycling by character index. This matches the katakana cascade aesthetic.

Implementation:
- All link text must be pre-split into individual character `<span>` elements
- On hover, each span receives its rainbow colour via CSS (e.g., `.link:hover .char-0 { color: #ff2d2d }` or using CSS custom properties)
- Default state: black (`#000`), no underline
- No translateX glitch, no animation — just the colour change on hover

---

## CSS Architecture

All styles in `css/style.css`. No inline styles except for the dynamically generated rainbow character colours in the katakana cascade.

```css
:root {
  --color-bg: #ffffff;
  --color-text: #000000;
  --color-accent-1: #ff2d2d;
  --color-accent-2: #ff8c00;
  --color-accent-3: #ffd700;
  --color-accent-4: #00c04b;
  --color-accent-5: #0077ff;
  --color-accent-6: #8b00ff;
  --color-accent-7: #ff69b4;

  --font-family: 'PP Kyoto', sans-serif;
  --font-extrabold: 800;
  --font-medium: 500;
  --font-light: 300;
}
```

---

## Desktop Breakpoint

At `min-width: 768px`:
- Main container: `max-width: 480px`, `margin: 0 auto`
- White gutters on either side
- No other layout changes — the single-column design scales cleanly
- Font sizes may increase slightly (nav links: ~20px)

---

## Performance & Quality Notes

- `woff2` fonts only. No fallback formats.
- `font-display: swap` on all `@font-face` declarations.
- Katakana cascade generated once on `DOMContentLoaded`. No regeneration on resize.
- `pointer-events: none` and `user-select: none` on the katakana cascade layer.
- No external dependencies, CDN links, or npm packages. Vanilla only.
- Final page under 100kb excluding the press photo.
- All links must be proper `<a>` tags with `href` attributes.
- Page must be readable with JavaScript disabled (katakana cascade won't render, but all content and links remain accessible).

---

## Tone & Vibe Summary

When making judgment calls not covered above:
- **More restrained** rather than more chaotic
- **Black on white** rather than coloured text for functional elements
- **Smaller** rather than larger type
- **Less animation** rather than more

The chaos is the katakana cascade. The foreground is calm. That contrast is the whole point.
