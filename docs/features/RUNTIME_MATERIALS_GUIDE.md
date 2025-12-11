# Runtime Materials Guide (v2.2.0)

**Version:** 1.0.0
**Date:** 2025-11-19
**Status:** ✅ Production Ready

---

## 🎯 Overview

This system allows you to modify GLB model materials **in real-time from React Three Fiber** without:
- ❌ Re-exporting from Blender
- ❌ Running complex pipelines
- ❌ Using KHR_materials_variants extension
- ❌ Manual CSV editing

**Use case:** Perfect for story-driven games where visual states change based on gameplay/narrative.

---

## 📁 Files Structure

```
src/
├── hooks/
│   └── useDynamicMaterials.js       ← Hook for runtime material changes
├── config/
│   └── visual-states.json           ← YOUR configuration file
├── components/
│   ├── DynamicBuildingModel.jsx     ← Component using the hook
│   └── MapVariations.jsx            ← Demo/testing component
```

---

## 🚀 Quick Start

### 1. **Use the Component**

```jsx
import DynamicBuildingModel from './components/DynamicBuildingModel';

function MyScene() {
  const [theme, setTheme] = useState('default');

  return (
    <Canvas>
      <DynamicBuildingModel
        modelUrl="/assets/models/quality/standard/Experience.glb"
        buildingName="Experience"
        currentTheme={theme}
        debug={true}
      />
    </Canvas>
  );
}
```

### 2. **Configure Visual States**

Edit `src/config/visual-states.json`:

```json
{
  "buildings": {
    "Experience": {
      "materials": {
        "MAT_Mat_build_base_silver": {
          "default": {
            "color": "#c0c0c0",
            "metalness": 0.8,
            "roughness": 0.5
          },
          "cyberpunk": {
            "color": "#ff6600",
            "metalness": 0.95,
            "roughness": 0.1,
            "emissive": "#ff3300",
            "emissiveIntensity": 0.5
          },
          "destroyed": {
            "color": "#333333",
            "metalness": 0.0,
            "roughness": 1.0
          }
        }
      }
    }
  }
}
```

### 3. **Change Theme at Runtime**

```jsx
// Change theme based on story/gameplay
setTheme('cyberpunk');  // Materials update instantly!
setTheme('destroyed');  // Building looks destroyed
setTheme('default');    // Back to normal
```

---

## 🎨 Available Material Properties

You can modify any of these properties in `visual-states.json`:

| Property | Type | Range | Description |
|----------|------|-------|-------------|
| `color` | Hex String | `"#rrggbb"` | Base color (albedo) |
| `metalness` | Number | `0.0 - 1.0` | How metallic the surface is |
| `roughness` | Number | `0.0 - 1.0` | Surface roughness (0 = mirror, 1 = matte) |
| `emissive` | Hex String | `"#rrggbb"` | Glow/emission color |
| `emissiveIntensity` | Number | `0.0 - 10.0` | Strength of emission |
| `opacity` | Number | `0.0 - 1.0` | Transparency (0 = invisible, 1 = opaque) |
| `envMapIntensity` | Number | `0.0 - 2.0` | Environment reflection strength |

---

## 📋 How to Add a New Building

### Step 1: Export from Blender

1. Make sure material names follow format: `MAT_Mat_name`
2. Export as GLB to `public/assets/models/quality/standard/BuildingName.glb`

### Step 2: Generate Config Template

Open browser console and run:

```javascript
import { generateVisualConfig } from './hooks/useDynamicMaterials';

// Load your model in MapVariations
// Then in console:
const scene = /* get scene from your model */;
generateVisualConfig(scene, 'BuildingName');
// Copy the output JSON
```

### Step 3: Add to visual-states.json

Paste the generated config and customize colors/properties as needed.

### Step 4: Use It

```jsx
<DynamicBuildingModel
  modelUrl="/assets/models/quality/standard/BuildingName.glb"
  buildingName="BuildingName"
  currentTheme="cyberpunk"
/>
```

---

## 🎮 Story-Driven Example

```jsx
function StoryDrivenCity() {
  const [storyState, setStoryState] = useState({
    chapter: 1,
    cityCondition: 'pristine'
  });

  // Map story state to visual theme
  const getThemeForBuilding = (buildingName) => {
    if (storyState.chapter === 1) return 'default';
    if (storyState.chapter === 2) return 'cyberpunk';
    if (storyState.cityCondition === 'destroyed') return 'destroyed';
    return 'default';
  };

  return (
    <Canvas>
      {buildings.map(name => (
        <DynamicBuildingModel
          key={name}
          modelUrl={`/assets/models/quality/standard/${name}.glb`}
          buildingName={name}
          currentTheme={getThemeForBuilding(name)}
        />
      ))}
    </Canvas>
  );
}
```

---

## 🧪 Testing with MapVariations

1. Start dev server: `npm run dev`
2. Navigate to `/map-variations`
3. Select building from dropdown
4. Click theme buttons to see instant changes
5. Check console for debug logs (if debug=true)

---

## 🔧 Advanced: Using the Hook Directly

If you need more control:

```jsx
import { useGLTF } from '@react-three/drei';
import { useDynamicMaterials } from '../hooks/useDynamicMaterials';

function CustomBuilding({ modelPath, visualConfig, currentState }) {
  const { scene } = useGLTF(modelPath);

  // Apply materials
  useDynamicMaterials(scene, visualConfig, currentState, {
    debug: true,
    onMaterialUpdate: (materialName, config) => {
      console.log(`Updated ${materialName}`, config);
    }
  });

  return <primitive object={scene} />;
}
```

---

## 💡 Tips & Best Practices

### 1. **Material Naming in Blender**

Run this script ONCE in Blender to fix material names:

```python
# tools/blender/FIX_ALL_MATERIALS_NOW.py
# Already created - just run it in Blender
```

### 2. **Performance**

- Material updates are fast (< 1ms per material)
- No re-renders or geometry changes
- Safe to change themes frequently

### 3. **Hot Reload**

Edit `visual-states.json` while dev server is running → Changes apply on next theme switch.

### 4. **Debugging**

Set `debug={true}` to see console logs:

```
🎨 Applying state: cyberpunk
  ✅ Updating material: MAT_Mat_build_base_silver
  ✅ Updating material: MAT_Mat_build_sup
📊 Updated 15 materials
```

---

## 🆚 Comparison: Old vs New System

| Feature | Old (KHR_materials_variants) | New (Runtime Materials) |
|---------|------------------------------|-------------------------|
| **Setup** | 10+ manual steps | Edit 1 JSON file |
| **Pipeline** | Blender → CSV → JSON → Export → Process | Export → Configure → Done |
| **Changes** | Re-export + re-process GLB | Edit JSON, refresh browser |
| **Preview** | Must process and load in browser | Instant in browser |
| **Dynamic** | Pre-defined themes only | Unlimited states, story-driven |
| **Complexity** | High (multiple tools) | Low (pure React) |
| **Performance** | Good | Excellent |

---

## 🐛 Troubleshooting

### Materials not changing?

**Check:**
1. Material name in Blender matches name in JSON
2. Building name matches between component and JSON
3. Console for errors (set `debug={true}`)

**Fix:**
```bash
# In browser console:
import { getMaterialsInfo } from './hooks/useDynamicMaterials';
const info = getMaterialsInfo(scene);
console.table(info);
# Compare material names with your JSON
```

### Config not loading?

**Check:**
- JSON is valid (use JSONLint)
- File path is correct: `src/config/visual-states.json`
- Building name matches exactly (case-sensitive)

---

## 📚 Next Steps

1. ✅ System is ready to use
2. Edit `visual-states.json` to add your custom themes
3. Integrate with your story/gameplay logic
4. Test different visual states in MapVariations
5. Deploy when ready

---

## 🔗 Related Files

- [PLAN_REAL_SOLUTION.md](../PLAN_REAL_SOLUTION.md) - Full technical plan
- [useDynamicMaterials.js](../src/hooks/useDynamicMaterials.js) - Hook implementation
- [visual-states.json](../src/config/visual-states.json) - Your configuration file

---

**Questions? Check the code comments or ask for help!**

Last updated: 2025-11-19
