# Theme Material Variant Generation Guide

**Purpose**: Step-by-step guide for generating material variants for the 6 new themes  
**Themes**: Ocean, Matrix, Sunset, Industrial, Tokyo, Chernobyl  
**Files to Update**: `visual-states.json` (37KB) + `materials_variants.json` (36KB)

---

## 📊 Work Estimation

**Total Work**: ~840 material variant entries needed
- visual-states.json: ~420 entries (7 buildings × 10 materials average × 6 themes)
- materials_variants.json: ~420 entries (140 base materials × 3 existing themes = pattern to follow)

**Time Estimate**: ~8-12 hours manual work (or script-assisted 2-3 hours)

---

## 🎨 Theme Color Palettes & Aesthetic Guidelines

### Ocean Theme
**Primary Colors**: Deep blues, teals, bioluminescent greens  
**Aesthetic**: Underwater civilization, glass domes, coral-inspired architecture
- Base Color: `#1e90ff` (Dodger Blue)
- Secondary: `#00ced1` (Dark Turquoise)
- Emissive: `#00ffaa` (Bioluminescent Green)
- Metalness: 0.3-0.5 (wet, reflective surfaces)
- Roughness: 0.1-0.3 (smooth, water-smoothed)

**Material Examples**:
```json
"ocean": {
  "color": "#1e90ff",
  "metalness": 0.4,
  "roughness": 0.2,
  "emissive": "#00ced1",
  "emissiveIntensity": 0.5,
  "opacity": 0.8
}
```

### Matrix Theme
**Primary Colors**: Green phosphor, black backgrounds  
**Aesthetic**: Digital code rain, terminal screens, cyberpunk hacker aesthetic
- Base Color: `#003300` (Dark Green)
- Emissive: `#00ff00` (Matrix Green)
- Accent: `#00aa00` (Lime Green)
- Metalness: 0.7-0.9 (metallic terminals)
- Roughness: 0.2-0.4

**Material Examples**:
```json
"matrix": {
  "color": "#001100",
  "metalness": 0.85,
  "roughness": 0.25,
  "emissive": "#00ff00",
  "emissiveIntensity": 0.9
}
```

### Sunset Theme
**Primary Colors**: Orange, gold, warm purples  
**Aesthetic**: Golden hour, warm evening ambiance, California sunset vibes
- Base Color: `#ff8c00` (Dark Orange)
- Secondary: `#ffd700` (Gold)
- Accent: `#ff6347` (Tomato)
- Emissive: `#ffaa44` (Warm Glow)
- Metalness: 0.6-0.8
- Roughness: 0.3-0.5

**Material Examples**:
```json
"sunset": {
  "color": "#ff8c00",
  "metalness": 0.7,
  "roughness": 0.4,
  "emissive": "#ffd700",
  "emissiveIntensity": 0.6
}
```

### Industrial Theme
**Primary Colors**: Rusted browns, grays, dark metals  
**Aesthetic**: Factory floors, oxidized metal, steampunk industrial
- Base Color: `#8b4513` (Saddle Brown)
- Secondary: `#696969` (Dim Gray)
- Accent: `#cd853f` (Peru - oxidized copper)
- Emissive: None or very low (`#ff4500` dim glow)
- Metalness: 0.5-0.7 (rusted metal)
- Roughness: 0.7-0.9 (oxidized, rough)

**Material Examples**:
```json
"industrial": {
  "color": "#8b4513",
  "metalness": 0.6,
  "roughness": 0.85,
  "emissive": "#ff4500",
  "emissiveIntensity": 0.1
}
```

### Tokyo Theme
**Primary Colors**: Hot pink, electric blue, neon purple  
**Aesthetic**: Tokyo streets at night, neon signs, Japanese urban culture
- Base Color: `#ff1493` (Deep Pink)
- Secondary: `#00ffff` (Cyan)
- Accent: `#9400d3` (Dark Violet)
- Emissive: `#ff00ff` (Magenta)
- Metalness: 0.8-0.95 (shiny neon)
- Roughness: 0.05-0.2 (glossy signs)

**Material Examples**:
```json
"tokyo": {
  "color": "#ff1493",
  "metalness": 0.9,
  "roughness": 0.1,
  "emissive": "#ff00ff",
  "emissiveIntensity": 0.7
}
```

### Chernobyl Theme
**Primary Colors**: Sickly yellows, radioactive greens, grays  
**Aesthetic**: Post-apocalyptic, abandoned Soviet structures, radioactive decay
- Base Color: `#9acd32` (Yellow Green - radioactive)
- Secondary: `#808080` (Gray - concrete)
- Accent: `#adff2f` (Green Yellow - radiation glow)
- Emissive: `#ccff00` (Chartreuse - eerie glow)
- Metalness: 0.3-0.5 (decayed metal)
- Roughness: 0.8-0.95 (weathered, corroded)

**Material Examples**:
```json
"chernobyl": {
  "color": "#808080",
  "metalness": 0.4,
  "roughness": 0.9,
  "emissive": "#9acd32",
  "emissiveIntensity": 0.3
}
```

---

## 📝 Step-by-Step Process

### Option A: Manual Generation (8-12 hours)

#### Step 1: Update visual-states.json

**For each theme and each building**, add material variants:

```json
// Example for Ocean theme in Experience building
"Experience": {
  "materials": {
    "MAT_Mat_build_base_silver": {
      "default": { /* existing */ },
      "cyberpunk": { /* existing */ },
      "mars": { /* existing */ },
      "pandora": { /* existing */ },
      "ocean": {
        "color": "#1e90ff",
        "metalness": 0.4,
        "roughness": 0.2,
        "emissive": "#00ced1",
        "emissiveIntensity": 0.5
      }
    }
  }
}
```

**Repeat for ALL materials in ALL buildings** (HTLand, Experience, Skills, Vision, Docs, About, Projects, Blog)

#### Step 2: Update materials_variants.json

**For each theme**, add variant mappings:

```json
"Ocean": {
  "MAT_Mat_build_base_silver": "MAT_Mat_build_base_silver__Ocean",
  "MAT_Mat_steel": "MAT_Mat_steel__Ocean",
  // ... repeat for ALL 140 materials
}
```

**Note**: Most materials follow the pattern `OriginalName__ThemeName`

---

### Option B: Script-Assisted Generation (2-3 hours)

**Create** `scripts/generate-theme-variants.js`:

```javascript
import fs from 'fs';

const THEME_CONFIGS = {
  ocean: {
    baseColor: '#1e90ff',
    emissive: '#00ced1',
    emissiveIntensity: 0.5,
    metalness: 0.4,
    roughness: 0.2
  },
  matrix: {
    baseColor: '#001100',
    emissive: '#00ff00',
    emissiveIntensity: 0.9,
    metalness: 0.85,
    roughness: 0.25
  },
  // ... rest of themes
};

function generateVariant(materialName, themeConfig) {
  return {
    color: themeConfig.baseColor,
    metalness: themeConfig.metalness,
    roughness: themeConfig.roughness,
    emissive: themeConfig.emissive,
    emissiveIntensity: themeConfig.emissiveIntensity
  };
}

// Read existing files
const visualStates = JSON.parse(fs.readFileSync('src/config/visual-states.json'));
const materials Variants = JSON.parse(fs.readFileSync('config/materials_variants.json'));

// Generate variants for each theme
for (const [themeName, themeConfig] of Object.entries(THEME_CONFIGS)) {
  console.log(`Generating variants for ${themeName}...`);
  
  // Update visual-states.json
  for (const building of Object.keys(visualStates.buildings)) {
    for (const material of Object.keys(visualStates.buildings[building].materials)) {
      visualStates.buildings[building].materials[material][themeName] = 
        generateVariant(material, themeConfig);
    }
  }
  
  // Update materials_variants.json
  materialsVariants.variants[capitalize(themeName)] = {};
  for (const baseMaterial of Object.keys(materialsVariants.variants.Original)) {
    materialsVariants.variants[capitalize(themeName)][baseMaterial] = 
      `${baseMaterial}__${capitalize(themeName)}`;
  }
}

// Write back
fs.writeFileSync('src/config/visual-states.json', JSON.stringify(visualStates, null, 2));
fs.writeFileSync('config/materials_variants.json', JSON.stringify(materialsVariants, null, 2));

console.log('✅ Theme variants generated!');
```

**Run**:
```bash
node scripts/generate-theme-variants.js
```

This will automatically generate ALL variants following the established pattern.

---

## ✅ Validation Checklist

After generating variants, validate:

- [ ] visual-states.json is valid JSON (no syntax errors)
- [ ] materials_variants.json is valid JSON
- [ ] Each theme has entries for ALL buildings
- [ ] Color codes are valid hex values
- [ ] Metalness/roughness values are 0.0-1.0
- [ ] Emissive intensity values are reasonable (0.0-1.0)
- [ ] Test in CockpitConsole selector (each theme applies correctly)

---

## 🎯 Recommended Approach

1. **Priority 1 (NOW)**: Generate Ocean + Matrix themes manually (test pattern)
2. **Priority 2**: Create script to automate remaining 4 themes
3. **Priority 3**: Fine-tune colors/properties based on visual testing

**Estimated Total Time**: 4-6 hours (2 manual + 2-3 script + 1 testing)

---

## 📦 Deliverables

When complete, you'll have:
- ✅ 13 total themes (7 existing + 6 new)
- ✅ ~1,800 material variant entries across both JSON files
- ✅ Visually rich, diverse aesthetic options for portfolio
- ✅ Better showcase of technical capabilities (dynamic material system)

---

**Next Step**: Choose manual vs. script approach and start with Ocean theme first (easiest to visualize/test)
