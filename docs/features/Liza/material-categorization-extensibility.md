# Material Categorization - Extensibility & Future Enhancements

**Feature**: Hybrid Material Categorization with Optional Hints  
**Version**: v5.1 (Implemented)  
**Status**: Ready for Extension

---

## 📖 Table of Contents

1. [Current System Overview](#current-system-overview)
2. [How to Add New Hints](#how-to-add-new-hints)
3. [Future Enhancement: JSON Configuration](#future-enhancement-json-configuration)
4. [Future Enhancement: Material Library](#future-enhancement-material-library)
5. [Future Enhancement: Visual Hint Selector](#future-enhancement-visual-hint-selector)

---

## Current System Overview

### What We Have

**Terrain Hints** (6 types):
- `dark`, `earthy`, `alien`, `volcanic`, `icy`, `desert`

**Water Hints** (5 types):
- `clear`, `toxic`, `murky`, `frozen`, `glowing`

### How It Works

```javascript
// Gemini calls apply_visual_theme with optional hints
{
  styleName: "Desert Sunset",
  primaryColor: "#ff8c42",
  accentColor: "#ffd700",
  terrainHint: "desert",    // OPTIONAL
  waterHint: "clear",       // OPTIONAL
  emissiveIntensity: 0.8
}

// System applies hint-based color derivation
terrain → adjustColorForTerrain(primaryColor, "desert")
water → adjustColorForWater(accentColor, "clear")
```

---

## How to Add New Hints

### Step 1: Add Enum Value to Tool Schema

**File**: `src/utils/liza/liza-tools.js`

```javascript
terrainHint: {
  type: "STRING",
  description: "OPTIONAL: Terrain aesthetic hint...",
  enum: [
    "dark", 
    "earthy", 
    "alien", 
    "volcanic", 
    "icy", 
    "desert",
    "lava",        // ← NEW HINT
    "crystalline"  // ← NEW HINT
  ]
}
```

### Step 2: Implement Color Derivation

**File**: `src/utils/liza/liza-theme-generator.js`

```javascript
function adjustColorForTerrain(hexColor, hint = null) {
  const r = parseInt(hexColor.slice(1, 3), 16);
  const g = parseInt(hexColor.slice(3, 5), 16);
  const b = parseInt(hexColor.slice(5, 7), 16);
  
  let newR, newG, newB;
  
  switch(hint) {
    // ... existing cases ...
    
    case 'lava':
      // Bright red-orange with glow
      newR = Math.min(Math.floor(r * 1.2 + 100), 255);
      newG = Math.floor(g * 0.5 + 50);
      newB = Math.floor(b * 0.2);
      break;
      
    case 'crystalline':
      // High saturation, bright
      const avgCrystal = (r + g + b) / 3;
      const brightFactor = 1.5;
      newR = Math.min(Math.floor(avgCrystal + (r - avgCrystal) * brightFactor + 80), 255);
      newG = Math.min(Math.floor(avgCrystal + (g - avgCrystal) * brightFactor + 80), 255);
      newB = Math.min(Math.floor(avgCrystal + (b - avgCrystal) * brightFactor + 80), 255);
      break;
    
    // ... default case ...
  }
  
  // Clamp and return
  newR = Math.max(0, Math.min(255, newR));
  newG = Math.max(0, Math.min(255, newG));
  newB = Math.max(0, Math.min(255, newB));
  
  return `#${newR.toString(16).padStart(2, '0')}${newG.toString(16).padStart(2, '0')}${newB.toString(16).padStart(2, '0')}`;
}
```

### Step 3: Document in System Prompt

**File**: `src/utils/liza/liza-prompts.js`

```javascript
TERRAIN HINTS:
- "dark": Very dark terrain (Matrix, cyberpunk) - Almost black with subtle glow
- "earthy": Natural brown-green (forest, natural) - Rich organic tones
- "alien": Otherworldly saturated colors - Strange, vibrant hues
- "volcanic": Dark with red/orange embers - Glowing cracks
- "icy": Light blues, frozen - Crystalline white-blue
- "desert": Sandy, arid tones - Beige-brown sand
- "lava": Bright red-orange molten - Flowing fire         // ← NEW
- "crystalline": High saturation gems - Bright minerals   // ← NEW
```

### Step 4: Add Example Theme

```javascript
**Example Themes**:
1. Matrix: {styleName: "The Matrix", primaryColor: "#0d0d0d", ...}
2. Desert Sunset: {styleName: "Desert Sunset", terrainHint: "desert", ...}
// ... more examples ...
7. Lava Planet: {styleName: "Lava Planet", primaryColor: "#4a2c2a", accentColor: "#ff4500", terrainHint: "lava", emissiveIntensity: 1.5}
```

---

## Future Enhancement: JSON Configuration

**Goal**: Make hint definitions configurable without code changes

### Proposed File Structure

**New File**: `src/config/material-hints.json`

```json
{
  "version": "1.0.0",
  "terrainHints": {
    "dark": {
      "description": "Very dark terrain (Matrix, cyberpunk)",
      "colorTransform": {
        "operation": "multiply",
        "factor": 0.2
      },
      "examples": ["The Matrix", "Cyberpunk Neon"]
    },
    "earthy": {
      "description": "Natural brown-green (forest, natural)",
      "colorTransform": {
        "operation": "custom",
        "formula": {
          "r": "base.r * 0.6 + 70",
          "g": "base.g * 0.8 + 40",
          "b": "base.b * 0.5"
        }
      },
      "examples": ["Enchanted Forest", "Natural Landscape"]
    },
    "lava": {
      "description": "Bright red-orange molten",
      "colorTransform": {
        "operation": "custom",
        "formula": {
          "r": "min(base.r * 1.2 + 100, 255)",
          "g": "base.g * 0.5 + 50",
          "b": "base.b * 0.2"
        }
      },
      "emissiveMultiplier": 1.8,
      "examples": ["Lava Planet", "Volcanic Wasteland"]
    }
  },
  "waterHints": {
    "clear": {
      "description": "Natural water - Transparent blue",
      "colorTransform": {
        "operation": "shiftBlue",
        "desaturation": 0.2
      },
      "opacity": 0.7,
      "examples": ["Ocean Depths", "Crystal Lake"]
    },
    "acid": {
      "description": "Corrosive - Bright yellow-green",
      "colorTransform": {
        "operation": "custom",
        "formula": {
          "r": "base.r * 0.4 + 50",
          "g": "min(base.g * 1.8 + 100, 255)",
          "b": "base.b * 0.3"
        }
      },
      "opacity": 0.85,
      "emissiveMultiplier": 1.5,
      "examples": ["Acid Rain", "Toxic Swamp"]
    }
  }
}
```

### Implementation Plan

```javascript
// src/utils/liza/material-hints-loader.js

import materialHintsConfig from '../../config/material-hints.json';

/**
 * Parse and apply color transform from config
 */
export function applyMaterialHint(hexColor, hintCategory, hintName) {
  const hintDef = materialHintsConfig[hintCategory]?.[hintName];
  if (!hintDef) {
    console.warn(`[MaterialHints] Unknown hint: ${hintCategory}.${hintName}`);
    return hexColor; // Return unchanged
  }
  
  const { colorTransform } = hintDef;
  const base = parseHexColor(hexColor);
  
  switch(colorTransform.operation) {
    case 'multiply':
      return multiplyColor(base, colorTransform.factor);
      
    case 'shiftBlue':
      return shiftToBlue(base, colorTransform.desaturation);
      
    case 'custom':
      return evaluateFormula(base, colorTransform.formula);
      
    default:
      return hexColor;
  }
}

/**
 * Evaluate custom formula from JSON
 */
function evaluateFormula(base, formula) {
  // SECURITY NOTE: In production, use a safe eval library or pre-compile
  // For now, simple string replacement
  const context = { base, min: Math.min, max: Math.max };
  
  const r = eval(formula.r.replace(/base\./g, 'context.base.'));
  const g = eval(formula.g.replace(/base\./g, 'context.base.'));
  const b = eval(formula.b.replace(/base\./g, 'context.base.'));
  
  return toHex({ r, g, b });
}
```

### Benefits

✅ Add new hints without code changes  
✅ Non-technical users can contribute hints  
✅ Easy A/B testing of formulas  
✅ Version control for hint definitions  
✅ Shareable hint libraries

---

## Future Enhancement: Material Library

**Goal**: Pre-defined material presets that users can browse

### Proposed File Structure

**New File**: `src/config/material-library.json`

```json
{
  "presets": {
    "lava": {
      "name": "Lava",
      "category": "terrain",
      "preview": "/assets/previews/lava.png",
      "properties": {
        "baseColor": "#4a2c2a",
        "emissiveColor": "#ff4500",
        "emissiveIntensity": 1.5,
        "roughness": 0.9,
        "metalness": 0.1
      },
      "hints": {
        "terrainHint": "lava",
        "waterHint": "toxic"
      },
      "tags": ["hot", "glow", "volcanic", "red"]
    },
    "crystalCave": {
      "name": "Crystal Cave",
      "category": "environment",
      "preview": "/assets/previews/crystal-cave.png",
      "properties": {
        "baseColor": "#b8d4ff",
        "emissiveColor": "#00d4ff",
        "emissiveIntensity": 0.8,
        "roughness": 0.1,
        "metalness": 0.3
      },
      "hints": {
        "terrainHint": "crystalline",
        "waterHint": "frozen"
      },
      "tags": ["ice", "crystal", "blue", "magical"]
    }
  }
}
```

### UI Integration

```javascript
// Future: Material Palette Selector in LIZA
<LizaMaterialPalette 
  onSelect={(preset) => {
    applyMaterialPreset(preset);
  }}
/>

// User can browse visually:
// [Lava] [Crystal Cave] [Desert] [Ocean] [Forest] [Cyberpunk]
//   🔥      💎           🏜️      🌊       🌳       🌃
```

---

## Future Enhancement: Visual Hint Selector

**Goal**: Let Gemini choose hints based on reference images

### Workflow

```javascript
// Step 1: User uploads reference image
const userImage = "user-uploads/desert-sunset.jpg";

// Step 2: Analyze image with Gemini Vision
const analysis = await analyzeImageAesthetic(userImage);
// Returns: {
//   dominantColors: ["#ff8c42", "#ffd700"],
//   mood: "warm, arid, peaceful",
//   suggestedHints: {
//     terrain: "desert",
//     water: "clear"
//   }
// }

// Step 3: Auto-generate theme from analysis
const theme = {
  styleName: "Desert Sunset (from image)",
  primaryColor: analysis.dominantColors[0],
  accentColor: analysis.dominantColors[1],
  terrainHint: analysis.suggestedHints.terrain,
  waterHint: analysis.suggestedHints.water
};
```

### System Prompt Enhancement

```
## Image-Based Theme Generation (Future)

When analyzing a reference image to generate a theme:

1. **Identify Environment Type**:
   - Look for terrain characteristics (rocky, sandy, icy, etc.)
   - Identify water bodies (clear ocean, frozen lake, toxic swamp, etc.)

2. **Map to Hints**:
   - Sandy dunes → terrainHint: "desert"
   - Frozen landscape → terrainHint: "icy", waterHint: "frozen"
   - Volcanic ash → terrainHint: "volcanic"
   - Glowing water → waterHint: "glowing"

3. **Extract Colors**:
   - Use dominant colors from image
   - Adjust for Three.js material properties
```

---

## Precision JSON Schema (Future v6.0)

**Goal**: Full material specification per object

### Current System

```json
{
  "styleName": "The Matrix",
  "primaryColor": "#0d0d0d",
  "terrainHint": "dark",
  "waterHint": "toxic"
  // → Applied to ALL terrain and ALL water globally
}
```

### Future: Per-Object Specification

```json
{
  "styleName": "Mixed Environment",
  "global": {
    "primaryColor": "#0d0d0d",
    "accentColor": "#00ff41"
  },
  "materials": {
    "HTLand_Terrain_01": {
      "type": "terrain",
      "hint": "volcanic",
      "color": "#4a2c2a",
      "emissiveColor": "#ff4500",
      "emissiveIntensity": 1.2
    },
    "HTLand_Terrain_02": {
      "type": "terrain",
      "hint": "icy",
      "color": "#e6f7ff",
      "emissiveColor": "#00d4ff",
      "emissiveIntensity": 0.3
    },
    "HTLand_Water": {
      "type": "water",
      "hint": "clear",
      "color": "#001f3f",
      "opacity": 0.75
    }
  }
}
```

**Benefits**:
- Mix different terrain types in one scene
- Precise control over each material
- Create complex, layered aesthetics

**Complexity**: HIGH - requires UI for material-level editing

---

## Recommended Implementation Order

### Phase 1 (v5.1) ✅ DONE
- [x] Hybrid with basic hints (6 terrain + 5 water)

### Phase 2 (v5.2)
- [ ] Add 2-3 more hints based on user feedback
- [ ] Test with real users
- [ ] Refine existing hint formulas

### Phase 3 (v5.3)
- [ ] Implement JSON configuration for hints
- [ ] Create material-hints.json
- [ ] Add formula evaluator

### Phase 4 (v6.0)
- [ ] Build material library with presets
- [ ] Add visual preview system
- [ ] Implement material palette selector UI

### Phase 5 (v6.5)
- [ ] Multimodal image analysis
- [ ] Auto-suggest hints from images
- [ ] Community hint sharing

### Phase 6 (v7.0)
- [ ] Per-object material specification
- [ ] Material editor UI
- [ ] Advanced layering and mixing

---

## Quick Reference: Adding a New Hint

**Checklist**:
1. [ ] Add enum value to `liza-tools.js`
2. [ ] Implement case in `adjustColorForTerrain()` or `adjustColorForWater()`
3. [ ] Document in `liza-prompts.js` TERRAIN HINTS or WATER HINTS section
4. [ ] Add example theme using the new hint
5. [ ] Test with Gemini: "Create a [hint-name] themed environment"
6. [ ] Update `material-categorization-options.md` comparison table

**Example PR**:
```
feat(liza): add 'lava' terrain hint for molten landscapes

- Added 'lava' enum to terrainHint in liza-tools.js
- Implemented bright red-orange color formula in liza-theme-generator.js
- Documented usage in system prompts with Lava Planet example
- Tested with "volcanic planet" and "lava world" prompts

Result: Terrain now glows bright red-orange with high emissive
```

---

## Related Documents

- [Material Categorization Options](./material-categorization-options.md) - Analysis & comparison
- [LIZA Feature Guide](./LIZA-FEATURE.md) - Main feature documentation
- [v5.0.0 Release Notes](../../releases/v5.0.0.md) - Neuro-Architect details

---

<div align="center">

**Material Categorization - Ready for Extension** 🎨

*Start with simple hints, scale to full material editor*

</div>
