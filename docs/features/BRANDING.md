# 🎨 HEKTEK City Branding Guide

This document outlines the visual identity of **HEKTEK City**, a cyberpunk-inspired 3D portfolio.

## 🌌 Core Philosophy
**"High Tech, High Life"**
The visual language combines the raw, industrial energy of cyberpunk with the clean, precise aesthetics of modern web design. It is bold, immersive, and unapologetically digital.

## 🎨 Color Palette

The color scheme is high-contrast, designed for dark mode environments.

### Primary Colors

| Color Name | Hex Code | Usage | Preview |
|------------|----------|-------|---------|
| **HekTek Red** | `#BB1111` | Primary Actions, Alerts, "HekTek" Brand | ![#BB1111](https://placehold.co/15x15/BB1111/BB1111.png) |
| **Cyber Cyan** | `#00F0FF` | Accents, Tech Elements, "City" Brand | ![#00F0FF](https://placehold.co/15x15/00F0FF/00F0FF.png) |
| **Void Black** | `#000000` | Backgrounds, Deep Contrast | ![#000000](https://placehold.co/15x15/000000/000000.png) |

### Usage Rules
-   **Backgrounds** should be predominantly black or very dark grey.
-   **Text** should be white or light grey for readability.
-   **Accents** (borders, glows, active states) use **Cyber Cyan**.
-   **Primary Buttons** and **Critical UI** elements use **HekTek Red**.

## 🔤 Typography

### Primary Font: **Michroma**
Used for headings, branding, and UI elements that need a futuristic, machine-like feel.

> "The quick brown fox jumps over the lazy dog."

*Usage:*
-   Main Headings (`h1`, `h2`)
-   Button Labels
-   HUD Elements

### Secondary Font: **Sans-Serif** (System Default / Inter)
Used for body text, descriptions, and long-form content to ensure readability.

## 🖼️ Logo & Iconography

### The Icon
The project uses a stylized representation of a city block or a digital node.
*(Ensure the `public/icons` folder contains the latest SVG/PNG versions)*

### UI Components
-   **Glassmorphism**: Panels often use a semi-transparent black background with a blur effect (`backdrop-filter: blur(10px)`).
-   **Borders**: Thin, 1px borders in Cyan or Red are common.
-   **Glows**: `box-shadow` with Cyan/Red is used to indicate active or focused states.

## 💻 CSS Variables (Tailwind)

```javascript
// tailwind.config.js
colors: {
  brand: {
    red: '#BB1111',
    cyan: '#00F0FF',
    dark: '#000000'
  }
},
fontFamily: {
  brand: ['Michroma', 'sans-serif'],
}
```
