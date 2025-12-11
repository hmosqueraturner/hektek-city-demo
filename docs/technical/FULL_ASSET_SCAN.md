# Exhaustive Asset & Link Scan Report

**Generated on:** 2025-12-11
**Scope:** All `.md` files in `d:\code\portfolio\hektek-city-demo`.
**Method:** Regex scan for Markdown links/images and HTML src/href attributes.

## 🚨 CRITICAL ISSUES (Must Fix)

These paths are **broken** for any other user or in production (absolute local paths to your temp directories).

| File | Line | Invalid Path Found |
|------|------|--------------------|
| `docs/features/ASSETVIEWER_ANALYSIS.md` | 142 | `C:/Users/hector/.gemini/antigravity/brain/b31bb821.../assetviewer_mockup_1764676295668.png` |
| `docs/features/ASSETVIEWER_ANALYSIS.md` | 153 | `C:/Users/hector/.gemini/antigravity/brain/b31bb821.../carousel_mockup_1764676312911.png` |
| `docs/features/ASSETVIEWER_ANALYSIS.md` | 164 | `C:/Users/hector/.gemini/antigravity/brain/b31bb821.../single_image_mockup_1764676327791.png` |
| `docs/features/Liza/LIZA_FIX_WALKTHROUGH.md` | 146 | `C:/Users/hector/.gemini/antigravity/brain/b31bb821.../uploaded_image_0_1764669972001.png` |
| `docs/features/Liza/LIZA_FIX_WALKTHROUGH.md` | 153 | `C:/Users/hector/.gemini/antigravity/brain/b31bb821.../uploaded_image_1_1764669972001.png` |
| `docs/features/Liza/LIZA_IMAGE_ASSETS_GUIDE.md` | 107 | `C:/Users/hector/.gemini/antigravity/brain/b31bb821.../liza_banner_wide_1764688515225.png` |
| `docs/archive/technical/json-r2-impl-plan.md` | 43, 54, 58, 62 | `file:///d:/code/portfolio/hektek-city/src/...` |
| `docs/technical/incident_reports/LIZA_REDIRECT_LOOP_FIX_PLAN.md` | 32, 120 | `file:///d:/code/portfolio/hektek-city/...` |

## ⚠️ External Asset Dependencies (CDNs)

Assets hosted outside of the repo or main R2 bucket.

| File | Line | External Link | Notes |
|------|------|---------------|-------|
| `docs/blog/001-vision-origins.md` | 7 | `https://hector-assets.b-cdn.net/img/post-covers/vision-banner.png` | **BunnyCDN** (Should be local `visino-banner.png`?) |
| `docs/features/BRANDING.md` | 17-19 | `https://placehold.co/15x15/...` | Placeholder service |
| All READMEs | Various | `https://img.shields.io/...` | Status Badges (Standard) |

## 📁 Local Asset Usage

Valid relative links to `docs/assets/`.

- **Banner Images**: `BANER-docs-FINAL.png`, `banner-mockup.png` (Used in `README.md`, `README_PREliza.md`)
- **UI Screenshots**: `cockpit-console-FINAL.png`, `map-overview.png`, `skills-building.png` (Used in `README.md`, `Old-README.md`)
- **Icons**: `logo.svg`
- **Concept Art**: `liza_concept.png`, `liza-chat-ui.png`

## 🔗 Internal Link Consitency

- Most internal links use relative paths `../features/...` or `./file.md`.
- **Note**: `docs/README_INT.md` contains many redundant links to `guides/` and `pipeline/` as it seems to be an intermediate index.

## 🛠️ Action Plan

1.  **Fix Absolute Paths**: Move the 6 images from `C:/Users/hector/.gemini/...` into `docs/assets/screenshots/` and update paths to relative.
2.  **Fix `file:///` Links**: Remove these absolute references or convert to relative paths in the implementation plans.
3.  **Localize BunnyCDN**: Replace the `b-cdn.net` link with the local `docs/assets/vision-banner.png` (if it matches).
4.  **Localize Placeholders**: Generate actual 15x15 color swatches for `docs/features/BRANDING.md` instead of relying on `placehold.co`.
