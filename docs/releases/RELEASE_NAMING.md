# Release Naming & History Analysis

## 1. Executive Summary

This document analyzes the divergence between the project's internal versioning (currently referencing **v2.2.0**) and the official GitHub tags (currently at **v1.0.0**). This discrepancy arose from a lack of standardized merge strategies and release triggers during previous development phases.

**Current State:**
- **Official Git Tag:** `v1.0.0` (Release)
- **Internal Working Version:** `v2.2.0` (Context Configuration & SSOT)
- **Changelog Status:** Contains entries for `[2.0.0]` which were never officially tagged in git.

## 2. Release History Analysis

The following table reconstructs the history based on Git Tags, Pull Requests, and GitHub Project Items (inferred from branch names).

| Tag | Date | Pull Request | GitHub Item (Branch) | Description | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **v2.3.0** | *Pending* | *Current* | `38-generalcleanup` | General CleanUp, Branding, Creative Icons | **Pending Release** |
| **v2.2.0** | *Skipped* | - | `context-configuration` | SSOT, Dynamic UI (Subsumed by v2.3.0) | **Internal Only** |
| **v2.0.0** | *Missing* | - | - | Runtime Materials, Navigable Buildings | **Changelog Only** |
| **v1.0.0** | 2025-11-15 | #34 | `30-subdomains` | "VersionOne" - Initial Production Release | **Official Tag** |
| **v0.1.3** | 2025-11-22 | #34 | `30-subdomains` | Subdomains & UI Unification | **Tag** |
| **v0.1.0-beta** | 2025-09-23 | #11 | `9-betacomponents` | Production-Ready Asset System | **Tag** |
| **v0.0.1 - v0.0.9** | Various | #19-#26 | `versionone` | Initial development iterations | **Tags** |

### Key Findings
1.  **The "Lost" v2.0.0:** The features for v2.0.0 (Runtime Materials, etc.) were merged but likely never triggered a release tag due to incorrect commit messages or workflow failures.
2.  **Version Jump:** The project jumped internally from v1.0.0 to v2.0.0/v2.2.0 without a corresponding git tag, causing the "chaos" in version tracking.
3.  **Branch naming:** Branch names like `30-subdomains` and `9-betacomponents` map directly to GitHub Project items, providing a clear audit trail if traced correctly.

## 3. Documentation Audit

The following table analyzes existing documentation files and their version alignment.

| File Path | Claimed Version | Associated Tag/Era | Match Status | Notes |
| :--- | :--- | :--- | :--- | :--- |
| `README.md` | v2.2.0 | `context-configuration` | ✅ Match | Updated in current branch |
| `CHANGELOG.md` | v2.0.0 / v1.0.0 | `context-configuration` | ⚠️ Partial | Lists v2.0.0 but tag is missing |
| `docs/release/v2.2.0/RELEASE_STRATEGY.md` | v2.2.0 | `context-configuration` | ✅ Match | New file for this release |
| `docs/features/RUNTIME_MATERIALS_GUIDE.md` | v2.0.0 | `v2.0.0` (Untagged) | ❌ Mismatch | Describes v2.0 features present in code but not tagged |
| `docs/features/IMPLEMENTATION_SUMMARY.md` | v2.0.0 | `v2.0.0` (Untagged) | ❌ Mismatch | Describes v2.0 implementation |
| `docs/pipeline/HTLAND_SETUP_GUIDE.md` | v2.0.0 | `v2.0.0` (Untagged) | ❌ Mismatch | Describes LOD system (v2.0) |

## 4. Recommendations

To reconcile the history and move forward with a clean slate:

### A. Immediate Action (v2.2.0 Release)
Since we are currently on `context-configuration` preparing for **v2.2.0**:
1.  **Do NOT retag history:** It is messy to rewrite git tags for v2.0.0.
2.  **Squash Merge & Release:** Merge the current branch with `feat(release): v2.2.0`. This will create the official `v2.2.0` tag, effectively "catching up" the git history to the internal version.
3.  **Update Changelog:** Ensure `CHANGELOG.md` correctly reflects that v2.0.0 features are subsumed or historically acknowledged in the v2.2.0 release if they weren't previously tagged.

### B. Documentation Strategy
1.  **Archive Legacy:** Move v1.x specific docs to `docs/archive/v1.0.0/` if they are obsolete.
2.  **Standardize Headers:** Ensure all active docs in `docs/` reflect the current architecture (v2.2.0).
3.  **Release Folders:** Continue the pattern `docs/release/vX.X.X/` for release-specific artifacts (strategies, audits, walkthroughs).

### C. Future Naming Convention
- **Branch:** `feat/description` or `fix/description` (linked to GitHub Issue/Project Item).
- **PR Title:** Conventional Commits (`feat:`, `fix:`, `chore:`).
- **Release Tag:** SemVer (`vX.Y.Z`).
- **Documentation:** Updated *before* release in the same PR.

## 5. GitHub Projects Integration
For future reference, link PRs to GitHub Project items by including the issue number (e.g., `Closes #30`) in the PR description. This automates the "Done" column movement and keeps the history linked.
