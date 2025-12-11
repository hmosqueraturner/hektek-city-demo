# Documentation Overhaul & Blog Integration Plan

## 1. Analysis
The "Hektek-city" project is a sophisticated AI-native 3D experience relying on a "No-Deploy Update" philosophy using Cloudflare R2 for assets and configuration.

### Key Constraints & Requirements
1.  **Blog Architecture**:
    - **Source of Truth**: `docs/blog/*.md` in this repo (for versioning/authoring).
    - **Runtime Source**: Cloudflare R2. The application (`BlogScene.jsx`) fetches `blog.json` and markdown content from R2.
    - **Deployment**: Updates to the blog (new posts, config changes) are done by uploading to R2, **NOT** by redeploying the application.
2.  **Documentation Strategy**:
    - The `docs/` folder is the staging area for the public `hektek-city-demo` repository.
    - It must ideally represent the project's current state: AI-Native, 3D Immersive, Cloud-First.
3.  **Future Vision**:
    - Documentation should hint at or prepare for "Hektek-City G-Cloud" (Vertex AI, Semantic Search).

## 2. Proposed Changes

### A. Documentation Structure (`docs/`)
Rearrange `docs/` to serve as a high-quality public documentation hub:
- `docs/README.md`: Central index.
- `docs/architecture/`: System diagrams, architectural decisions (e.g., The R2 "No-Deploy" pattern).
- `docs/features/`: Deep dives into LIZA, Neuro-Architect.
- `docs/blog/`: **Authoring space** for blog posts.
- `docs/technical/`: Protocols (like this one), API specs.

### B. Blog Implementation (`docs/blog/`)
1.  **Directory**: Create `docs/blog/`.
2.  **Content**: Create initial markdown posts here (e.g., `001-hektek-city-origins.md`).
3.  **Workflow Documentation**: Create `docs/blog/README.md` explaining the publishing flow:
    *   *Draft in `docs/blog/`* -> *Commit to Git* -> *Upload `.md` to R2* -> *Update `blog.json` in R2*.
4.  **Configuration**: Review `src/config/blog.json` to ensure it matches the R2 schema, but **do not switch the app to use local config**.

### C. Major `README.md` Overhaul
The root `README.md` will become the definitive "Sales Page" for the project's technical capability:
- **Hero Section**: 3D + AI convergence.
- **Tech Stack**: React 19, R3F, Gemini 2.0, R2.
- **Key Differentiators**:
    - *LIZA Consciousness* (Agentic AI).
    - *Neuro-Architect* (Real-time generative themes).
    - *Cloud-Native Content* (The R2 architecture).
- **Roadmap**: Tease "Hektek-City G-Cloud".

## 3. Implementation Steps

1.  **Restructure `docs/`**: move existing files to `architecture/` and `technical/` as appropriate.
2.  **Create Blog Authoring Space**:
    - `docs/blog/`
    - `docs/blog/001-vision-origins.md` (Draft)
    - `docs/blog/PUBLISHING_WORKFLOW.md` (The guide for the human developer)
3.  **Update Root `README.md`**: Rewrite to be professional, visually striking, and accurate to the v5.0+ era.
4.  **Clean up**: Ensure no broken links in the new `README.md`.

## 4. User Review Required
- **Approval to Execute**: Confirm this plan aligns with the R2 strategy.
- **Note**: I will **not** modify `BlogScene.jsx` logic, only ensure the *documentation* about how to use it is correct.
