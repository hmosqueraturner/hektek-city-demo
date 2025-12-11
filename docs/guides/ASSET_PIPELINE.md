# 📦 Asset Pipeline & Content Guide

> **Vision**: "No-Code Updates". We add content by uploading files, not by deploying code.

## 1. The R2 "Asset Lake"
All binary assets live in an external Cloudflare R2 bucket (`hector-assets`). This keeps our git repo small (<50MB) while the site serves gigabytes of content.

**Asset Types:**
*   **Models**: `.glb` (Draco Compressed).
*   **Textures**: `.jpg` / `.ktx2` (Basis Universal).
*   **Content**: `.md` (Blog posts).

## 2. Adding a New Building
To add a new interactive building (e.g., "Podcast Studio") without code:

1.  **Model**: Upload `podcast_studio.glb` to R2 `/models/`.
2.  **Config**: Edit `src/config/buildings-config.json` (on R2):
    ```json
    {
      "id": "podcast",
      "modelUrl": "https://assets.hektek.io/models/podcast_studio.glb",
      "position": [10, 0, -5],
      "interaction": "open_link",
      "payload": "https://spotify.com/..."
    }
    ```
3.  **Refresh**: Reload the site. `DynamicBuildingModel` will fetch the new entry.

## 3. The Blog Pipeline
See [Publishing Workflow](../blog/PUBLISHING_WORKFLOW.md).

## 4. Optimizing Assets
Run all GLBs through `gltf-transform` before uploading:
`gltf-transform optimize input.glb output.glb --compress draco`
