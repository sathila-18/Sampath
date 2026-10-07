# AI Assistant Prototype — UI & Experience Guide

| | |
|---|---|
| **Status** | v0.4 · 2026-10-07 |
| **Scope** | AI assistant chat flow only, mobile only. Built as **one self-contained HTML file** with no framework (§13). |
| **Sources of truth** | 1. Figma variables (6 collections, exported 2026-10-07)<br>2. Chat UI frames: [sampath-chat-test › assistant](https://www.figma.com/design/p41jFOs3yRsffVgcs3ze1x/sampath-chat-test?node-id=1-1103) (node `1:1103`)<br>3. Design-system component file (to be provided) |
| **Audience** | Anyone building or reviewing the prototype, human or AI agent |

This guide is the contract between Figma and the prototype. If the build disagrees with this guide, the build is wrong. If this guide disagrees with Figma, Figma wins and this guide gets updated.

**Components.** The chat frames use two kinds of element:

- **Design-system component instances:** `Header-full` (with `Header-base` and `Nav-button`), `Badge-primary` (the date badge), `Chat field` (with `Input field-base` and `Pill-button`). The component file hasn't been provided yet. Until it is, build these from the specs in §8, which are read from the instances in the chat frames. When the component file arrives, it overrides §8 wherever they differ.
- **Plain frames, not componentised:** the user bubble, the assistant message and the timestamps. These are only bound to colour, typography, spacing and radius tokens. §8 is their spec.

Card components will be provided later as well (§9).

---

## 1. Principles for the assistant UI

1. **The user speaks in orange, the assistant on the page.** User messages are filled brand bubbles. Assistant replies are plain text set straight on the canvas, with no bubble.
2. **Structure through text.** Assistant replies use paragraphs and bulleted lists, as in the FD history example. Card components will be added once they're provided (§9).
3. **Nothing moves money without an explicit confirm.** This holds even though the confirm UI isn't designed yet (§10).
4. **Semantic tokens only.** Components never reference a primitive or alias colour directly (§2).
5. **Script-driven.** The prototype plays scripted conversations (§13.5). It is not connected to a live model.

---

## 2. Token architecture

Colors use three tiers. Typography and spacing use two. Component spacing has an optional fourth layer for guidance.

```
COLORS
  Primitives        raw hex             Orange-500 = #F47921
      ↓
  Alias Colors      role ramps          Primary1/Primary-500 → Orange-500
      ↓
  Color collection  semantic, moded     bg-brand → Primary-500   (Light / Dark)

TYPOGRAPHY
  Primitives        family, weights, sizes, line heights
      ↓
  Alias Typography  text styles         Body L = 16 / 24 / 400   (mode: Mobile)

SIZES
  Primitives        Size-0 … Size-1920
      ↓
  Alias Spacing & Radius                Space-XL = 16, Radius-L = 16
      ↓
  Component collection (guidance)       card-padding = Space-XL
```

### Rules

- **Components consume tier 3 only:** semantic colors, text styles, spacing/radius aliases and component tokens. Primitives and alias ramps exist in code only to feed the semantic layer.
- **Light/Dark are modes of the Color collection.** Code switches mode by swapping the semantic variables (`[data-theme="dark"]`). Component code never branches on theme.
- **Generate, don't transcribe.** Tokens in code are generated from the Figma JSON export by a script (§13.3). The tables in this file are for reading, not for copying.

### Naming in code

| Figma | Code (CSS custom property) | Rule |
|---|---|---|
| `Background/bg-action-primary_pressed` | `--bg-action-primary-pressed` | Drop the group name, lower-case, `_` → `-` |
| `Forground/fg-action` | `--fg-action` | Same rule. The group is spelled "Forground" in Figma; it doesn't appear in code names. |
| `Alpha/Alpha-nav` | `--alpha-nav` | Prefix `alpha-` |
| `Spacing/Space-XL` | `--space-xl` | |
| `Radius/Radius-Full` | `--radius-full` | |
| `Body L` (text style) | `.text-body-l` / `--font-body-l` | Text styles become utility classes or composite tokens |
| `Mobile/Component/card-padding` | `--card-padding` | |
| `Mobile/Layout/screeen-margin` | `--screen-margin` | Typo fixed in code ("screeen") |

---

## 3. Color

### 3.1 Primitives and alias ramps

Each alias ramp maps 1:1 onto a primitive ramp, step for step.

| Alias ramp | Primitive | 50 | 100 | 200 | 300 | 400 | 500 | 600 | 700 | 800 | 900 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Primary1 | Orange | `#FDE7D6` | `#FCD5BA` | `#FAC199` | `#F8A56A` | `#F6944D` | `#F47921` | `#DE6E1E` | `#AD5617` | `#864312` | `#66330E` |
| Primary2 | Spring wood | `#FEFEFD` | `#FDFBFA` | `#FBF9F8` | `#FAF7F4` | `#F9F5F2` | `#F7F3EF` | `#E1DDD9` | `#AFADAA` | `#888683` | `#686664` |
| Text | Mirage | `#E8E8E9` | `#B6B9BC` | `#93979C` | `#62686E` | `#434A52` | `#141D27` | `#121A23` | `#0E151C` | `#0B1015` | `#080C10` |
| Secondary | Woodsmoke | `#E8E8E8` | `#B6B6B7` | `#939394` | `#626263` | `#1E1E21` | `#141416` | `#121214` | `#0E0E10` | `#0B0B0C` | `#080809` |
| Tertiary | Violet | `#EFEAFC` | `#CDBFF5` | `#B59FF1` | `#9374EA` | `#7E59E6` | `#5E2FE0` | `#562BCC` | `#43219F` | `#341A7B` | `#27145E` |
| Success | Green | `#EAF6EF` | `#BEE4CC` | `#9FD7B3` | `#73C590` | `#58B97B` | `#2EA85A` | `#2A9952` | `#217740` | `#195C32` | `#244A3E` |
| Warning | Yellow | `#FCF3EA` | `#F4DABF` | `#EFC8A0` | `#E8AF75` | `#E49F5A` | `#DD8731` | `#C97B2D` | `#9D6023` | `#7A4A1B` | `#5D3915` |
| Error | Red | `#F9E9E9` | `#EDBABA` | `#E49998` | `#D86B69` | `#D14E4C` | `#C5221F` | `#B31F1C` | `#8C1816` | `#6C1311` | `#6B211B` |
| Warning secondary | Peach Yellow | `#FFFCF6` | `#FEF6E3` | `#FDF1D6` | `#FCEBC3` | `#FCE7B8` | `#FBE1A6` | `#E4CD97` | `#B2A076` | `#8A7C5B` | `#695F46` |

Key colors: `Key/White` = `#FFFFFF`, `Key/Black` = `#000000`.

**Brand in one line:** warm orange (`#F47921`) on a warm off-white canvas (`Spring wood #F7F3EF`), with near-black navy text (`Mirage #141D27`). Violet is used for info states. Dark mode uses neutral near-black surfaces (`Woodsmoke`).

### 3.2 Semantic colors

Each row gives the resolved value and the alias step it maps to, for each mode. The usage notes are the descriptions from Figma.

| Token | Code | Light | Dark | Usage (from Figma) |
|---|---|---|---|---|
| `bg-primary` | `--bg-primary` | `#F7F3EF` · Primary2-500 | `#141416` · Secondary-500 | Use this for main background of the screens |
| `bg-secondary` | `--bg-secondary` | `#FFFFFF` · White | `#1E1E21` · Secondary-400 | Use this color for card frames, popups, modals, input field, bottom nav, action icons |
| `bg-tertiary` | `--bg-tertiary` | `#FCD5BA` · Primary-100 | `#AD5617` · Primary-700 | Use this for info callout notification backgrounds |
| `bg-neutral` | `--bg-neutral` | `#F7F3EF` · Primary2-500 | `#080809` · Secondary-900 | Use this for badge neutral background |
| `bg-brand` | `--bg-brand` | `#F47921` · Primary-500 | `#F47921` · Primary-500 | Use this for brand related backgrounds |
| `bg-active` | `--bg-active` | `#FDE7D6` · Primary-50 | `#66330E` · Primary-900 | Use this color for active or selected state backgrounds |
| `bg-active-hover` | `--bg-active-hover` | `#FCD5BA` · Primary-100 | `#864312` · Primary-800 | Use this color for hover state of active or selected backgrounds |
| `bg-action-primary` | `--bg-action-primary` | `#DE6E1E` · Primary-600 | `#F47921` · Primary-500 | Mainly use for primary button backgrounds |
| `bg-action-primary_pressed` | `--bg-action-primary-pressed` | `#AD5617` · Primary-700 | `#AD5617` · Primary-700 | Mainly use for pressed or hover states of primary button backgrounds |
| `bg-action-secondary` | `--bg-action-secondary` | `#E1DDD9` · Primary2-600 | `#080809` · Secondary-900 | Use this for swipe button background |
| `bg-action-secondary_pressed` | `--bg-action-secondary-pressed` | `#FCD5BA` · Primary-100 | `#864312` · Primary-800 | Mainly use for pressed or hover states of secondary button backgrounds |
| `bg-action-inverse` | `--bg-action-inverse` | `#FFFFFF` · White | `#FFFFFF` · White |  |
| `bg-action-destructive` | `--bg-action-destructive` | `#C5221F` · Error-500 | `#D14E4C` · Error-400 | Use this color for destructive buttons |
| `bg-disabled` | `--bg-disabled` | `#E1DDD9` · Primary2-600 | `#626263` · Secondary-300 | Use this for disabled backgrounds |
| `bg-dark` | `--bg-dark` | `#080809` · Secondary-900 | `#27145E` · Tertiary-900 | Use this for toast/snackbar backgrounds |
| `bg-success` | `--bg-success` | `#BEE4CC` · Success-100 | `#244A3E` · Success-900 | Use this for success backgrounds. (notification, badge) |
| `bg-success_secondary` | `--bg-success-secondary` | `#EAF6EF` · Success-50 | `#195C32` · Success-800 | Use this for success backgrounds. (notification, badge) |
| `bg-error` | `--bg-error` | `#EDBABA` · Error-100 | `#6B211B` · Error-900 | Use this for error backgrounds. (notification, badge) |
| `bg-error_secondary` | `--bg-error-secondary` | `#F9E9E9` · Error-50 | `#6C1311` · Error-800 | Use this for error backgrounds. (notification, badge) |
| `bg-warning` | `--bg-warning` | `#F4DABF` · Warning-100 | `#5D3915` · Warning-900 | Use this for warning backgrounds. (notification, badge) |
| `bg-info` | `--bg-info` | `#CDBFF5` · Tertiary-100 | `#27145E` · Tertiary-900 | Use this for info backgrounds. (notification, badge) |
| `bg-info_secondary` | `--bg-info-secondary` | `#EFEAFC` · Tertiary-50 | `#341A7B` · Tertiary-800 | Use this for info backgrounds. (notification, badge) |
| `bg-icons` | `--bg-icons` | `#FDE7D6` · Primary-50 | `#66330E` · Primary-900 | Use this for icon or avatar backgrounds |
| `bg-avatar` | `--bg-avatar` | `#E8E8E8` · Secondary-50 | `#080809` · Secondary-900 | Use this for avatar backgrounds with logos |
| `bg-priority` | `--bg-priority` | `#FBE1A6` · WarningSecondary-500 | `#695F46` · WarningSecondary-900 | Use this for priority banners |
| `bg-gold` | `--bg-gold` | `#FCE7B8` · WarningSecondary-400 | `#8A7C5B` · WarningSecondary-800 | Use this for loyalty backgrounds |

| Token | Code | Light | Dark | Usage (from Figma) |
|---|---|---|---|---|
| `text-primary` | `--text-primary` | `#141D27` · Text-500 | `#FDFBFA` · Primary2-100 | Use this for Titles and headings |
| `text-secondary` | `--text-secondary` | `#62686E` · Text-300 | `#E1DDD9` · Primary2-600 | Use this for description texts |
| `text-tertiary` | `--text-tertiary` | `#434A52` · Text-400 | `#AFADAA` · Primary2-700 | Use this for captions, overlines, hints |
| `text-placeholder` | `--text-placeholder` | `#62686E` · Text-300 | `#B6B9BC` · Text-100 | Use this for input field placeholder text |
| `text-disabled` | `--text-disabled` | `#B6B9BC` · Text-100 | `#62686E` · Text-300 | Use this for disabled text |
| `text-disabled-on_bg` | `--text-disabled-on-bg` | `#93979C` · Text-200 | `#B6B9BC` · Text-100 | Use this for disabled text on backgrounds. (buttons, badge, chips) |
| `text-inverse` | `--text-inverse` | `#FFFFFF` · White | `#FFFFFF` · White | Use this text color on brand  backgrounds |
| `text-dark_both` | `--text-dark-both` | `#141D27` · Text-500 | `#141D27` · Text-500 | Use this text color on brand  backgrounds |
| `text-inverse_subtle` | `--text-inverse-subtle` | `#B6B9BC` · Text-100 | `#B6B9BC` · Text-100 | Secondary text color for on brand backgrounds |
| `text-action` | `--text-action` | `#DE6E1E` · Primary-600 | `#F47921` · Primary-500 | Use this for text links, secondary buttons |
| `text-action_pressed` | `--text-action-pressed` | `#AD5617` · Primary-700 | `#F6944D` · Primary-400 | Pressed state color for text-link |
| `text-active` | `--text-active` | `#F47921` · Primary-500 | `#F47921` · Primary-500 | Use this for input field lables in typing or complete states |
| `text-success` | `--text-success` | `#2EA85A` · Success-500 | `#58B97B` · Success-400 | Use this for success texts |
| `text-success_secondary` | `--text-success-secondary` | `#195C32` · Success-800 | `#BEE4CC` · Success-100 | Secondar darker version of a success color (notifications, banners) |
| `text-error` | `--text-error` | `#C5221F` · Error-500 | `#D14E4C` · Error-400 | Use this for error texts |
| `text-error_secondary` | `--text-error-secondary` | `#6C1311` · Error-800 | `#EDBABA` · Error-100 | Secondar darker version of a error text (notifications, banners) |
| `text-info` | `--text-info` | `#5E2FE0` · Tertiary-500 | `#7E59E6` · Tertiary-400 | Use this for information texts |
| `text-info_secondary` | `--text-info-secondary` | `#341A7B` · Tertiary-800 | `#CDBFF5` · Tertiary-100 | Secondar darker version of a infomartion text (notifications, banners) |
| `text-warning` | `--text-warning` | `#DD8731` · Warning-500 | `#E49F5A` · Warning-400 | Use this for warning texts |
| `text-warning_secondary` | `--text-warning-secondary` | `#7A4A1B` · Warning-800 | `#F4DABF` · Warning-100 | Secondar darker version of a warning text (notifications, banners) |
| `text-gold` | `--text-gold` | `#8A7C5B` · WarningSecondary-800 | `#FDF1D6` · WarningSecondary-200 | Use this for loyalty texts |

| Token | Code | Light | Dark | Usage (from Figma) |
|---|---|---|---|---|
| `border-default` | `--border-default` | `#E1DDD9` · Primary2-600 | `#686664` · Primary2-900 | dedault border color. Can use this for dividers as well |
| `border-subtle` | `--border-subtle` | `#E8E8E8` · Secondary-50 | `#434A52` · Text-400 | Subtle version of border color |
| `border-strong` | `--border-strong` | `#888683` · Primary2-800 | `#E1DDD9` · Primary2-600 | Strong version of border color (radio buttons) |
| `border-active` | `--border-active` | `#F47921` · Primary-500 | `#F47921` · Primary-500 | Use for active or selected states. Can use for typing state of input fields. |
| `border-focused` | `--border-focused` | `#F47921` · Primary-500 | `#F47921` · Primary-500 | Use this for focus ring, or focused states |
| `border-action-primary` | `--border-action-primary` | `#F47921` · Primary-500 | `#F47921` · Primary-500 | Use this for outine (secondary) buttons |
| `border-disabled` | `--border-disabled` | `#939394` · Secondary-200 | `#626263` · Secondary-300 | Use for disabled borders |
| `border-inverse` | `--border-inverse` | `#FFFFFF` · White | `#FFFFFF` · White | Borders on brand color |
| `border-success` | `--border-success` | `#2EA85A` · Success-500 | `#2EA85A` · Success-500 | Border for success color |
| `border-error` | `--border-error` | `#C5221F` · Error-500 | `#C5221F` · Error-500 | Border for error color |
| `border-info` | `--border-info` | `#5E2FE0` · Tertiary-500 | `#5E2FE0` · Tertiary-500 | Border for information color |
| `border-warning` | `--border-warning` | `#DD8731` · Warning-500 | `#DD8731` · Warning-500 | Border for warning color |
| `border-gold` | `--border-gold` | `#E4CD97` · WarningSecondary-600 | `#FBE1A6` · WarningSecondary-500 | Border for loyalty color |

| Token | Code | Light | Dark | Usage (from Figma) |
|---|---|---|---|---|
| `fg-primary` | `--fg-primary` | `#141D27` · Text-500 | `#FDFBFA` · Primary2-100 | Primary icon color |
| `fg-secondary` | `--fg-secondary` | `#62686E` · Text-300 | `#E1DDD9` · Primary2-600 | Secondary icon color. (input fields, Close, cheveron) |
| `fg-brand` | `--fg-brand` | `#F47921` · Primary-500 | `#F6944D` · Primary-400 | Brand related icons |
| `fg-placeholder` | `--fg-placeholder` | `#939394` · Secondary-200 | `#B6B6B7` · Secondary-100 | Icon color for input field icons |
| `fg-active` | `--fg-active` | `#DE6E1E` · Primary-600 | `#F6944D` · Primary-400 | Icons on active state |
| `fg-disabled` | `--fg-disabled` | `#B6B9BC` · Text-100 | `#62686E` · Text-300 | disabled icon |
| `fg-disabled-on_bg` | `--fg-disabled-on-bg` | `#93979C` · Text-200 | `#B6B9BC` · Text-100 | Disabled icon color on background |
| `fg-inverse` | `--fg-inverse` | `#FFFFFF` · White | `#FFFFFF` · White | Icons on brand color |
| `fg-dark_both` | `--fg-dark-both` | `#62686E` · Text-300 | `#62686E` · Text-300 | Icons on brand color |
| `fg-white_both` | `--fg-white-both` | `#FFFFFF` · White | `#FFFFFF` · White | Icons on brand color |
| `fg-action` | `--fg-action` | `#DE6E1E` · Primary-600 | `#F6944D` · Primary-400 | Color for action icons |
| `fg-action_pressed` | `--fg-action-pressed` | `#AD5617` · Primary-700 | `#F8A56A` · Primary-300 | Pressed or hover state color for action icons |
| `fg-success` | `--fg-success` | `#2EA85A` · Success-500 | `#58B97B` · Success-400 | Color for success icons |
| `fg-error` | `--fg-error` | `#C5221F` · Error-500 | `#D14E4C` · Error-400 | Color for error icons |
| `fg-info` | `--fg-info` | `#5E2FE0` · Tertiary-500 | `#7E59E6` · Tertiary-400 | Color for information icon |
| `fg-warning` | `--fg-warning` | `#DD8731` · Warning-500 | `#E49F5A` · Warning-400 | Color for warning icons |
| `fg-gold` | `--fg-gold` | `#8A7C5B` · WarningSecondary-800 | `#FCE7B8` · WarningSecondary-400 | Color for loyalty icons |

| Token | Code | Light | Dark | Usage (from Figma) |
|---|---|---|---|---|
| `Alpha-sm` | `--alpha-sm` | `#141416 @ 16%` · raw | `#141416 @ 16%` · raw | mainly used in the small CTAs inside branded cards |
| `Alpha-md` | `--alpha-md` | `#141416 @ 40%` · raw | `#141416 @ 40%` · raw |  |
| `Alpha-lg` | `--alpha-lg` | `#141416 @ 75%` · raw | `#141416 @ 75%` · raw | Used in scan QR code interface overlay |
| `Alpha-light` | `--alpha-light` | `#FFFFFF @ 12%` · raw | `#FFFFFF @ 12%` · raw | Use this to show more depth to element's on brand background  |
| `Alpha-nav` | `--alpha-nav` | `#FFFFFF @ 75%` · raw | `#FFFFFF @ 6%` · raw | Used this in bottom navigation backgrounds, and loading overlay |
| `Alpha-nav_background` | `--alpha-nav-background` | `#FFFFFF @ 40%` · raw | `#141416 @ 40%` · raw |  |
| `Alpha-shade` | `--alpha-shade` | `#F7F3EF @ 0%` · raw | `#141416 @ 0%` · raw |  |
| `Alpha-both` | `--alpha-both` | `#141416 @ 42%` · raw | `#141416 @ 42%` · raw | Used in overlay backgrounds |
| `Alpha-border` | `--alpha-border` | `#141416 @ 8%` · raw | `#141416 @ 2%` · raw | used for borders of back icon circle |
| `Alpha-success` | `--alpha-success` | `#2EA85A @ 16%` · raw | `#2EA85A @ 16%` · raw |  |
| `Alpha-brand` | `--alpha-brand` | `#F26722 @ 16%` · raw | `#F26722 @ 16%` · raw |  |
| `Alpha-error` | `--alpha-error` | `#C5221F @ 16%` · raw | `#C5221F @ 16%` · raw |  |

Alpha tokens are raw values in Figma, not aliased to primitives. `Alpha-brand` uses `#F26722`, which isn't in the Orange ramp. See §14 #8.

### 3.3 Contrast check (WCAG 2.1 AA)

AA needs 4.5:1 for normal text, and 3:1 for large text (≥ 24px regular or ≥ 18.66px bold) and for UI boundaries.

**Pairs used in the chat frames**

| Where | Pair | Light | Dark | Result |
|---|---|---|---|---|
| **User bubble text** (Label M, 14px/600) | text-inverse on bg-brand | **2.76** | **2.76** | Below AA. **Accepted as designed** for the prototype. |
| Assistant message text | text-tertiary on bg-primary | 8.13 | 8.22 | ✅ |
| Timestamps (10px) | text-tertiary on bg-primary | 8.13 | 8.22 | ✅ |
| Date badge | text-secondary on bg-secondary | 5.64 | 12.31 | ✅ |
| Intro subtitle | text-secondary on bg-primary | 5.11 | 13.62 | ✅ |
| Intro greeting, header title | text-primary on bg-primary | 15.40 | 17.83 | ✅ |
| Composer placeholder | text-placeholder on bg-secondary | 5.64 | — | ✅ |

**Other system pairs, for when more of the flow is designed**

| Pair | Light | Dark | Result |
|---|---|---|---|
| text-inverse on bg-action-primary (primary button) | 3.30 | 2.76 | ⚠️ / ❌ |
| text-action on bg-primary (text links) | 2.99 | 6.66 | ❌ in light mode |
| text-warning on bg-secondary | 2.77 | — | ❌ |
| text-success on bg-secondary | 3.06 | — | ⚠️ large text only |
| border-focused vs bg-secondary (focus ring) | 2.76 | — | ⚠️ just under 3:1 |

**Prototype stance:** built exactly as designed (white on `bg-brand`). If this is revisited later, two passing options are `text-dark_both` on `bg-brand` (6.16:1), or white on Primary-700 (5.10:1).

---

## 4. Typography

**Family:** Hanken Grotesk, for everything. It's open-source (SIL OFL) and on Google Fonts. Load weights 400, 500, 600, 700 and 800.

**OpenType:** every text layer in the chat frames has `font-feature-settings: "case" 1` (case-sensitive forms) applied. Set it globally on `body`.

### 4.1 Where each style is used in the chat

| Element | Style | Size / LH / Weight | Colour |
|---|---|---|---|
| Header title ("AI Assistant") | Title/Large | 18 / 25.2 / 600 | text-primary |
| Date badge ("Today 9:41 AM") | Label/Small | 12 / 18 / 600 | text-secondary |
| User message | Label/Medium | 14 / 21 / 600 | text-inverse |
| Assistant message (paragraphs and bullets) | Label/Medium | 14 / 21 / 600 | text-tertiary |
| Message timestamp ("9:41 AM") | Caption/Small | 10 / 15 / 600 | text-tertiary |
| Composer input and placeholder | Body/Large | 16 / 24 / 400 | text-primary / text-placeholder |
| Intro greeting | Headline/Medium-H5 | 20 / 28 / 700 | text-primary |
| Intro subtitle | Body/Medium | 14 / 21 / 400 | text-secondary |

Messages are set in **Label M (semibold)**, not a Body style. That's a deliberate choice in the frames, and the code should follow it.

The composer uses Body L at 16px. That also stops iOS Safari zooming in when the field gets focus.

### 4.2 Full scale

| Style (Figma text style) | Variables | Size / Line height | Weight |
|---|---|---|---|
| Display/Large-H1 | Display Large-H1 | 36 / 46.8 | 800 |
| Display/Medium-H2 | Display Medium-H2 | 32 / 41.6 | 800 |
| Display/Small-H3 | Display Small-H3 | 28 / 39.2 | 800 |
| Headline/Large-H4 | Headline Large-H4 | 24 / 33.6 | 800 |
| Headline/Medium-H5 | Headline Medium-H5 | 20 / 28 | 700 |
| Headline/Small-H6 | Headline Small-H6 | 16 / 24 | 700 |
| Title/XLarge | Title XL | 32 / 41.6 | 600 |
| Title/Large | Title L | 18 / 25.2 | 600 |
| Title/Medium | Title M | 16 / 24 | 600 |
| Title/Small | Title S | 14 / 21 | 600 |
| Body/Large | Body L | 16 / 24 | 400 |
| Body/Medium | Body M | 14 / 21 | 400 |
| Body/Small | Body S | 12 / 18 | 400 |
| Label/Large | Label L | 16 / 24 | 600 |
| Label/Medium | Label M | 14 / 21 | 600 |
| Label/Small | Label S | 12 / 18 | 600 |
| Caption/Large | Caption L | 12 / 18 | 500 |
| Caption/Medium | Caption M | 11 / 16.5 | 500 |
| Caption/Small | Caption S | 10 / 15 | 600 |
| Button/XLarge | Button XL | 18 / 24 | 700 |
| Button/Large | Button L | 16 / 24 | 700 |
| Button/Medium | Button M | 14 / 24 | 700 |
| Button/Small | Button S | 12 / 24 | 700 |

All styles have 0 letter spacing.

**Line-height system:** 1.3× for 32 to 36px, 1.4× for 18 to 28px, and 1.5× for 16px and below. Buttons use a fixed 24px.

**In code:** each style is one CSS shorthand token generated from the variables, e.g. `--font-label-m: 600 14px/21px 'Hanken Grotesk'`, used as `font: var(--font-label-m)`.

**Bullets:** lists use the font's own `•` glyph via `::marker`, which Hanken Grotesk draws as a small square, matching Figma. CSS's default `disc` would draw a round dot instead.

---

## 5. Spacing & radius

### 5.1 Spacing

| Token | px | | Token | px |
|---|---|---|---|---|
| `Space-none` | 0 | | `Space-4XL` | 32 |
| `Space-XXS` | 2 | | `Space-5XL` | 40 |
| `Space-XS` | 4 | | `Space-6XL` | 48 |
| `Space-S` | 6 | | `Space-7XL` | 48 ⚠️ same as 6XL |
| `Space-M` | 8 | | `Space-8XL` | 56 |
| `Space-L` | 12 | | `Space-9XL` | 64 |
| `Space-XL` | 16 | | `Space-10XL` | 80 |
| `Space-2XL` | 20 | | `Space-11XL` | 96 |
| `Space-3XL` | 24 | | `Space-12XL` | 120 |

`Space-13XL` to `Space-18XL` (343, 375, 1024, 1280, 1440, 1920) are layout widths, not spacing. In code they become `--width-content` (343), `--width-frame` (375) and breakpoint constants. They are never used as margins or gaps.


### 5.2 Radius

| Token | px | Used in the chat frames |
|---|---|---|
| `Radius-none` | 0 | |
| `Radius-XS` | 4 | The "tail" corner of the user bubble (bottom-right) |
| `Radius-S` | 8 | |
| `Radius-M` | 12 | |
| `Radius-L` | 16 | The other three corners of the user bubble |
| `Radius-XL` | 24 | |
| `Radius-Full` | 999999 → `9999px` in code | Composer field, send button, header icon buttons, date badge |

---

## 6. Layout & grid

### 6.1 Frame

| | |
|---|---|
| Base frame | 375 × 812 |
| Grid | **4 columns · 16px margin · 12px gutter** |
| Content width | 343 (`Size-343`) = 375 − 2 × 16 |
| Column width | 76.75 = (343 − 3 × 12) / 4 |
| Safe areas | The frames include a 44px iOS status bar. In code, use `env(safe-area-inset-top)` in its place, and add `env(safe-area-inset-bottom)` under the composer. |

Columns stretch and margins stay at 16px. The prototype renders in a 375-wide phone frame on desktop and full-bleed on a real phone.

### 6.2 Component spacing rules (guidance)

From the Component collection. These aren't bound in the Figma UI, but code uses them as the default rules.

| Token | Value | Rule |
|---|---|---|
| `screen-margin` | 16 (Space-XL) | Left/right margin on every screen |
| `section-gap` | 32 (Space-4XL) | Between main sections in a page |
| `header-to-content` | 24 (Space-3XL) | Header to first component |
| `card-padding` | 16 (Space-XL) | Inside every card, all sides |
| `card-gap_small` / `_medium` / `_large` | 8 / 16 / 24 | Gaps inside cards |
| `input-padding` | 16 (Space-XL) | Inside input fields |
| `list-padding` | 16 (Space-XL) | Inside list components |
| `icon-text` | 8 (Space-M) | Icon to label |
| `button-gap` | 12 (Space-L) | Between adjacent buttons |

### 6.3 Chat screen measurements

Read from the chat frame (`1:1104`). "Raw" means the value in the frame isn't bound to a token; the token in brackets has the same value and is what code uses.

```
 0 ┌───────────────────────────────┐
   │ status bar                44  │
44 ├───────────────────────────────┤
   │ header                    64  │  Title L · 2 × 48px icon buttons
108├───────────────────────────────┤
   │            ↕ 16               │  Space-XL
124│        [ Today 9:41 AM ]  22  │  date badge, centered
146│            ↕ 24               │  Space-3XL
170│  messages … (16 margin)       │
   │                               │
708├───────────────────────────────┤
   │ composer (gradient fade)  104 │  48 top · 40 field · 16 bottom
812└───────────────────────────────┘
```

| Measurement | Value |
|---|---|
| Header → date badge | 16 · Space-XL |
| Date badge → first message | 24 · Space-3XL (matches `header-to-content`) |
| Between message groups (turns) | 16 · raw (Space-XL) |
| Message → its timestamp | 4 · raw (Space-XS) |
| Between paragraphs inside an assistant message | 16 · Space-XL |
| User bubble padding | 12 vertical · 16 horizontal (Space-L / Space-XL) |
| User bubble width | Hugs its text, right-aligned, up to the full 343 content width. The frame's sample text is long enough to fill it. (open question §14 #1) |
| Assistant message width | Fills the content width (343) |
| Bullet indent | 21 · raw |

---

## 7. Elevation & motion

### 7.1 Shadows (Figma effect styles)

Only two effect styles are used in the chat frames. No other shadows are used in the prototype.

| Effect style | Figma value | CSS | Used on |
|---|---|---|---|
| `Shadow/Resting_cards` | Drop shadow 0 / 1 / blur 3, `#000000` @ 5% | `box-shadow: 0 1px 3px rgba(0,0,0,.05)` | User bubble, date badge, header icon buttons |
| `Shadow/Floating_nav, search` | Drop 0 / 2 / 8, `#140F0A` @ 8% · Drop 0 / 8 / 30, `#140F0A` @ 16% · Background blur 20 | `box-shadow: 0 2px 8px rgba(20,15,10,.08), 0 8px 30px rgba(20,15,10,.16); backdrop-filter: blur(10px)` | Composer field |

These are effect styles, not variables, so they're defined once in the elevation block of the prototype. Shadow colours `#000000` and `#140F0A` aren't tokens, and they don't change in dark mode (§14 #6). Figma's background blur 20 corresponds to roughly CSS `blur(10px)`; check this visually.

### 7.2 Motion (placeholder)

Figma has no motion defined. The prototype needs the minimum below to play scripts; replace it when motion is designed.

| What | Placeholder |
|---|---|
| New message appears | 200ms fade |
| Assistant "thinking" before a reply | See §9: not designed. Placeholder: three dots in `fg-secondary`, plain on the canvas |
| Reply text | Appears whole by default. Optional word-by-word reveal per script. |
| Reduced motion | `prefers-reduced-motion` turns all of the above off |

---

## 8. Chat components

All values below are from the token bindings in the frames.

### 8.1 Screen

| Part | Spec |
|---|---|
| Background | `bg-primary` |
| Content margin | 16 (`screen-margin`) |
| Message list | Scrolls between the header and the composer, anchored to the bottom. Content scrolls *under* the composer's gradient fade. |

### 8.2 Header — `Header-full` instance

| Part | Spec |
|---|---|
| Height | 64 (plus status bar) · padding 8 / 16 · gap Space-M |
| Title | "AI Assistant" · Title/Large · `text-primary` · left-aligned, fills the space |
| Leading slot | Back `Pill-button` exists but is **hidden**. The assistant has no back button. |
| Trailing icons | Two `Nav-button`s, gap Space-M: **history** (clock with a counter-clockwise arrow) and **close** (×) |
| Nav-button | 48 × 48 · `bg-secondary` · `Radius-Full` · padding Space-L · 24px icon in `fg-primary` · `Shadow/Resting_cards` |
| Background | Intro frame: `bg-primary`. Chat frame: none (transparent). The prototype uses `bg-primary` on both, so messages scroll cleanly under it (open question §14 #2). |

### 8.3 Date badge — `Badge-primary` component instance

A design-system component. The values below are read from the instance in the chat frame; the component file overrides them when it arrives.

| Part | Spec |
|---|---|
| Surface | `bg-secondary` · `Radius-Full` · `Shadow/Resting_cards` |
| Padding | 2 / 8 (Space-XXS / Space-M) · gap Space-XS |
| Text | Label/Small · `text-secondary` · e.g. "Today 9:41 AM" |
| Position | Centered, at the start of each day's messages |

### 8.4 User message (bubble)

| Part | Spec |
|---|---|
| Alignment | Right; timestamp right-aligned under it |
| Width | Hugs its text, max 343 (§6.3) |
| Surface | `bg-brand` (#F47921, the same in both themes) |
| Text | Label/Medium · `text-inverse` (below AA, accepted, §3.3) |
| Padding | Space-L (12) vertical · Space-XL (16) horizontal |
| Radius | `Radius-L` on top-left, top-right and bottom-left · `Radius-XS` on **bottom-right** |
| Shadow | `Shadow/Resting_cards` |
| Timestamp | Caption/Small · `text-tertiary` · 4px below |

### 8.5 Assistant message (no bubble)

| Part | Spec |
|---|---|
| Alignment | Left; timestamp left-aligned under it |
| Surface | **None.** Text sits directly on `bg-primary`. No radius, no shadow. |
| Width | Fills the content width (343) |
| Text | Label/Medium · `text-tertiary` |
| Paragraphs | Each paragraph is its own block, gap Space-XL (16) |
| Lists | Bullets, 21px indent, same text style, no extra gap between items |
| Timestamp | Caption/Small · `text-tertiary` · 4px below |

There's no avatar or name on assistant messages.

### 8.6 Composer — `Chat field` instance

The component has variants **Size: Small | Large**, an optional **leading icon** button, and one state (**Enabled**). Both screens use **Small**.

| Part | Small (used) | Large (exists, unused) |
|---|---|---|
| Container | Full width 375 · padding 48 top (Space-6XL), 16 sides and bottom · gap Space-M | Same |
| Container background | Vertical gradient: `bg-primary` from 74% → `Alpha-shade` (transparent) at the top. Messages fade out under it. | Same |
| Field | Height 40 · padding 8 / 16 · `bg-secondary` · `Radius-Full` · `Shadow/Floating_nav, search` | Height 56 · padding 16 |
| Text / placeholder | Body/Large · `text-placeholder` · "Ask anything..." | Same |
| Trailing slot | Empty. The mic has been removed. | Same |
| Send button | 36 × 36 · `Radius-Full` · padding Space-M · 20px up-arrow icon | 56 × 56 · padding Space-XL · 24px icon |
| Send (disabled, the only designed state) | `bg-disabled` · icon `fg-disabled-on_bg` | Same |
| Leading button (optional, hidden) | 36px outline pill · 1px `border-action-primary` | 56px |

**Send button enabled state isn't designed.** Placeholder (placeholder): `bg-action-primary` + `fg-inverse`, pressed `bg-action-primary_pressed`.

### 8.7 Intro / empty state — frame `1:1124`

| Part | Spec |
|---|---|
| Layout | Block centered vertically and horizontally, width 343 · gap Space-XL between the mascot and the text |
| Mascot | 100 × 100 SVG illustration ("final"): orange robot with a violet face, waving. It's the assistant's only visual identity. Export it from Figma as-is; don't redraw it. |
| Greeting | "Hi Ruwini, how can I help?" · Headline/Medium-H5 · `text-primary` · centered |
| Subtitle | "Ask me anything about your accounts, spending, transfers or cards. You can type or talk." · Body/Medium · `text-secondary` · centered · gap Space-XS. "transfer" is corrected to "transfers" in the prototype. |
| Hidden button | A 92 × 40 `Pill-button` sits 24px below, hidden. Not built (open question §14 #3). |
| Header | Same as chat; no date badge |
| Composer | Small, no mic |

The intro frame is 820 tall and the chat frame is 812. Code uses 812.

---

## 9. What's designed vs not

| State / element | Status |
|---|---|
| Intro / empty state | ✅ Designed |
| User message, assistant text reply (paragraphs and bullets), timestamps, date badge | ✅ Designed |
| Composer, empty, with send disabled | ✅ Designed |
| Composer with text typed and send enabled | ❌ Placeholder in §8.6 |
| Assistant thinking / typing indicator | ❌ Placeholder in §7.2 |
| Card components | ⏳ To be provided. They'll be added as new script block types (§13.5). |
| Quick replies or suggestion chips | ❌ Not designed |
| Money-movement confirmation and step-up auth | ❌ Not designed (rules in §10) |
| Error, retry, offline | ❌ Not designed |
| History screen (header clock icon) | ❌ Not designed. The button does nothing. |
| Close (×) behaviour | ❌ Not designed. The prototype resets to the intro screen. |
| Dark mode frames | ❌ Not designed. Tokens support it, so the prototype has a theme toggle without a visual reference. |

The prototype builds the ✅ rows faithfully. ❌ rows get the clearly marked placeholders above, or are left out, until they're designed.

---

## 10. Banking guardrails

These apply to every script, including ones that need UI that isn't designed yet.

1. **Money movement = explicit confirm + step-up.** Transfers, bill payments, card freezes and limit changes must end in a confirmation and authentication step. Nothing is "done" before both. The UI for this isn't designed yet, so scripts that move money wait for it.
2. **Formats, as used in the frames:**
   - Amounts: `Rs 200,000.00`, with the "Rs" prefix, comma grouping and 2 decimals.
   - Dates: `27 Jan 2026`.
   - Times: `9:41 AM`.
   - Day badge: `Today 9:41 AM`.
3. **Reference numbers** (e.g. "FD #FD00123456") are shown in full for now. Masking is deferred.
4. **No secrets in chat.** The assistant never asks for a PIN, password or code in a message.
5. **Disclaimer.** None in the intro frame. Copy and placement are TBD with legal.

---

## 11. Accessibility

- **Target:** WCAG 2.1 AA. The user bubble (white on `bg-brand`, 2.76:1) is a known, accepted exception (§3.3).
- **Touch targets:** the header buttons (48) pass. The send button (36) has an invisible 44 × 44 hit area in code, without changing its look.
- **Screen readers:**
  - The message list is `role="log"` with `aria-live="polite"`, and a reply is announced when it's complete.
  - Each message is prefixed for screen readers with "You said" or "Assistant said". There's no visible name, so this is the only cue that isn't colour or position.
  - Icon buttons have labels: "Conversation history", "Close assistant", "Send".
- **Focus:** buttons show a 2px `border-focused` ring on keyboard focus. Focus returns to the composer after each reply.
- **Text scaling:** messages wrap; nothing is fixed-height except the controls.
- **Reduced motion** is handled as in §7.2.

---

## 12. Assistant voice

Based on the copy in the frames:

- Greets by first name: "Hi Ruwini, how can I help?"
- Leads with the answer, then gives detail as short paragraphs and a bulleted list: "Here's the history for FD #FD00123456, which is your earliest-maturing fixed deposit."
- Ends with a relevant follow-up offer as a question.
- Plain, brief, bank-grade: no emoji. 1 to 3 short paragraphs per reply.

Copy typos in the frames are corrected in the prototype's script: "I can also provide…", and "transfers".

---

## 13. Building the prototype

### 13.1 Format

- **One self-contained HTML file:** markup, CSS, JS, tokens, SVGs and conversation scripts all inline. No framework, no runtime dependencies, opens straight from disk.
- **Only external request:** Hanken Grotesk from Google Fonts (weights 400, 500, 600, 700, 800). Without a connection it falls back to `system-ui`.
- **Desktop:** the app renders in a 375 × 812 phone frame, centred, with a demo panel beside it (§13.6).
- **Phone (≤ 760px wide):**
  - The app is full-bleed (`100dvh`), and the demo panel is hidden.
  - The fake status bar is hidden; use `env(safe-area-inset-top)` instead.
  - The composer adds `env(safe-area-inset-bottom)`.
  - Use `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">`.

### 13.2 Suggested repo layout

```
/
├─ DESIGN.md                         this guide
├─ design-tokens/
│  └─ figma-export/                  the 6 Figma variable exports (*.tokens.json), committed as-is
├─ assets/                           SVGs exported from Figma (§13.4)
├─ scripts/
│  ├─ build-tokens.(js|py)           export → token CSS (§13.3)
│  └─ build.(js|py)                  template + tokens + SVGs + scripts.json → dist/index.html
├─ src/
│  ├─ index.template.html
│  └─ scripts.json                   conversation scripts (§13.5)
└─ dist/
   └─ index.html                     the single-file prototype (the deliverable)
```

A small build step that inlines the pieces keeps tokens generated rather than hand-typed, while still shipping one file.

### 13.3 Tokens: generate from the Figma export

Each `*.tokens.json` file (W3C design-token format, as exported by Figma) holds resolved values. Colours are in `$value.hex` and `$value.alpha`; aliases are in `$extensions["com.figma.aliasData"]`. Generate CSS custom properties with these rules:

| Source | Output | Rule |
|---|---|---|
| `Color collection/Light` | `:root { … }` | Drop the group (`Background/`, `Text/`, `Border/`, `Forground/`), lower-case, `_` → `-`. Example: `bg-action-primary_pressed` → `--bg-action-primary-pressed` |
| `Color collection/Dark` | `:root[data-theme="dark"] { … }` | Same names; Dark values only |
| `Alpha/*` | `--alpha-*` | `Alpha-nav` → `--alpha-nav`. Alpha < 1 is output as `rgba()`. |
| `Alias Spacing & Radius` | `--space-*`, `--radius-*` | `Space-XL` → `--space-xl`. `Radius-Full` (999999) → `9999px`. |
| `Component collection` | `--screen-margin`, `--card-padding`, … | Last path segment; fix the typo `screeen` → `screen` |
| `Alias Typography/Mobile` | `--font-<style>` | One `font` shorthand per style: `--font-label-m: 600 14px/21px 'Hanken Grotesk', system-ui, sans-serif` |

Primitives and Alias Colors don't need to be output; the semantic layer is already resolved. Components use semantic tokens only (§2).

Also set these globally:

```css
body { font-feature-settings: "case" 1; -webkit-font-smoothing: antialiased; }
```

Every text layer in the frames uses the `"case"` feature.

### 13.4 Assets: export from Figma

Export these nodes as SVG from the chat file (`p41jFOs3yRsffVgcs3ze1x`) and inline them. Don't redraw them.

| Asset | Node ID | Size | Notes |
|---|---|---|---|
| History icon | `I1:1121;95:2958;95:1569;1:1120;91:844;91:822` | 24 | Swap `stroke="#141D27"` → `currentColor`; colour it with `fg-primary` |
| Close icon | `I1:1121;95:2958;95:1569;1:1120;91:845;91:822` | 24 | Same as History |
| Send arrow | `I1:1123;776:4628;47:1850;37:913` | 20 | Swap `stroke="#93979C"` → `currentColor`; colour comes from the send-button state |
| Mascot | `1:1131` | 100 × 100 | Use as-is, e.g. as an `<img>` with an SVG data URI. Don't recolour it. |

The Figma MCP's temporary asset URLs may be blocked or expire. Exporting through the plugin API (`node.exportAsync({ format: "SVG_STRING" })`) or manually from Figma both work.

The iOS status bar is a system element. Draw a simple generic one (time "9:41", signal, wifi, battery) in `text-primary`; don't export it.

### 13.5 Conversation engine

Scripts are JSON in `<script type="application/json" id="scripts">`, so they can be edited without touching code.

```json
{
  "scenarios": [{
    "id": "fd-history",
    "title": "Fixed deposit history",
    "customer": { "firstName": "Ruwini" },
    "greeting": "Hi {firstName}, how can I help?",
    "start": "intro",
    "fallback": "fallback",
    "routes": [ { "any": ["fixed deposit", "fd"], "next": "fd-answer" } ],
    "steps": {
      "intro": {
        "suggestedInput": "Show me the transaction history for my fixed deposit",
        "expect": { "match": [ { "any": ["fixed deposit", "fd"], "next": "fd-answer" } ] }
      },
      "fd-answer": {
        "thinkingMs": 1400,
        "assistant": [
          { "p": "Here's the history for FD #FD00123456, which is your earliest-maturing fixed deposit." },
          { "p": "A deposit placement of Rs 200,000.00, followed by monthly interest payments of Rs 2,875.00 credited to your FD on 27 Jan and 27 Feb 2026." },
          { "ul": ["Maturity date: 10 Oct 2026", "FD type: Sampath Fixed Deposit", "Closure date: 12 Jan 2027"] },
          { "p": "I can also provide a detailed historical breakdown of your other active FDs. Would you like to view a detailed history of them as well?" }
        ],
        "suggestedInput": "Yes, please",
        "expect": {
          "match": [
            { "any": ["yes", "sure", "ok", "please"], "next": "other-fds" },
            { "any": ["no", "not now"], "next": "close-out" }
          ]
        }
      },
      "fallback": {
        "assistant": [
          { "p": "Sorry, I can't help with that in this demo yet." },
          { "p": "Try asking about the transaction history for your fixed deposit." }
        ]
      }
    }
  }]
}
```

**Behaviour:**

1. **Intro.** Show the intro screen (§8.7) with `greeting`. `{field}` placeholders are filled from `customer`.
2. **First send:**
   - Switch to the chat screen.
   - Insert the date badge, "Today h:mm AM", using the real current time.
   - Then show the user message.
3. **Matching.** The text is checked against the current step's `expect.match` keywords, then the scenario `routes`, then `fallback`. Matching is case-insensitive on whole words and phrases, so "no" doesn't match "know".
4. **Timing.** After about 350ms, show the thinking placeholder (§7.2) for `thinkingMs` (default 1000). Then replace it with the reply blocks and a timestamp.
5. **Blocks:**
   - `p` renders a paragraph and `ul` a bullet list (§8.5).
   - With optional `stream: true`, the reply reveals word by word at `streamMs` per word (default 30).
   - Card blocks will be added when the card components arrive.
6. **While a reply is pending,** Send is disabled. Afterwards, focus returns to the input.
7. **Timestamps** use the real current time in the format `9:41 AM`.
8. **Copy.** Write script copy to §10 and §12. Fix the frame typos in scripts ("I can also provide…", "transfers").

### 13.6 Controls

| Control | Behaviour |
|---|---|
| Send button | Disabled (Figma state) when the input is empty or a reply is pending; enabled placeholder otherwise (§8.6). Enter submits. |
| History (header) | Not designed. Does nothing. |
| Close × (header) | Not designed. Resets the current scenario to the intro screen. |
| Demo panel (desktop only, not part of the design) | Scenario picker · **Fill next message** (puts the current step's `suggestedInput` in the composer) · Restart · Light / Dark toggle (sets `data-theme` on `<html>`) · a short list of the placeholders in use. Style it with the same tokens so it sits comfortably beside the phone. |

### 13.7 Acceptance checks

Before calling the build done:

- [ ] Light-mode renders of the intro and of the chat after the FD exchange match Figma frames `1:1124` and `1:1104`: positions, sizes, type, colours and icons.
- [ ] The header is 44 + 64 tall; the badge is 16 below the header; the first message is 24 below the badge; turns are 16 apart; timestamps are 4 below.
- [ ] The user bubble has three `Radius-L` corners and a `Radius-XS` bottom-right corner, with `Shadow/Resting_cards`.
- [ ] Assistant text has no surface, radius or shadow, and fills the width.
- [ ] Bullets render as the font's square glyph, not a round CSS disc (§4).
- [ ] The composer gradient fades messages out beneath it; the field has `Shadow/Floating_nav, search`.
- [ ] No hard-coded colours outside the generated token block, the two shadows (§7.1) and the mascot SVG.
- [ ] Dark mode switches every surface through tokens alone.
- [ ] Works at 375 × 812 and full-bleed on a real phone, with no horizontal scroll.
- [ ] Keyboard: Tab reaches all buttons with a visible focus ring; Enter sends.
- [ ] `prefers-reduced-motion` turns off the fade and the dot animation.

---

## 14. Open questions

None of these block the build. Use the default listed until each is decided.

| # | Item | Default for the build |
|---|---|---|
| 1 | Should the user bubble hug short messages? In the frame it fills 343, but the sample text is long. | Hug, right-aligned, max 343 |
| 2 | The header has a fill on intro and none on chat. | `bg-primary` on both |
| 3 | There's a hidden 92 × 40 pill button under the intro text. | Don't build it |
| 4 | The intro says "You can type or talk", but the mic has been removed. | Keep the copy as designed |
| 5 | Assistant text uses `text-tertiary` (#434A52), which is darker than `text-secondary`. | Follow Figma |
| 6 | Shadow colours aren't tokens and are identical in dark mode. | Same shadows in both themes |
| 7 | Send-enabled, thinking, history, error and dark-mode screens aren't designed (§9). | Use the placeholders noted |
| 8 | Variable audit: `Space-6XL` = `Space-7XL` = 48; `Alpha-brand` uses `#F26722`, which isn't a primitive; `text-dark_both` and `text-inverse` have the same usage note; buttons, links and warning text fail contrast (§3.3). | n/a. Not used in the chat yet. |
| 9 | Disclaimer copy and placement (legal). | None |
| 10 | The design-system component file (header, badge, chat field) hasn't been shared yet. | Build from §8; the component file overrides it when it arrives |

---

## Changelog

| Version | Date | Change |
|---|---|---|
| 0.4 | 2026-10-07 | Rewritten as a build guide for the HTML prototype. §13 now covers token generation rules, asset node IDs, engine behaviour, controls and acceptance checks. Removed the tags legend. Noted that the header, date badge and chat field are design-system components (component file to come). |
| 0.3 | 2026-10-07 | Applied the Figma fixes: assistant message fills the width, no radius or shadow; mic removed. Accepted the user-bubble contrast. Reference numbers stay unmasked for now. |
| 0.2 | 2026-10-07 | §8 rebuilt from the chat frames. Replaced the made-up shadows with Figma effect styles. |
| 0.1 | 2026-10-07 | First draft from the Figma variable export. |
