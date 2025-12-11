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

## 🔤 Typography

### Primary Font: **Michroma**
Used for headings, branding, and UI elements that need a futuristic, machine-like feel.

> "The quick brown fox jumps over the lazy dog."

### Secondary Font: **Sans-Serif** (System Default / Inter)
Used for body text, descriptions, and long-form content to ensure readability.

## 🖼️ Iconography: CreativeIcons

We do not use standard generic icons or a `public/icons` folder for UI elements. We use **CreativeIcons**, a custom React component system that renders SVG paths with dynamic gradient fills matching our `Red/Cyan` palette.

### Key Icons
*   **RocketLaunchIcon**: Used for Tour activation.
*   **LizaAwakeningIcon**: The neural-network representation of LIZA.
*   **OrbitIcon**: Camera reset and navigation.
*   **DevOpsInfiniteIcon**: Represents CI/CD and DevOps skills.

*(See `src/components/CreativeIcons.jsx` for the SVG paths)*

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
