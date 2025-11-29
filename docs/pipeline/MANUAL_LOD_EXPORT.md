# 🎨 Manual LOD Export from Blender

**For when you want full control over each quality level**

---

## Preparation

1. Open Blender
2. Import your HTLand_8k.glb: `File → Import → glTF 2.0 (.glb/.gltf)`
3. Verify materials are named:
   - `MAT_Mat_Terrain`
   - `MAT_Mat_Water`

---

## Export 8K (Original)

**Settings:**
- `File → Export → glTF 2.0 (.glb/.gltf)`
- **Format:** glTF Binary (.glb)
- **Include:** Selected Objects (or All if nothing selected)
- **Transform:** +Y Up
- **Geometry:**
  - ✅ Apply Modifiers
  - ✅ UVs
  - ✅ Normals
  - ✅ Tangents
  - ✅ Vertex Colors
- **Materials:**
  - ✅ Materials
  - Images: Automatic
- **Compression:**
  - ✅ Draco mesh compression
  - Compression level: **6**
  - Position quantization: **14 bits**
  - Normal quantization: **10 bits**
  - Texcoord quantization: **12 bits**

**Export as:** `HTLand_8k.glb`

**Expected size:** ~150MB

---

## Export 4K (High Quality)

### Step 1: Duplicate Scene
- Right-click on Scene → Duplicate Scene (Linked Data)
- Rename to "HTLand_4K"

### Step 2: Decimate Geometry (25%)
1. Select all terrain objects (Select → All)
2. Add Decimate Modifier:
   - Modifiers → Add Modifier → Decimate
   - Ratio: **0.75** (keep 75%)
   - Collapse Type: **Collapse**
   - ✅ Triangulate
3. Apply modifier: `Ctrl+A → Apply All Modifiers`

### Step 3: Resize Textures
1. Open Shader Editor
2. For each Image Texture node:
   - Select the image
   - Image → Resize
   - Width: **4096**
   - Height: **4096**
   - Click OK
3. Save all images: `Image → Save All Images`

### Step 4: Export
- Same settings as 8K but:
  - Compression level: **7**
  - Position quantization: **12 bits**

**Export as:** `HTLand_4k.glb`

**Expected size:** ~60MB

---

## Export 2K (Medium Quality - Default)

### Step 1: Duplicate Scene
- Duplicate from original (not from 4K)
- Rename to "HTLand_2K"

### Step 2: Decimate Geometry (50%)
1. Select all terrain objects
2. Add Decimate Modifier:
   - Ratio: **0.5** (keep 50%)
   - ✅ Triangulate
3. Apply modifier

### Step 3: Resize Textures
- Resize all to **2048 x 2048**

### Step 4: Export
- Compression level: **8**
- Position quantization: **11 bits**
- Normal quantization: **8 bits**

**Export as:** `HTLand_2k.glb`

**Expected size:** ~30MB

---

## Export 1K (Low Quality - Mobile)

### Step 1: Duplicate Scene
- Duplicate from original
- Rename to "HTLand_1K"

### Step 2: Decimate Geometry (80% reduction)
1. Select all terrain objects
2. Add Decimate Modifier:
   - Ratio: **0.2** (keep 20%)
   - ✅ Triangulate
   - ✅ Use Collapse
3. Apply modifier

### Step 3: Aggressive Texture Reduction
- Resize all to **1024 x 1024**
- Consider reducing texture quality:
  - Image → Pack → Unpack
  - Save with lower quality JPEG/PNG

### Step 4: Export
- Compression level: **10** (maximum)
- Position quantization: **10 bits**
- Normal quantization: **7 bits**
- Texcoord quantization: **8 bits**

**Export as:** `HTLand_1k.glb`

**Expected size:** ~15MB

---

## Verification Checklist

After exporting all 4 versions:

- [ ] All files have correct names (HTLand_1k.glb, HTLand_2k.glb, HTLand_4k.glb, HTLand_8k.glb)
- [ ] File sizes are approximately:
  - 1K: 10-20MB
  - 2K: 25-35MB
  - 4K: 50-70MB
  - 8K: 120-180MB
- [ ] Materials are named correctly (check in Blender before export):
  - `MAT_Mat_Terrain`
  - `MAT_Mat_Water`
- [ ] No export errors in Blender console

---

## Quick Test in Blender

Before exporting, test the decimation:

```python
# Blender Python Console
import bpy

# Get polygon count
total_polys = sum(len(obj.data.polygons) for obj in bpy.data.objects if obj.type == 'MESH')
print(f"Total polygons: {total_polys:,}")

# Expected counts:
# 8K: 150,000 - 250,000 polygons
# 4K: 110,000 - 190,000 polygons (75%)
# 2K: 75,000 - 125,000 polygons (50%)
# 1K: 30,000 - 50,000 polygons (20%)
```

---

## Tips for Better Results

### Decimation
- **Use Planar mode** for flat areas (faster, better quality)
- **Use Collapse mode** for organic shapes (terrain)
- **Check "Symmetry"** if terrain is symmetrical
- **Preserve UV seams** to avoid texture stretching

### Textures
- **Use square textures** (1024x1024, 2048x2048)
- **Power of 2 sizes** for better GPU performance
- **Compress with TinyPNG** before packing in Blender
- **Use BC7 compression** for best quality/size ratio (in texture settings)

### Materials
- **Keep material names unchanged**
- **Don't delete unused materials** (might break references)
- **Test in viewport** before export (Material Preview mode)

---

## Troubleshooting

### Export takes too long
- Disable "Export All" and export selected objects only
- Lower Draco compression level (faster but larger files)

### Materials look different after export
- Check "Export Materials" is enabled
- Verify texture paths are correct
- Use "Embed Textures" option

### File size too large
- Increase Draco compression level
- Further reduce texture resolution
- Apply additional decimation

### Geometry artifacts after decimation
- Reduce decimation ratio (less aggressive)
- Use "Unsubdivide" modifier instead
- Apply "Smooth" modifier after decimation

---

## Batch Export Script (Optional)

If you do this often, save this script in Blender Text Editor:

```python
import bpy
import os

output_dir = "D:/export/"  # Change this
qualities = {
    '8k': (1.0, 8192, 6),
    '4k': (0.75, 4096, 7),
    '2k': (0.5, 2048, 8),
    '1k': (0.2, 1024, 10)
}

for quality, (ratio, tex_size, draco) in qualities.items():
    # Duplicate scene
    # Apply decimation
    # Resize textures
    # Export
    filepath = os.path.join(output_dir, f"HTLand_{quality}.glb")
    bpy.ops.export_scene.gltf(
        filepath=filepath,
        export_format='GLB',
        export_draco_mesh_compression_enable=True,
        export_draco_mesh_compression_level=draco
    )
    print(f"Exported {quality}")
```

---

*For automated workflow, use the Python script: [generate_htland_lods.py](../../tools/blender/generate_htland_lods.py)*
