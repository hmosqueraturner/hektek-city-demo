# Walkthrough - v2.2.0 Release Preparation

I have successfully refactored the codebase to enforce a "Single Source of Truth" architecture, implemented dynamic animations, and prepared the release workflow for v2.2.0.

## Changes

### 1. Configuration Centralization (SSOT)
- **Updated `src/config/buildings-config.json`**: Added missing buildings (Blog, AboutMe, Projects, Docs) and migrated camera settings (focus/inside offsets) from React code to JSON.
- **Created `src/hooks/useConfig.js`**: New hooks `useBuildingConfig` and `useThemeConfig` to abstract data access.

### 2. Component Refactoring
- **`src/components/BuildingLabel.jsx`**: Removed hardcoded theme logic; now accepts style props. Implemented Y-axis billboarding for readable 3D text.
- **`src/components/MapRPG.jsx`**: Dynamic rendering loop based on JSON config. Removed hardcoded instances.
- **`src/components/NavigableBuilding.jsx`**: Added `animationType` prop for custom redirection effects ('warp', 'scan').

### 3. Dynamic Animations
- **Standardized Viewer Animations**: Applied `fadeIn` and cyberpunk effects to all viewers (`SkillsViewer`, `VisionViewer`, etc.).
- **Redirection Effects**:
    - **Projects**: 'Warp' speed animation on double-click.
    - **Blog**: 'Scan' line animation on double-click.

### 4. Release Workflow & Documentation
- **Fixed `release.yml`**: Relaxed version bump tokens to support conventional commits.
- **Documentation Overhaul**: Moved root `.md` files to `/docs`. Updated `README.md` with v2.2.0 architecture and Mermaid diagrams.
- **Release Strategy**: Created `docs/release/v2.2.0/RELEASE_STRATEGY.md` for controlled deployment.

## Verification Results

### Static Analysis
- **Hardcoding Visuals**: Resolved. Colors and styles are passed from `useThemeConfig` or `buildings-config.json`.
- **Hardcoding Spatial**: Resolved. Positions, rotations, and scales are read from JSON.
- **Duplicated Logic**: Resolved. Camera settings and building lists are no longer duplicated in `MapRPG.jsx`.

### Manual Verification Steps
1.  **Start the Application**: Run `npm run dev`.
2.  **Visual Check**: Verify that "Skills", "Experience", "Vision", "Blog", "About Me", "Projects", and "Docs" buildings appear on the map.
3.  **Navigation**: Double-click "Blog" or "Projects" to verify redirection works with new animations ('warp'/'scan').
4.  **Theme Switching**: Change the theme using the UI controls and verify that building labels update their colors.
5.  **Release**: Merge the PR to `main` with the message `feat(release): v2.2.0` to trigger the GitHub Action.
