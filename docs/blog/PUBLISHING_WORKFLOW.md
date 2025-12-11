# 📝 Hektek City Blog Publishing Workflow

> **PROTOCOL: R2-FIRST PUBLISHING**
> This workflow ensures that blog content is updated without redeploying the application.

## 1. Authoring
All blog posts are authored in Markdown within this repository for version control and backup.

**Location:** `docs/blog/`
**Naming Convention:** `NNN-slug-name.md` (e.g., `001-vision-origins.md`)

## 2. The Cloudflare R2 Lifecycle
The application (`BlogScene.jsx`) does **not** read these files locally in production. It fetches them from your configured Cloudflare R2 bucket.

### Step-by-Step Publishing:

1.  **Draft**: Write your `.md` file in `docs/blog/`.
2.  **Commit**: Commit the file to this repo (Maintains history).
3.  **Upload to R2**:
    *   Use your tool (e.g., `nicky-bucket` or R2 dashboard).
    *   Target Path: `blog/posts/` (or your configured structure).
4.  **Update Config (R2)**:
    *   Download the current `blog.json` from R2.
    *   Add the new entry:
        ```json
        {
          "id": "new-post-id",
          "title": "The Title",
          "date": "2025-12-11",
          "contentUrl": "https://[YOUR-R2-DOMAIN]/blog/posts/NNN-slug-name.md",
          "summary": "...",
          "tags": ["AI", "Dev"]
        }
        ```
    *   Upload `blog.json` back to R2.

## 3. Verification
1.  Open [Hektek City Live](https://hektek-city.vercel.app/).
2.  Navigate to the Blog Scene.
3.  Verify the new card appears and the markdown loads correctly.

---
**Why this way?**
This decouples *content* from *code*. We can fix typos, add posts, or change images instantly without triggering a Vercel build.
