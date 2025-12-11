# 💎 UI System: Cyberpunk Glass

**Version:** 3.1.0
**Status:** Active

The **Cyberpunk Glass** UI system is a unified design language introduced to provide a consistent, immersive experience across HEKTEK City. It combines glassmorphism, neon aesthetics, and animated SVG icons.

## Core Components

### 1. `ViewerLayout`
The backbone of all content views (Skills, Experience, Vision, Blog).
-   **Backdrop Blur:** Uses `backdrop-filter: blur(8px)` to obscure the 3D scene, ensuring text readability without losing context.
-   **Auto-Scroll:** Automatically resets scroll position to the top when opened or when Tour mode changes.
-   **Responsive:** Adapts padding and layout for mobile and desktop.

### 2. `CyberpunkBackground`
A shared background component used in `ViewerLayout` and `BlogScene`.
-   **Visuals:** Animated grid lines and floating particles.
-   **Customization:** Accepts `opacity` and color props to blend with different contexts.

### 3. `CreativeIcons`
A custom SVG library replacing generic icon sets.
-   **Animation:** All icons feature subtle SVG animations (pulse, rotate, flow).
-   **Theming:** Props for `color` (Neon Blue) and `secondaryColor` (Neon Red).
-   **Key Icons:** `AiBrainIcon`, `CloudPipelineIcon`, `DevOpsInfiniteIcon`, `RocketLaunchIcon`, etc.

### 4. `BuildingLabel`
Interactive 3D labels floating above buildings.
-   **Focus State:** Scales up (1.5x) and glows brighter when the building is selected.
-   **Dimming:** Non-selected labels fade to 0.2 opacity to reduce clutter.

### 5. `CockpitConsole`
The centralized operation hub for the entire experience.
-   **Unified Control:** Consolidates Tour controls, Theme selection, and Navigation into a single, reactive bar.
-   **Reactive Design:** Uses `fit-content` to dynamically adjust its width based on active elements.
-   **Hero Action:** A context-aware button that adapts to the current state (Start/Stop Tour, Enter/Exit Building).
-   **Exposed Settings:** Direct access to visual themes without hidden menus.

## Usage Guidelines

### Colors
-   **Neon Blue:** `#00F0FF` (Primary accents, borders, text)
-   **Neon Red:** `#BB1111` (Secondary accents, warnings, active states)
-   **Glass Dark:** `rgba(11, 12, 16, 0.6)` (Card backgrounds)

### Typography
-   **Headers:** `Michroma` (Uppercase, wide spacing)
-   **Body:** `Roboto Mono` or `Inter` (Readable, tech-focused)

## Implementation Example

```jsx
import ViewerLayout from "./ViewerLayout";
import { AiBrainIcon } from "./CreativeIcons";

export default function MyFeature() {
  return (
    <ViewerLayout onClose={handleClose}>
      <div className="glass-card">
        <AiBrainIcon size={60} />
        <h1>Feature Title</h1>
      </div>
    </ViewerLayout>
  );
}
```
