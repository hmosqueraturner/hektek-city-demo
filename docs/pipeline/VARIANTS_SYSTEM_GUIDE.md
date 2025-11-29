# 🎨 Material Variants System Guide

Complete guide for the dual-mode material variants system in HekTek City.

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Variant Types](#variant-types)
3. [CSV Format](#csv-format)
4. [Workflow](#workflow)
5. [Examples](#examples)
6. [MATERIAL_PALETTE Usage](#material_palette-usage)
7. [Best Practices](#best-practices)

---

## 🎯 Overview

The Material Variants System supports **two modes** of material variation:

### **Mode 1: SWAP (Material Replacement)**
Complete replacement of materials between themes.

**Use case:** An object changes its base material entirely.

**Example:** A concrete wall becomes metal panels in Cyberpunk theme.

### **Mode 2: PROPERTIES (Property Modification)**
Same material with modified properties (color, emission, roughness, etc.).

**Use case:** An object keeps its material but changes appearance.

**Example:** A glass window changes tint color per theme.

---

## 🔀 Variant Types

### SWAP Type

```
Original Theme:  MAT_Concrete
Cyberpunk Theme: MAT_Metal_Panels
Mars Theme:      MAT_Mars_Rock
Pandora Theme:   MAT_Bio_Surface
```

Each theme uses a **completely different material**.

### PROPERTIES Type

```
Base Material: MAT_Glass (all themes use this)

Property Overrides:
- Cyberpunk: base_color:#00ffff;emission_strength:0.5
- Mars:      base_color:#ff4500;roughness:0.3
- Pandora:   base_color:#00ff7f;emission_strength:0.8
```

All themes use the **same base material** but with different property values.

---

## 📊 CSV Format

### Column Structure

```csv
Material Name,Role ID,Variant Type,Original,Cyberpunk,Mars,Pandora,Cyberpunk_Props,Mars_Props,Pandora_Props,Compatibility,Users,Extracted_Props
```

### Column Descriptions

| Column | Purpose | Example |
|--------|---------|---------|
| **Material Name** | Original material name in Blender | `MAT_Glass` |
| **Role ID** | Standardized role identifier | `MAT_Mat_glass` |
| **Variant Type** | `SWAP` or `PROPERTIES` | `PROPERTIES` |
| **Original** | Material name for Original theme | `MAT_Glass` |
| **Cyberpunk** | Material name for Cyberpunk theme | `MAT_Glass__Cyberpunk` |
| **Mars** | Material name for Mars theme | `MAT_Glass__Mars` |
| **Pandora** | Material name for Pandora theme | `MAT_Glass__Pandora` |
| **Cyberpunk_Props** | Property overrides for Cyberpunk | `base_color:#00ffff;emission_strength:2.0` |
| **Mars_Props** | Property overrides for Mars | `base_color:#ff0000;roughness:0.2` |
| **Pandora_Props** | Property overrides for Pandora | `base_color:#00ff00;emission_strength:1.5` |
| **Compatibility** | WebGL compatibility check | `Compatible (Principled BSDF)` |
| **Users** | Number of objects using material | `5` |
| **Extracted_Props** | Auto-detected properties (reference) | `base_color:#ffffff;roughness:0.5` |

---

## 🔄 Workflow

### Step 1: Generate Base CSV from Blender

Run the enhanced script in Blender:

```python
# In Blender > Scripting workspace
# Open: tools/blender/generate_materials_csv_enhanced.py
# Click "Run Script"
```

**Output:** `assets/datosAll - material_report_final_NewBuildings.csv`

This generates a CSV with:
- All materials detected
- Default `PROPERTIES` type for all
- Empty property columns
- Extracted properties for reference

---

### Step 2: Edit CSV Manually

Open the CSV and configure each material:

#### Example 1: SWAP Type (Concrete Wall)

```csv
MAT_Concrete,MAT_Mat_concrete,SWAP,MAT_Concrete,MAT_Metal_Panels,MAT_Mars_Rock,MAT_Bio_Surface,"","",""
```

- Set **Variant Type** to `SWAP`
- Define different material names for each theme
- Leave **property columns empty**

#### Example 2: PROPERTIES Type (Glass Window)

```csv
MAT_Glass,MAT_Mat_glass,PROPERTIES,MAT_Glass,MAT_Glass__Cyberpunk,MAT_Glass__Mars,MAT_Glass__Pandora,"base_color:#00ffff;emission_strength:0.5","base_color:#ff4500;roughness:0.3","base_color:#00ff7f;emission_strength:0.8"
```

- Set **Variant Type** to `PROPERTIES`
- Use suffix naming: `MaterialName__ThemeName`
- Fill **property columns** with overrides

---

### Step 3: Generate JSON Files

Run the JSON generator:

```bash
npm run pipeline:generate-json
```

**Output:**
- `config/materials_roles.json` - Material → Role mapping
- `config/materials_variants.json` - Role → Theme materials + Properties

**Structure of materials_variants.json:**

```json
{
  "variants": {
    "Original": {
      "MAT_Mat_glass": "MAT_Glass"
    },
    "Cyberpunk": {
      "MAT_Mat_glass": "MAT_Glass__Cyberpunk"
    },
    // ... more themes
  },
  "properties": {
    "Cyberpunk": {
      "MAT_Mat_glass": {
        "base_color": "#00ffff",
        "emission_strength": "0.5"
      }
    },
    // ... more themes
  }
}
```

---

### Step 4: Copy to Blender Addon

```bash
# Windows
copy config\materials_roles.json tools\blender\addons\
copy config\materials_variants.json tools\blender\addons\

# Linux/Mac
cp config/materials_roles.json tools/blender/addons/
cp config/materials_variants.json tools/blender/addons/
```

---

### Step 5: Process in Blender

1. **Load JSON:** `1️⃣ Load JSON Configurations`
2. **Apply Roles:** `3️⃣ Apply Material Roles`
3. **Build Palette:** `4️⃣ Build MATERIAL_PALETTE`
4. **Generate Variants:** `5️⃣ Generate Variant Materials`
   - For **SWAP:** Creates new materials
   - For **PROPERTIES:** Duplicates base and applies property overrides
5. **Export:** `6️⃣ Export Collection as GLB`

---

### Step 6: Run Pipeline

```bash
npm run pipeline:process-all
```

This will:
1. Add KHR_materials_variants to GLBs
2. Optimize with DRACO
3. Generate low-res versions

---

## 📝 Examples

### Example 1: Neon Sign (PROPERTIES)

A neon sign that changes color and intensity per theme.

**CSV Entry:**

```csv
MAT_Neon_Sign,MAT_Mat_neon_sign,PROPERTIES,MAT_Neon_Sign,MAT_Neon_Sign__Cyberpunk,MAT_Neon_Sign__Mars,MAT_Neon_Sign__Pandora,"emission_strength:2.5;base_color:#ff00ff","emission_strength:1.8;base_color:#ff0000","emission_strength:3.0;base_color:#00ff00"
```

**Result in Blender:**
- Base material: `MAT_Neon_Sign` (Original)
- Addon creates:
  - `MAT_Neon_Sign__Cyberpunk` (pink glow, intensity 2.5)
  - `MAT_Neon_Sign__Mars` (red glow, intensity 1.8)
  - `MAT_Neon_Sign__Pandora` (green glow, intensity 3.0)

**In React:**
```javascript
// Switch to Cyberpunk theme
variantManager.selectVariant('Cyberpunk');
// Neon sign now glows pink with 2.5x intensity
```

---

### Example 2: Building Wall (SWAP)

A building wall that changes from concrete to different materials.

**CSV Entry:**

```csv
MAT_Building_Wall,MAT_Mat_building_wall,SWAP,MAT_Concrete_Clean,MAT_Metal_Panels_Cyber,MAT_Rock_Mars_Red,MAT_Organic_Pandora,"","",""
```

**Materials needed in Blender:**
- `MAT_Concrete_Clean` (original)
- `MAT_Metal_Panels_Cyber` (cyberpunk)
- `MAT_Rock_Mars_Red` (mars)
- `MAT_Organic_Pandora` (pandora)

**Result:**
Each theme uses a completely different material with unique shaders, textures, and properties.

---

### Example 3: Glass (PROPERTIES with Transparency)

```csv
MAT_Glass_Window,MAT_Mat_glass_window,PROPERTIES,MAT_Glass_Window,MAT_Glass_Window__Cyberpunk,MAT_Glass_Window__Mars,MAT_Glass_Window__Pandora,"base_color:#00ffff;roughness:0.1;transmission:0.95","base_color:#ff6347;roughness:0.3;transmission:0.85","base_color:#32cd32;roughness:0.2;transmission:0.9"
```

---

## 🎨 MATERIAL_PALETTE Usage

### What is MATERIAL_PALETTE?

A special object that holds **all materials** used in your scene, ensuring they're exported in the GLB.

### Structure

```
MATERIAL_PALETTE (Empty or Plane object)
├─ Material Slot 1: MAT_Glass
├─ Material Slot 2: MAT_Glass__Cyberpunk
├─ Material Slot 3: MAT_Glass__Mars
├─ Material Slot 4: MAT_Glass__Pandora
├─ Material Slot 5: MAT_Concrete
├─ Material Slot 6: MAT_Metal_Panels
└─ ... (all materials)
```

### How the Addon Uses It

**Step 4️⃣ Build MATERIAL_PALETTE:**
- Scans all objects in collection
- Collects all unique materials
- Assigns them to MATERIAL_PALETTE slots

**Step 6️⃣ Export:**
- MATERIAL_PALETTE is automatically included in export
- Ensures all variant materials are available in GLB

### Manual Setup (Optional)

If you want to create it manually:

1. Create an Empty or hidden Plane: `Add → Empty → Plain Axes`
2. Rename to: `MATERIAL_PALETTE`
3. Add material slots for all variants
4. Assign materials to slots
5. Hide from renders (if using Plane)

---

## ✅ Best Practices

### 1. Naming Conventions

**For SWAP:**
```
Original:  MAT_Concrete
Cyberpunk: MAT_Metal_Panels  (completely different name)
Mars:      MAT_Rock_Red
```

**For PROPERTIES:**
```
Original:  MAT_Glass
Cyberpunk: MAT_Glass__Cyberpunk  (suffix with __)
Mars:      MAT_Glass__Mars
```

### 2. Property Keys

Supported property keys for PROPERTIES type:

```
base_color          → #RRGGBB format
metallic            → 0.0 to 1.0
roughness           → 0.0 to 1.0
emission            → #RRGGBB format (emission color)
emission_strength   → 0.0+ (emission intensity)
transmission        → 0.0 to 1.0 (for glass)
ior                 → 1.0+ (index of refraction)
alpha               → 0.0 to 1.0 (transparency)
```

### 3. When to Use Each Type

**Use SWAP when:**
- Materials are fundamentally different (concrete vs metal vs organic)
- Different texture sets needed
- Completely different shader setups
- Large visual differences between themes

**Use PROPERTIES when:**
- Same material type, different appearance
- Color/tint variations
- Emission/glow intensity changes
- Roughness/metallic tweaks
- Maintaining shader complexity

### 4. Testing Workflow

1. **Test in Blender first:**
   - Create variant materials manually
   - Temporarily assign to objects
   - Verify appearance in Viewport
   - Switch back to Original before export

2. **Test after export:**
   - Run pipeline
   - Load in React
   - Switch themes using variant selector
   - Verify all materials switch correctly

### 5. Performance Considerations

**SWAP pros:**
- More flexible
- Independent materials

**SWAP cons:**
- More materials = larger file size
- More memory usage

**PROPERTIES pros:**
- Smaller file size (fewer materials)
- Faster theme switching
- Lower memory usage

**PROPERTIES cons:**
- Limited to property changes
- Can't change textures/shader structure

---

## 🐛 Troubleshooting

### Issue: Properties not applying

**Check:**
1. CSV has correct format: `property:value;property:value`
2. Property names are spelled correctly
3. Values are in correct format (#RRGGBB for colors, floats for others)

### Issue: SWAP materials not found

**Check:**
1. All theme materials exist in Blender
2. Material names match exactly in CSV
3. Materials are added to MATERIAL_PALETTE

### Issue: Variants not switching in React

**Check:**
1. GLB has KHR_materials_variants extension
2. Role IDs match between CSV and GLB
3. Variant names are correct
4. Using correct variant selector API

---

## 📚 Related Documentation

- [Quick Start Guide](./QUICK_START_AUTOMATED.md)
- [Complete Workflow](./WORKFLOW_AUTOMATED.md)
- [Blender Addon Guide](../../tools/blender/README.md)
- [Pipeline Tools](../../tools/pipeline/README.md)

---

**Last Updated:** 2025-01-18
