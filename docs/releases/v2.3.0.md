# Release Notes v2.3.0 - General Cleanup & Branding

**Status:** Pending Release
**Branch:** `38-generalcleanup`
**Milestone:** General CleanUp & Visual Polish

## 1. Executive Summary
Release v2.3.0 focuses on stabilizing the codebase through a comprehensive cleanup ("The Purge"), finalizing the Single Source of Truth (SSOT) architecture for assets, and applying a cohesive "HekTek City" cyberpunk branding across all UI components. This release also introduces custom animated SVG icons to replace generic libraries.

## 2. Key Features

### A. Branding Overhaul
*   **Loading Screen:** Redesigned `LoadingModal` with glassmorphism, neon effects, and the official `LogoIcon` SVG.
# Release Notes v2.3.0 - General Cleanup & Branding

**Status:** Pending Release
**Branch:** `38-generalcleanup`
**Milestone:** General CleanUp & Visual Polish

## 1. Executive Summary
Release v2.3.0 focuses on stabilizing the codebase through a comprehensive cleanup ("The Purge"), finalizing the Single Source of Truth (SSOT) architecture for assets, and applying a cohesive "HekTek City" cyberpunk branding across all UI components. This release also introduces custom animated SVG icons to replace generic libraries.

## 2. Key Features

### A. Branding Overhaul
*   **Loading Screen:** Redesigned `LoadingModal` with glassmorphism, neon effects, and the official `LogoIcon` SVG.
*   **Viewers:** Applied consistent cyberpunk styling (Neon Red/Blue palette, Michroma font) to:
    *   `ExperienceViewer.jsx`
    *   `SkillsViewer.jsx`
    *   `VisionViewer.jsx`
    *   `DocsViewer.jsx`
*   **Creative Icons:** Replaced Ant Design icons with 9 custom animated SVGs (`CreativeIcons.jsx`) representing key domains (AI, DevOps, Agile, etc.).
*   **Branded Close Button:** Replaced the generic close button with a custom animated `CloseIcon` (rotating neon ring) in `CreativeIcons.jsx`, integrated into the global `ViewerLayout`.
*   **BrandBanner:** Implemented a standardized `BrandBanner` component for consistent headers across all viewers.
*   **NavigationInfoPanel:** Restored and styled the global navigation panel with glassmorphism and animations.
*   **CyberpunkBackground:** Created a shared background component to ensure visual consistency across all routes.
*   **ViewerLayout:** Centralized layout component handling full-screen positioning, scrolling, and consistent branding for all viewers.

### B. SSOT Architecture Finalization
*   **Asset Management:** Refactored `assetsHelper.js` to support suffix-based quality loading (e.g., `_4k.jpg`, `_2k.glb`) instead of folder-based switching.
*   **Configuration:** Consolidated theme and building logic into JSON configurations, removing hardcoded arrays in components.

### C. Visual Improvements
*   **Glassmorphism:** Standardized container styles with semi-transparent backgrounds, neon borders, and shadows across all UI components.
*   **Terrain LOD:** Forced 8K resolution for standard quality users on desktop.
*   **HDRI Background:** Added rotation support to `HDRIBackground.jsx` for better scene composition.
*   **DocsViewer:** Added a creative circuit-board background pattern.

## 3. Code Cleanup ("The Purge")
Removed "zombie" code and obsolete strategies to reduce technical debt:
*   **Deleted Components:** `MapLake.jsx`, `SkillsViewer_OLD.jsx`, `DecorativeGroup.jsx`, `DecorativePositions.js`, `MapRPG_Integration_Example.jsx`.
*   **Refactoring:** Removed legacy `DECO_*` maps and `THEME_CONFIGS` from `assetsHelper.js`.
*   **Standardization:** Translated code comments to English and standardized file headers.

## 4. Bug Fixes
*   **3D Canvas Layout:** Fixed full-screen display issues by applying `overflow: hidden` to the body and using fixed positioning for the canvas.
*   **Viewer Scrolling:** Fixed off-center content and global scrollbar issues by implementing `ViewerLayout` with internal scrolling.
*   **Blog Scrolling:** Enabled vertical scrolling for the `/blog` route (`BlogScene.jsx`) while maintaining the global no-scroll policy for the map.
*   **Blog Imports:** Fixed missing React and Ant Design imports in `BlogScene.jsx`.
*   **SkillsViewer:** Restored missing content and ensured correct rendering of the skills matrix.
*   **LoadingModal:** Fixed prop mismatch (`visible` vs `isLoading`).
*   **ExperienceViewer:** Fixed crash due to missing list rendering logic.
*   **HDRIBackground:** Fixed performance issue by removing complex offset logic.

## 5. Migration Guide
*   **Assets:** Ensure all terrain textures follow the `_2k`, `_4k` suffix convention.
*   **Icons:** Use `CreativeIcons.jsx` for any new domain-specific visuals.
*   **Viewers:** All new viewer components must be wrapped in `<ViewerLayout>` and accept an `onClose` prop.
