# 🏗️ HekTek City Architecture

**HEKTEK City** (v6.0+) is an AI-Native 3D application that converges React 19, Three.js, and Agentic AI into a "No-Deploy" content platform.

## 🧩 System Overview

The core paradigm is **"LIZA as the Controller"**. The UI is not just buttons; it is a bidirectional communication layer between the user and the agent.

```mermaid
graph TB
    subgraph "The App (Vite/React)"
        UI[LizaCockpit HUD]
        Scene[R3F 3D World]
        State[Zustand Store]
    end

    subgraph "The Intelligence"
        Speech[Web Speech API]
        Agent[LIZA (Vercel AI SDK)]
        Neuro[Neuro-Architect]
    end

    subgraph "The Content (R2)"
        Assets[GLB/Textures]
        Config[JSON Rules]
        Blog[Markdown Posts]
    end

    UI <-->|Voice/Text| Speech
    UI <-->|Intent| Agent
    
    Agent -->|Paint| Neuro
    Neuro -->|Materials| Scene
    
    Scene <-->|Stream| Assets
    State <-->|Fetch| Config
```

[👉 **See Detailed Event Loop & Sequence Diagrams**](./SYSTEM_DIAGRAMS.md)

## 🧱 Key Components

| Component | Responsibility | Status |
|-----------|---------------|--------|
| **LizaCockpit** | The Unified HUD. Integrates Chat, Voice, Navigation, and Tour controls. | ✅ Standard (v6) |
| **Neuro-Architect** | Generative material engine. Translates "Make it cyberpunk" into roughness/emissive maps. | ✅ Active |
| **MapRPG** | The main game-loop orchestrator. Handles physics and camera flight. | ✅ Active |
| **DynamicBuildingModel** | Loads GLB models and applies runtime materials based on themes. | ✅ Active | 
| **LODTerrain** | Manages terrain loading (switching `HTLand_2k` -> `HTLand_4k`) for performance. | ✅ Active |
| **useRemoteConfig** | The "No-Deploy" engine. Fetches configuration from Cloudflare R2 first, falling back to local only in dev. | ✅ Core |
| **useTour** | Manages guided tour state and camera orchestration. | ✅ Active |

## 🔄 Data Methods

### 1. The "Free-Form" Painting Flow
We no longer use static theme presets (like v3.0).
1.  **User**: "I want a radioactive swamp."
2.  **LIZA**: Analyses -> `{ primary: #2f4, roughness: 0.9, water: "sludge" }`.
3.  **Neuro-Architect**: Injects these values directly into the `DynamicMaterial` instances of 200+ meshes.

### 2. R2 Content Hydration
Upon load, the app hydrates from R2:
-   `blog.json` → Populates the Blog Scene.
-   `buildings-config.json` → Defines where camera targets are.
-   **Benefit**: We can add a building or a blog post without a Vercel build.

## 📂 Configuration (JSON)

We maintain a "Single Source of Truth" in JSON, hosted on R2.
-   `content-sources.json`: Defines the R2 bucket endpoints.
-   `blog.json`: The index of all blog posts (remote or local).
-   `tours-config.json`: The script for LIZA's guided tours.
