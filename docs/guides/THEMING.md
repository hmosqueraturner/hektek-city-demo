# 🎨 Theme & Runtime Materials System

HEKTEK City uses a custom **Runtime Materials System** to allow instant theme switching without reloading 3D models. This enables the "Cyberpunk", "Mars", "Pandora", and other themes to coexist on the same geometry.

## 🚀 Overview

Instead of using the standard GLTF `KHR_materials_variants` extension (which requires complex pipelines and bloats file size), we use a **JSON-driven configuration** approach.

1.  **One Geometry**: We load a single `.glb` file for the city.
2.  **Dynamic Materials**: A React hook (`useDynamicMaterials`) watches for theme changes.
3.  **Instant Swap**: When the theme changes, the hook traverses the scene and updates material properties (color, emissive, roughness) in <1ms.

## 🛠️ Configuration

The system is controlled by two key files in `src/config/`:

### 1. `theme-config.json`
Defines the available themes and their global properties (background, fog, post-processing).

```json
{
  "themes": {
    "cyberpunk": {
      "name": "Night City",
      "type": "dark",
      "colors": {
        "primary": "#00f0ff",
        "background": "#050510"
      }
    },
    "mars": {
      "name": "Red Planet",
      "type": "light",
      "colors": {
        "primary": "#ff4400",
        "background": "#220500"
      }
    }
  }
}
```

### 2. `visual-states.json`
This is the "Material Map". It defines how each named material in the 3D model should look for each theme.

```json
{
  "Building_Glass": {
    "cyberpunk": { "color": "#001133", "emissive": "#00ffff", "roughness": 0.1 },
    "mars": { "color": "#aa4400", "emissive": "#000000", "roughness": 0.8 }
  },
  "Road_Asphalt": {
    "cyberpunk": { "color": "#111111", "roughness": 0.2 },
    "mars": { "color": "#553322", "roughness": 0.9 }
  }
}
```

## 💻 Implementation

The core logic resides in `useDynamicMaterials.ts`.

```typescript
// Simplified Logic
export const useDynamicMaterials = (scene, theme) => {
  useEffect(() => {
    const config = VISUAL_STATES;
    
    scene.traverse((child) => {
      if (child.isMesh && config[child.material.name]) {
        const state = config[child.material.name][theme];
        
        if (state.color) child.material.color.set(state.color);
        if (state.emissive) child.material.emissive.set(state.emissive);
        // ... apply other props
      }
    });
  }, [scene, theme]);
};
```

## 📝 How to Add a New Theme

1.  **Add entry to `theme-config.json`**: Define the ID and display name.
2.  **Update `visual-states.json`**: Add a new key for your theme ID to *every* material entry.
    *   *Tip: Use the "Theme Editor" in the dev tools (if enabled) to tweak values live and export the JSON.*

## 📝 How to Add a New Material

1.  **Name it in Blender**: Ensure your material has a unique, descriptive name (e.g., `Neon_Sign_A`).
2.  **Export**: Export the GLB.
3.  **Add to `visual-states.json`**: Create a new entry for `Neon_Sign_A` and define its look for all active themes.
