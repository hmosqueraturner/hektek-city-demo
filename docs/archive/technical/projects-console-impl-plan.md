# Implementation Plan - v3.3.0: Projects Console (Sci-Fi Arcade) (Completed)

## Goal
Create a new standalone component `ProjectsConsole` that renders a 3D Sci-Fi Arcade scene. This scene will serve as an immersive interface for browsing projects, utilizing the existing R2 asset infrastructure.

## Architecture

### 1. Component Structure
`src/components/Projects/ProjectsConsole.jsx`
-   **Canvas**: Dedicated R3F Canvas, independent of the main MapRPG.
-   **Scene Graph**:
    -   `ArcadeEnvironment`: The room geometry (walls, floor, ceiling).
    -   `ArcadeMachine`: Reusable component for individual project stations.
    -   `PlayerController`: First-person or restricted orbit camera.
    -   `Lighting`: Neon aesthetics (AreaLights, PointLights, Bloom).

### 2. Asset Strategy
We will leverage the existing `src/utils/assetsHelper.js` to load assets from Cloudflare R2, maintaining consistency with the current pipeline.

-   **Base Path**: `models/quality/standard/` (and `low-res` variants).
-   **File Types**: `.glb` for models, `.jpg/.png` for textures, `.exr` for environment maps.
-   **Loading Logic**:
    ```javascript
    import { getAssetUrl } from '../../utils/assetsHelper';
    // Example usage
    const arcadeMachineUrl = getAssetUrl('models', 'ArcadeMachine_01');
    ```

### 3. Scene Composition (Arcade Theme)
Since specific GLBs are not yet fully designed, we will use a **Placeholder-First** approach:

-   **Environment**: A dark, reflective room (Cube with inverted normals or simple planes).
-   **Placeholders**:
    -   **Arcade Cabinets**: Neon-edged Boxes (`<Box args={[1, 2, 1]} />`).
    -   **Screens**: Emissive planes displaying project thumbnails.
    -   **Decor**: Floating geometric shapes (Spheres, Icosahedrons) to add depth.
-   **Lighting**:
    -   Heavy use of `Environment` (Cyberpunk HDRI).
    -   `EffectComposer` with `Bloom` for that "Arcade Glow".

## Implementation Steps

### Phase 1: Foundation
1.  Create `src/components/Projects/ProjectsConsole.jsx`.
2.  Setup basic R3F scene (Canvas, Lights, Camera).
3.  Implement `ArcadeEnvironment` with basic geometry.

### Phase 2: Asset Integration
1.  Connect to `AssetManager` / `assetsHelper`.
2.  Implement `ArcadeMachine` component that can load a GLB or fallback to a placeholder.
3.  Verify loading from R2 (using existing assets like `Building_Experience.glb` as a test if needed, or new ones).

### Phase 3: Interaction & Polish
1.  Add camera controls (OrbitControls restricted or PointerLockControls).
2.  Implement "Screen" interaction (hover effects, click to view project).
3.  Apply "Cyberpunk Glass" UI overlay for navigation (Return to Map).

## User Review Required
> [!NOTE]
> **Placeholder Strategy**: We will use simple geometric shapes with neon materials until final GLB assets are ready. This allows us to build the logic and interaction immediately.

## Verification
-   [ ] Scene loads without errors.
-   [ ] Assets (or placeholders) appear in 3D space.
-   [ ] Lighting and Post-processing (Bloom) are active.
-   [ ] "Return to Orbit" functionality works.
