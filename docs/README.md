
# 🚀 HEKTEK City — Interactive 3D Portfolio (CTO & Engineering Manager)

An **interactive 3D portfolio** built with **React Three Fiber**, **Blender / Omniverse**, and **Vite**.  
This project showcases a professional journey as a **CTO / Engineering Manager** and an enthusiast of **AI, games and space** — presented as an immersive, gamified 3D experience.


---

## Table of Contents

- [Concept](#concept)
- [Preview](#preview)
- [Links](#links)
- [Tech Stack](#tech-stack)
- [Quick Start](#quick-start)
- [Development Notes](#development-notes)
- [Project Structure](#project-structure)
- [Models & GLTF conventions](#models--gltf-conventions)
- [Deployment (Vercel)](#deployment-vercel)
- [Testing & QA](#testing--qa)
- [Performance tips](#performance-tips)
- [Roadmap & Future Features](#roadmap--future-features)
- [Contributing](#contributing)
- [License](#license)
- [Credits](#credits)

---

## Concept

This portfolio is not a traditional website. It is an **interactive 3D RPG-style map**, where each building represents a section of the portfolio:

- 🟧 **Skills** → Technical and soft skills.  
- 🟩 **Experience** → Professional history and projects.  
- 🟦 **Vision** → Goals, aspirations and future plans.

Each building is modeled in **Blender / Omniverse** and exported as **GLB / GLTF**. Special empties like `FocusEmpty` and `InsideEmpty` are used to define camera positions and entry points for navigation.

Visitors can:
- Explore a global map view.
- Click a building to focus the camera on it.
- Enter a building to view details and content.
- Return to the map at any time.

Smooth animations and camera transitions are powered by `react-spring`.

---

## Preview

![RPG Map Mock](https://i.ibb.co/jvqcfyXZ/hektek-city-beta.jpg)  
![Skills Building Mock](https://i.ibb.co/Y74vjMRq/mock-skills.jpg)  
![Experience Building Mock](https://i.ibb.co/ns6hPbWb/mock-experience.jpg)  
![Vision Building Mock](https://i.ibb.co/Z6JyHKSr/mock-vision.jpg)

*(Reference mocks — will be replaced with production screenshots and renders.)*

---

## Links

- 🌍 Main website: [hectortechno.com](https://www.hectortechno.com/)  
- 💼 Project portfolio: [hectortechno.dev](https://www.hectortechno.dev/)  
- 📦 GitHub repository: [github.com/hmosqueraturner/hektek-city](https://github.com/hmosqueraturner/hektek-city)  
- 🚀 Live demo (Vercel): [hektek-city.vercel.app](https://hektek-city.vercel.app/)

---

## Tech Stack

- **React** + **Vite** — frontend app & dev server.  
- **React Three Fiber** (Three.js) — 3D rendering.  
- **@react-three/drei** — helpers (useGLTF, OrbitControls, etc.).  
- **react-spring** — smooth camera and UI animations.  
- **Blender / NVIDIA Omniverse** — 3D modeling and scene assembly.  
- **Vercel** — hosting & automatic deployment.

---

## Quick Start

```bash
# Clone the repo
git clone https://github.com/hmosqueraturner/hektek-city.git
cd hektek-city

# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Preview production build locally
npm run preview
```

## Development Notes

- **🪐** Environment variables: use .env / .env.local for development-only keys (example: analytics keys, feature flags). Do not commit secrets.

- **🪐** Recommended: open the project in VSCode and enable extensions for React, Tailwind (if used), and the basic JS/TS tooling you prefer.

- **🪐** If you add new models, keep filenames consistent and export optimized GLTF/GLB (see Models & GLTF conventions
).

- **🪐** Animations & camera: camera target positions are defined by empties in the model (FocusEmpty, InsideEmpty). Make sure these nodes are present when exporting.

---

## 🚀 Deployment on Vercel

The project is pre-configured for **Vercel**:

1. Connect the repo to [Vercel](https://vercel.com/).  
2. Automatic deployment from `main` branch in GitHub.  
3. Basic configuration:
   - **Framework**: Vite  
   - **Build Command**: `npm run build`  
   - **Output Directory**: `public`  

Live demo available at:

[hektek-city.vercel.app](https://hektek-city.vercel.app/) 

---

## 🧩 Project Structure

📂
```ascii
src
├── components/                 # Reusable React components
│   ├── 📷 CameraControls.jsx   # Camera and navigation logic
│   ├── 💡 Lighting.jsx         # Light configuration
│   ├── 🏛️ Section.jsx          # Section buildings
│   ├── 🖥️ UIOverlay.jsx        # HTML/CSS UI overlay
│   └── 🖥️ MapRPG.jsx           # Main RPG-style scene
│
├── models/                     # 3D models converted from Blender
│   ├── 🏠 Skills.jsx            # Skills building (GLTF)
│   └── (other buildings: Experience, Vision…)
│
├── ⚛️ App.jsx                   # Root App
└── ▶️ main.jsx                  # Vite/React entry point


```

---

## 🧪 Tests y QA

While the project is mainly visual, recommended checks include:

- **UI tests**: navigation, camera transitions, overlays.
- **Cross-browser compatibility**: Chrome, Firefox, Edge.
- **3D model loading**: ensure correct paths `/public/models/*.glb`.
- **Performance testing**: Lighthouse + WebGL profiler.

---

## 📌 Roadmap & Future Features

- **🎮**  RPG-style navigation with keyboard/joystick.

- **🪐** I"Space exploration" exploration mode.

- **🤖** Dedicated AI projects section with live demos.

- **🖼️** Real renders and screenshots of the portfolio.


---

## 📜 Licencia

This project is intended for professional use.
If you’d like to adapt this concept, feel free to reach out — I’d be happy to help you create your own version ✨.

---

## ✍️ Crafted with passion by [Héctor](https://www.hectortechno.com/) 
<img src="https://i.ibb.co/3mQh29gz/logo.png" alt="Thank You!" border="0">
