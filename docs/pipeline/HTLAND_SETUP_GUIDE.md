# 🗺️ HTLAND Setup Guide (v2.2.0)

**Version:** 2.0.0
**Date:** 2025-11-20

---

## 📁 File Structure & Locations

### Local Development (Public Assets)

Place your optimized HTLand terrain files in:

```
public/
└── assets/
    └── models/
        └── quality/
            └── standard/
                ├── HTLand_1k.glb    (~15MB)
                ├── HTLand_2k.glb    (~30MB)
                ├── HTLand_4k.glb    (~60MB)
                └── HTLand_8k.glb    (~150MB)
```

### Cloudflare R2 Production (CDN)

Upload the same files to R2 with this structure:

```
your-r2-bucket/
└── models/
    └── quality/
        └── standard/
            ├── HTLand_1k.glb
            ├── HTLand_2k.glb
            ├── HTLand_4k.glb
            └── HTLand_8k.glb
```

---

## 🎨 Material Requirements

Each GLB file MUST contain these materials with **exact names**:

1. **MAT_Mat_Terrain** - Ground/terrain material
2. **MAT_Mat_Water** - Water bodies material

These materials are dynamically changed based on themes via `visual-states.json`.

---

## 🛠️ Blender Export Settings

When exporting from Blender:

### 1. Material Naming
- Rename terrain material to: `MAT_Mat_Terrain`
- Rename water material to: `MAT_Mat_Water`

### 2. Export Settings
- **Format:** glTF Binary (.glb)
- **Include:** Selected Objects only
- **Transform:** +Y Up
- **Geometry:**
  - ✅ Apply Modifiers
  - ✅ UVs
  - ✅ Normals
  - ✅ Tangents (if using normal maps)
  - ✅ Vertex Colors (if needed)

### 3. Materials
- ✅ Materials
- Textures: Embedded or Separate (depends on optimization)
- Images: Automatic format

### 4. Compression (for optimization)
- Enable Draco compression (especially for 4K and 8K)
- Compression level: 6-10 (higher = smaller file)

---

## 📊 Quality Levels Specification

| Quality | Resolution | File Size | Target Device | Polygons | Textures |
|---------|-----------|-----------|---------------|----------|----------|
| **1K** | 1024x1024 | ~15MB | Low-end Mobile | 10K-20K | 1K |
| **2K** | 2048x2048 | ~30MB | Mobile/Default | 30K-50K | 2K |
| **4K** | 4096x4096 | ~60MB | Desktop | 80K-120K | 4K |
| **8K** | 8192x8192 | ~150MB | High-end Desktop | 150K-250K | 8K |

### Optimization Tips

**For 1K (Mobile Low-end):**
- Aggressive decimation (reduce polygon count by 80%)
- 1K texture resolution
- Remove unnecessary detail
- Draco compression level: 10

**For 2K (Default/Mobile):**
- Moderate decimation (reduce by 50%)
- 2K textures
- Balance quality/performance
- Draco compression level: 8

**For 4K (Desktop):**
- Light decimation (reduce by 25%)
- 4K textures
- Keep most detail
- Draco compression level: 6

**For 8K (High-end):**
- Original quality or minimal decimation
- 8K textures
- Full detail preserved
- Optional compression

---

## 🔧 Environment Variables

Add to `.env.local` for local development:

```bash
# Leave empty for local-only development
VITE_R2_PUBLIC_BASE_URL=

# For R2 CDN (production):
# VITE_R2_PUBLIC_BASE_URL=https://your-bucket.r2.cloudflarestorage.com
# VITE_R2_MODELS_PATH=models
```

---

## 🧪 Testing Your Files

### 1. Test Locally

```bash
# 1. Place files in public/assets/models/quality/standard/
# 2. Start dev server
npm run dev

# 3. Open browser console and check for:
# - "Terrain load progress: 1" (loaded successfully)
# - No 404 errors for HTLand_*.glb
```

### 2. Test Quality Switching

1. Open the application
2. Use the **Terrain Quality** selector in the controls panel
3. Switch between 1K → 2K → 4K → 8K
4. Verify smooth loading and no errors

### 3. Test Progressive Loading

1. Start with 2K quality (default)
2. Wait 2-3 seconds
3. Watch browser console: "Terrain quality changed to: 4k"
4. Terrain should upgrade smoothly without flicker

### 4. Test Theme Changes

1. Switch themes (Cyberpunk, Mars, Pandora, etc.)
2. Verify terrain material colors change
3. Verify water material changes (color, emissive)
4. Check console for material application logs

---

## 📤 Uploading to Cloudflare R2

### Option A: Wrangler CLI

```bash
# Install Wrangler
npm install -g wrangler

# Configure
wrangler login

# Upload files
wrangler r2 object put your-bucket/models/quality/standard/HTLand_1k.glb --file=public/assets/models/quality/standard/HTLand_1k.glb
wrangler r2 object put your-bucket/models/quality/standard/HTLand_2k.glb --file=public/assets/models/quality/standard/HTLand_2k.glb
wrangler r2 object put your-bucket/models/quality/standard/HTLand_4k.glb --file=public/assets/models/quality/standard/HTLand_4k.glb
wrangler r2 object put your-bucket/models/quality/standard/HTLand_8k.glb --file=public/assets/models/quality/standard/HTLand_8k.glb
```

### Option B: Cloudflare Dashboard

1. Go to Cloudflare Dashboard → R2
2. Select your bucket
3. Navigate to `models/quality/standard/`
4. Upload HTLand_1k.glb, HTLand_2k.glb, HTLand_4k.glb, HTLand_8k.glb

### Option C: Automation Script

Create `tools/upload-terrain.sh`:

```bash
#!/bin/bash

BUCKET="your-bucket"
LOCAL_PATH="public/assets/models/quality/standard"
R2_PATH="models/quality/standard"

for quality in 1k 2k 4k 8k; do
  echo "Uploading HTLand_${quality}.glb..."
  wrangler r2 object put $BUCKET/$R2_PATH/HTLand_${quality}.glb \
    --file=$LOCAL_PATH/HTLand_${quality}.glb \
    --content-type="model/gltf-binary"
done

echo "✅ All terrain files uploaded!"
```

---

## 🔍 Troubleshooting

### Issue: 404 Error for HTLand_*.glb

**Solution:**
1. Verify files exist in `public/assets/models/quality/standard/`
2. Check file names match exactly (case-sensitive)
3. Restart dev server

### Issue: Materials Don't Change with Themes

**Solution:**
1. Open Blender and verify material names:
   - Must be exactly `MAT_Mat_Terrain` and `MAT_Mat_Water`
2. Re-export GLB files with correct names
3. Check `visual-states.json` has HTLand section

### Issue: Terrain Not Loading

**Solution:**
1. Open browser console
2. Look for errors
3. Verify LODTerrain component is used in MapRPG.jsx
4. Check `terrainQuality` state is set

### Issue: Progressive Loading Not Working

**Solution:**
1. Verify `enableProgressive={!isMobile}` in MapRPG.jsx
2. Check browser is not throttling (DevTools Network tab)
3. Ensure target file exists (e.g., HTLand_4k.glb)

### Issue: Files Too Large

**Solution:**
1. Enable Draco compression in Blender export
2. Reduce texture resolution
3. Decimate geometry (Modifier → Decimate)
4. Use texture compression tools (like `gltf-pipeline`)

---

## 🚀 Quick Start Checklist

- [ ] Created 4 optimized HTLand files (1K, 2K, 4K, 8K)
- [ ] Named materials correctly (MAT_Mat_Terrain, MAT_Mat_Water)
- [ ] Placed files in `public/assets/models/quality/standard/`
- [ ] Tested locally with `npm run dev`
- [ ] Verified quality switching works
- [ ] Verified theme changes work
- [ ] (Optional) Uploaded to R2 for production
- [ ] (Optional) Updated `.env.local` with R2 URL

---

## 📝 Related Documentation

- [LODTerrain Component](../features/LOD_TERRAIN.md)
- [Visual States Configuration](../features/RUNTIME_MATERIALS_GUIDE.md)
- [Blender Workflow](BLENDER_WORKFLOW_UPDATED.md)
- [R2 CDN Setup](../deployment/R2_CDN_SETUP.md)

---

*Last Updated: 2025-11-20*
