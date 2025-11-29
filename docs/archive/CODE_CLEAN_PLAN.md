# Code Cleanup Plan - General CleanUp Milestone

## 1. Executive Summary

This document outlines the strategy for the "General CleanUp" milestone. The goal is to remove technical debt accumulated during the rapid evolution of the project, specifically targeting "zombie" code, obsolete asset strategies, and mixed language documentation.

**Target Branch:** `38-generalcleanup`
**Objective:** Clean, efficient, and English-only codebase aligned with the v2.2.0 SSOT architecture.

## 2. Analysis of Current State

### A. Zombie Code & Unused Files
The following files have been identified as obsolete or "zombie" code (not imported or used in the active application flow):

| File Path | Status | Reason | Recommendation |
| :--- | :--- | :--- | :--- |
| `src/components/MapLake.jsx` | **Zombie** | Replaced by `MapRPG.jsx`. Contains old hardcoded logic. | **DELETE** |
| `src/components/SkillsViewer_OLD.jsx` | **Zombie** | Legacy version of `SkillsViewer.jsx`. | **DELETE** |
| `src/components/DecorativeGroup.jsx` | **Obsolete** | Part of the deprecated "Decorative Strategy". | **DELETE** |
| `src/components/DecorativePositions.js` | **Obsolete** | Hardcoded positions for deprecated decoratives. | **DELETE** |
| `src/components/MapRPG_Integration_Example.jsx` | **Zombie** | Example file no longer needed. | **DELETE** |
| `src/components/HoloReactor.jsx` | **Unused** | Not referenced in active `MapRPG` or config. | **DELETE** (Verify usage first) |

### B. Obsolete Strategies

#### 1. Decorative Strategy
**Current State:** The project previously used a complex system (`DecorativeGroup.jsx`, `assetsHelper.js` maps) to load different decorative models per theme.
**New Architecture:** Decoratives are now defined in `decoratives-config.json` (SSOT) and rendered dynamically via `MapRPG` loop, similar to buildings.
**Action:** Remove all "Decorative" specific components and logic from `assetsHelper.js`.

#### 2. Assets Helper (`src/utils/assetsHelper.js`)
**Current State:** Contains hardcoded `THEME_CONFIGS`, `DECO_*` maps, and complex path logic that tries to inject theme folders for models.
**New Architecture:**
*   Models are unified in `models/quality/standard/` and `models/quality/low-res/`.
*   Themes are applied via *Runtime Materials* (JSON), not by loading different GLB files from different folders.
*   `THEME_CONFIGS` are now in `src/config/theme-config.json`.
**Action:** Refactor `assetsHelper.js` to:
*   Remove `THEME_CONFIGS`, `DECO_*` maps.
*   Simplify `getAssetUrl` to point only to the unified model paths.
*   Remove `theme` parameter from model path construction (except for HDRs if still needed).

### C. Mixed Language & Documentation
**Current State:** Several files contain Spanish comments or mixed language documentation.
**Standard:** English is the official language for code comments and documentation.
**Exceptions:** `index.html` or specific SEO content for the "flat portfolio" fallback may remain bilingual if intended for that audience, but code comments must be English.

**Identified Targets:**
*   `src/hooks/useDynamicMaterials.js` (Spanish comments)
*   `src/components/ExperienceViewer.jsx` (Spanish comments)
*   `src/components/MapControlsPanel.jsx` (Spanish comments)
*   `src/components/MapLake.jsx` (Spanish comments - but will be deleted)
*   `src/utils/assetsHelper.js` (Spanish comments)

## 3. Action Plan

### Phase 1: Deletion (The Purge)
1.  [x] Delete `src/components/MapLake.jsx`.
2.  [x] Delete `src/components/SkillsViewer_OLD.jsx`.
3.  [x] Delete `src/components/DecorativeGroup.jsx`.
4.  [x] Delete `src/components/DecorativePositions.js`.
5.  [x] Delete `src/components/MapRPG_Integration_Example.jsx`.
6.  [ ] Delete `src/components/HoloReactor.jsx` (KEPT by user request).

### Phase 2: Refactoring `assetsHelper.js`
1.  [x] Remove `DECO_ORIGINAL`, `DECO_SCIFI`, etc.
2.  [x] Remove `THEME_CONFIGS`.
3.  [x] Simplify `getAssetUrl` logic to ignore `theme` for models (models are geometry-only now).
4.  [x] Translate all comments to English.

### Phase 3: Language Standardization
1.  [x] Scan `src/` for `//` comments containing non-English text.
2.  [x] Translate comments in `useDynamicMaterials.js`, `MapControlsPanel.jsx`, etc.
3.  [x] Ensure `README.md` and `docs/` are purely English.

### Phase 4: Verification
1.  [x] Run the application (`npm run dev`) to ensure no broken imports from deleted files.
2.  [x] Verify `MapRPG` still loads buildings and decoratives correctly using the simplified `assetsHelper`.

## 4. Best Practices for Future
1.  **Deprecation Policy:** When replacing a component (e.g., `MapLake` -> `MapRPG`), immediately delete the old one or move it to a `_archive` folder if reference is needed, but do not leave it in `src/components`.
2.  **SSOT Adherence:** Never hardcode config data (like decorative positions or theme colors) in JS files. Always use the JSON configs.
3.  **Language:** All commits, PRs, and code comments must be in English.

## 5. Deliverables
*   Cleaned `src/` directory.
*   Refactored `assetsHelper.js`.
*   `docs/CODE_CLEAN_PLAN.md` (This document).
