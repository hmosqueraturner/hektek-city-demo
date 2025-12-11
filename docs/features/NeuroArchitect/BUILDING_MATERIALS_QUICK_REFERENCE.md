# Building Materials Quick Reference

## Material Assignment by Building

### 🎮 Experience Building
```
✓ MAT_Mat_build_base_silver  (Primary metallic structure)
✓ MAT_Mat_build_base_met      (Secondary metal)
✓ MAT_Mat_steel               (Steel elements)
✓ MAT_Mat_glaze2006           (Window glass)
```

### 🎯 Skills Building (Game Controller)
```
✓ MAT_Mat_ctrl_core           (Core structure)
✓ MAT_Mat_ctrl_button_bola    (Circular button)
✓ MAT_Mat_ctrl_button_triangle (Triangle button)
✓ MAT_Mat_ctrl_cruz           (D-pad cross)
✓ MAT_Mat_ctrl_antena         (Antenna elements)
✓ MAT_Mat_build_base_silver   (Base structure)
✓ MAT_Mat_steel003            (Steel frame)
```

### 👁️ Vision Building (Water/Glass)
```
✓ MAT_Mat_water_vis           (Water material)
✓ MAT_Mat_green_glass         (Green glass panels)
✓ MAT_Mat_build_sup           (Support structure)
✓ MAT_Mat_glaze_i             (Glass type 1)
✓ MAT_Mat_glaze_ii            (Glass type 2)
✓ MAT_Mat_steel003            (Steel frame)
```

### 📚 Docs Building (Spacecraft)
```
✓ MAT_Mat_docs_base           (Base hull)
✓ MAT_Mat_docs_casco          (Main body/shell)
✓ MAT_Mat_docs_ala_der        (Right wing)
✓ MAT_Mat_dosc_ala_izq        (Left wing)
✓ MAT_Mat_docs_tablero        (Dashboard/panel)
✓ MAT_Mat_docs_tablero_back   (Panel backing)
✓ MAT_Mat_docs_window         (Main window)
✓ MAT_Mat_docs_window_ii      (Secondary window)
✓ MAT_Mat_build_base_silver   (Base structure)
```

### 👤 About Building
```
✓ MAT_Mat_build_base_silver   (Primary structure)
✓ MAT_Mat_build_sup           (Support beams)
✓ MAT_Mat_facade_glass        (Facade glass)
✓ MAT_Mat_steel003            (Steel elements)
✓ MAT_Mat_glaze_ii            (Window glass)
```

### 💼 Projects Building
```
✓ MAT_Mat_build_base_silver   (Primary structure)
✓ MAT_Mat_build_sup           (Support structure)
✓ MAT_Mat_steel003            (Steel frame)
✓ MAT_Mat_glaze_ii            (Glass panels)
✓ MAT_Mat_facade_glass        (Facade glass)
```

### 📝 Blog Building
```
✓ MAT_Mat_build_base_silver   (Primary structure)
✓ MAT_Mat_build_sup           (Support structure)
✓ MAT_Mat_steel003            (Steel elements)
✓ MAT_Mat_facade_glass        (Glass facade)
✓ MAT_Mat_glaze_ii            (Window glass)
```

---

## Common Materials Used Across Buildings

### Structural Materials
- `MAT_Mat_build_base_silver` - Used by: Experience, Skills, Docs, About, Projects, Blog
- `MAT_Mat_build_sup` - Used by: Vision, About, Projects, Blog
- `MAT_Mat_steel003` - Used by: Skills, Vision, About, Projects, Blog

### Glass Materials
- `MAT_Mat_glaze_ii` - Used by: Vision, About, Projects, Blog
- `MAT_Mat_facade_glass` - Used by: About, Projects, Blog

---

## Material Naming Conventions

### Building-Specific Prefixes
- `docs_*` → Docs building materials
- `ctrl_*` → Skills building (controller) materials
- `water_*` → Vision building water materials

### Material Type Suffixes
- `*_glass` → Glass/transparent materials
- `*_glaze*` → Glazed glass variants
- `*_silver` → Silver/metallic materials
- `*_steel*` → Steel structural materials
- `*_window*` → Window materials
- `*_sup` → Support/structural materials
- `*_base` → Base/foundation materials

---

## Theme Color Palettes

### Default (Realistic)
```css
Primary:   #c0c0c0 (Silver)
Secondary: #5f5f5f (Dark gray)
Accents:   #a5a5a5 (Light gray)
Glass:     #4682b4 (Steel blue)
```

### Cyberpunk (Neon)
```css
Primary:   #ff6600 (Orange neon)
Secondary: #ff00ff (Magenta neon)
Accents:   #00ffff (Cyan neon)
Glass:     #ff1493 (Deep pink)
```

### Mars (Desert)
```css
Primary:   #8b0000 (Dark red)
Secondary: #ff4500 (Orange red)
Accents:   #cd853f (Peru)
Glass:     #ff8c00 (Dark orange)
```

### Pandora (Bioluminescent)
```css
Primary:   #00ff88 (Spring green)
Secondary: #00ffcc (Cyan)
Accents:   #32cd32 (Lime green)
Glass:     #7fffd4 (Aquamarine)
```

---

## Material Properties Guide

### Metallic Surfaces
```json
{
  "default": { "metalness": 0.8-1.0, "roughness": 0.48-0.58 },
  "cyberpunk": { "metalness": 0.9-0.95, "roughness": 0.1-0.2 },
  "mars": { "metalness": 0.3-0.6, "roughness": 0.8-0.9 },
  "pandora": { "metalness": 0.1-0.3, "roughness": 0.5-0.6 }
}
```

### Glass Surfaces
```json
{
  "default": { "metalness": 0-1.0, "roughness": 0.0-0.33, "opacity": 0.5 },
  "cyberpunk": { "metalness": 0.2-0.3, "roughness": 0.0-0.1, "opacity": 0.6 },
  "mars": { "metalness": 0.0, "roughness": 0.25-0.38, "opacity": 0.4 },
  "pandora": { "metalness": 0.0, "roughness": 0.08-0.15, "opacity": 0.7-0.85 }
}
```

### Emissive Properties
```json
{
  "default": { "emissive": "none" },
  "cyberpunk": { "emissiveIntensity": 0.4-0.65 },
  "mars": { "emissive": "none" },
  "pandora": { "emissiveIntensity": 0.6-0.9 }
}
```

---

## Usage Instructions

1. **Import the configuration:**
   ```javascript
   import visualStates from './config/visual-states-complete.json';
   ```

2. **Access building materials:**
   ```javascript
   const experienceMaterials = visualStates.buildings.Experience.materials;
   ```

3. **Get theme-specific properties:**
   ```javascript
   const cyberpunkSilver = visualStates.buildings.Experience
     .materials.MAT_Mat_build_base_silver.cyberpunk;
   ```

4. **Apply to Three.js material:**
   ```javascript
   material.color.set(cyberpunkSilver.color);
   material.metalness = cyberpunkSilver.metalness;
   material.roughness = cyberpunkSilver.roughness;
   if (cyberpunkSilver.emissive) {
     material.emissive.set(cyberpunkSilver.emissive);
     material.emissiveIntensity = cyberpunkSilver.emissiveIntensity;
   }
   ```

---

**File Location:** `D:\code\portfolio\hektek-city\src\config\visual-states-complete.json`
