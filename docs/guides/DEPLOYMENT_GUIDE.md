# 🚀 Deployment & Infrastructure Guide

**Stack Overview:**
*   **Frontend**: Vercel (Next.js / Vite Static).
*   **Backend**: Vercel Serverless Functions (`/api/liza`).
*   **Storage**: Cloudflare R2.
*   **Domains**: Cloudflare DNS.

## 1. Production Deployment (Vercel)
The `main` branch is automatically deployed by Vercel.

**Environment Variables:**
*   `GOOGLE_GENERATIVE_AI_KEY`: For Gemini 2.0.
*   `NEXT_PUBLIC_R2_URL`: The CDN root (e.g., `https://assets.hektek.io`).

## 2. Content Deployment (R2)
There is no "Deploy Button" for content. It is "Drag and Drop".
*   We use **Rclone** or the Cloudflare Dashboard to sync `docs/blog` and `src/config` to the bucket.

## 3. Disaster Recovery
If Vercel goes down (500 Errors):
1.  Check `LIZA-FEATURE.md` for API health.
2.  Verify `buildings-config.json` on R2 is valid JSON (trailing commas break the app).
3.  Rollback via `git revert` if it was a code change.

## 4. Local Development
`npm run dev` starts the Vite server.
*   It connects to the **Live R2 Bucket** by default (Read-Only).
*   To test asset changes, upload to a `/dev` folder in R2 first.
