# 🏗️ Content Guide: Buildings & Decoratives

This guide explains how to populate HEKTEK City with interactive buildings and decorative elements.

## 🏢 Buildings

Buildings are the core interactive elements. They are defined in `src/config/buildings-config.json`.

### Adding a Building

1.  **Model**: Ensure the building exists in the main map GLB or is loaded as a separate GLB.
2.  **Config**: Add an entry to `buildings-config.json`.

```json
{
  "id": "skills-center",
  "name": "Skills Center",
  "position": [10, 0, -5],
  "cameraTarget": [12, 5, -5],
  "description": "Technical expertise and stack.",
  "contentSource": "content/skills.md"
}
```

*   **id**: Unique identifier (used for routing).
*   **position**: World coordinates for the floating label.
*   **cameraTarget**: Where the camera should fly to when clicked.
*   **contentSource**: Path to the markdown/JSON content to load in the Viewer.

## 🌳 Decorative Models

Decoratives are non-interactive elements (trees, streetlights, cars) that add life to the scene.

> **Note:** As of v4.0, we prefer baking static decoratives into the main map GLB for performance. However, dynamic decoratives can still be added via code.

### Adding Dynamic Decoratives

Use the `DecorativeModel` component in `MapRPG.tsx`.

```tsx
<DecorativeModel 
  model="cyber-tree.glb" 
  position={[5, 0, 5]} 
  scale={1.5} 
  variant="neon"
/>
```

## 📦 Asset Pipeline

1.  **Modeling**: Use Blender. Keep poly count low.
2.  **Export**: Export as `.glb` (Draco compression recommended).
3.  **Upload**: Upload to Cloudflare R2 (or place in `public/models` for local dev).
4.  **Register**: Update `src/config/assets-manifest.json` if using the preloader.
