# Asset & Image Paths Audit

This document lists all image and asset references identified in the codebase/documentation. Use this list to verify and update paths.

## 1. Local Assets (`docs/assets/`)
Physical files located in `docs/assets` that are currently tracked in the repository.

| Filename | Size | Usage Context (Files Referencing It) |
|----------|------|--------------------------------------|
| `BANER-docs-FINAL.png` | 7.18 MB | `README.md`, `docs/README.md` |
| `cockpit-console-FINAL.png` | 6.11 MB | `README.md`, `docs/README.md` |
| `logo.svg` | 16 KB | `README.md`, `docs/README.md`, `docs/README_Preliza.md` |
| `banner-mockup.png` | 222 KB | `docs/README_Preliza.md` |
| `cockpit-console.png` | 548 KB | *(Unused? Check if replaced by FINAL)* |
| `liza_concept.png` | 723 KB | *(Check usage)* |
| `version-banner.png` | 933 KB | *(Check usage)* |
| `experience-building.png` | 1.2 MB | *(Check usage)* |
| `skills-building.png` | 3.2 MB | *(Check usage)* |
| `theme-showcase.png` | 1.7 MB | *(Check usage)* |
| `map-overview.png` | 2.0 MB | *(Check usage)* |
| `vision-building.png` | 3.1 MB | *(Check usage)* |

## 2. R2 / Cloudflare Asset References
External assets hosted on `assets.hectortechno.com`. Most of these appear in code examples or walkthroughs.

**Base URL:** `https://assets.hectortechno.com/`

| Asset Path / Full URL | Context / File | Notes |
|-----------------------|----------------|-------|
| **Models** | | |
| `.../models/quality/standard/robotHT.glb` | `ASSETVIEWER_WALKTHROUGH.md`, `ASSETVIEWER_README.md`, `ASSETVIEWER_GIT_DELIVERABLES.md` | Common example model |
| `.../models/quality/standard/building.glb` | `ASSETVIEWER_WALKTHROUGH.md` | Example code |
| **Images** | | |
| `.../img/show/release/liza/img1.png` | `ASSETVIEWER_WALKTHROUGH.md` | Example set |
| `.../img/show/release/liza/img2.png` | `ASSETVIEWER_WALKTHROUGH.md` | Example set |
| `.../img/show/release/liza/banner.png` | `ASSETVIEWER_WALKTHROUGH.md`, `ASSETVIEWER_README.md` | Example single image |
| `.../img/show/release/liza/lizaControl1.png` | `ASSETVIEWER_WALKTHROUGH.md` | Tree structure example |
| `.../img/show/release/liza/liza-avatar.png` | `ASSETVIEWER_WALKTHROUGH.md` | Tree structure example |
| `.../img/show/release/liza/Liza-banneer.png` | `ASSETVIEWER_WALKTHROUGH.md` | Tree structure example |

## 3. Legacy / Third Party CDNs
External hosts that are not part of the primary R2 bucket `assets.hectortechno.com`.

| URL / Domain | Context / File | Notes |
|--------------|----------------|-------|
| `https://hector-assets.b-cdn.net/img/post-covers/vision-banner.png` | `docs/blog/001-vision-origins.md` | **Action Item**: Verify if this should be replaced by local `vision-banner.png` |

## 4. External & Badge Links
Paths to public internet resources (Documentation, Badges, Profiles).

**Shields.io Badges (used in READMEs):**
- `https://img.shields.io/badge/🚀_Release-v6.5.0...`
- `https://img.shields.io/badge/🌐_Live-Demo-00F0FF...`
- `https://img.shields.io/badge/📚_Technical_Deep_Dive...`
- `https://img.shields.io/badge/⚖️_License-MIT-white...`
- `https://img.shields.io/badge/-React_19...`
- And various other tech stack badges (Three.js, Gemini, Vercel, etc.)

**External Links:**
- `https://hektek-city.vercel.app/` (Live Demo)
- `https://www.hectortechno.com` (Production URL mentioned in Incident Reports)
- `https://github.com/hmosqueraturner` (GitHub Profile)
- `https://linkedin.com/in/hmosqueraturner` (LinkedIn Profile)
- Docs/Help links: `threejs.org`, `docs.blender.org`, `vercel.com`, `dash.cloudflare.com`

## 5. Potential Issues & Updates Needed

1.  **"FINAL" vs Standard Naming:**
    - Usage of `BANER-docs-FINAL.png` and `cockpit-console-FINAL.png` suggests temporary naming conventions making their way into main docs. Consider renaming to cleaner standard names (e.g., `main-banner.png`, `cockpit-ui.png`) if "updating correctly".

2.  **Unused Assets in `docs/assets/`:**
    - Several large PNGs (`skills-building.png`, `vision-building.png`, etc.) exist in the folder but weren't immediately flagged in the grep of the main READMEs. Need to verify if they are linked in other sub-documentation or if they are orphans consuming space.

3.  **Inconsistent R2 Paths in Documentation:**
    - Some docs reference `release/liza/banner.png` and others `release/liza/Liza-banneer.png` (note the double 'e'). This typo in the URL/filename should be checked.

