# 🏗️ HEKTEK City Architecture

**HEKTEK City** is built on a modular, performance-first architecture designed to handle complex 3D scenes in a web browser.

## 🧩 System Overview

The application is split into clear layers to separate concerns between the UI, the 3D rendering engine, and the state management logic.

```mermaid
graph TB
    A[User Interface] --> B[React App Layer]
    B --> C[3D Rendering Layer]
    B --> D[State Management]
    B --> E[Asset Management]

    subgraph "UI System"
        A1[CockpitConsole]
        A2[ViewerLayout]
        A3[CreativeIcons]
    end
    
    A --> A1
    A --> A2
    A --> A3

    subgraph "3D Scenes"
        C1[MapRPG (City)]
        C2[ProjectsConsole (Arcade)]
        C3[BlogScene]
    end

    C --> C1
    C --> C2
    C --> C3

    D --> H[Camera State]
    D --> I[Theme State]
    D --> J[Tour State]

    E --> K[Cloudflare R2]
    E --> L[Local Fallback]

    B --> M[Custom Hooks]
    M --> N[useRemoteConfig]
    M --> O[useTour]
    M --> P[useDynamicMaterials]

    style A fill:#4299e1
    style B fill:#48bb78
    style C fill:#ed8936
    style E fill:#9f7aea
    style M fill:#f6ad55
    style A1 fill:#BB1111,stroke:#fff,stroke-width:2px
    style C2 fill:#00F0FF,stroke:#fff,stroke-width:2px
```

## 🧱 Key Components

| Component | Responsibility | Tech Stack |
|-----------|---------------|------------|
| **MapRPG** | The main scene orchestrator. Handles the "World" state. | React, R3F |
| **CockpitConsole** | Centralized UI control hub for navigation, themes, and tours. | React, AntD |
| **ProjectsConsole** | Standalone 3D Arcade scene for project showcases. | React, R3F |
| **ViewerLayout** | Centralized layout for 2D content overlays (Skills, Experience). | React, CSS |
| **DynamicBuildingModel** | Loads GLB models and applies runtime materials based on themes. | R3F, Custom Hooks |
| **LODTerrain** | Manages terrain loading, switching between low and high res. | R3F, GLTF |
| **CameraControls** | Handles smooth camera transitions and "Focus" modes. | drei, Custom Logic |
| **useRemoteConfig** | Hybrid data fetching from Cloudflare R2 with local fallback. | Custom Hook |
| **useTour** | Manages guided tour state and camera orchestration. | Custom Hook |

## 🔄 Core Systems

### 1. Runtime Materials System
Allows instant theme switching by modifying material properties at runtime.
> 👉 **See [Theming Guide](guides/THEMING.md)** for details.

### 2. Progressive Terrain Loading (LOD)
To ensure fast initial load times, we use a Level of Detail (LOD) strategy for the massive terrain models.
1.  **Initial Load**: Request `HTLand_2k.glb` (Low Res).
2.  **Render**: Scene appears quickly.
3.  **Background**: Request `HTLand_4k.glb` (High Res).
4.  **Swap**: Once loaded, seamlessly swap the models.

### 3. Config-Driven Content
Most behavior can be changed without touching code via JSON files in `src/config/`.
> 👉 **See [Content Guide](guides/CONTENT.md)** for details.
