# 🚀 Deployment & Setup Guide

## 🛠️ Local Development

### Prerequisites
-   **Node.js**: v18.0.0+
-   **npm**: v9.0.0+

### Quick Start

1.  **Clone the repository**
    ```bash
    git clone https://github.com/hmosqueraturner/hektek-city.git
    cd hektek-city
    ```

2.  **Install dependencies**
    ```bash
    npm install
    ```

3.  **Configure Environment**
    Copy the example env file:
    ```bash
    cp .env.example .env.local
    ```
    *Note: You will need the Cloudflare R2 credentials to fetch assets. Contact the maintainer if you are a contributor.*

4.  **Run Dev Server**
    ```bash
    npm run dev
    ```
    Open `http://localhost:5173` to see the city.

## 📦 Build & Production

### Building for Production
This compiles the React app and optimizes assets.

```bash
npm run build
```

### Preview Production Build
Test the build locally before deploying.

```bash
npm run preview
```

## ☁️ Deployment

### Vercel (Recommended)
The project is optimized for Vercel.

1.  Connect your GitHub repository to Vercel.
2.  Import the project.
3.  **Build Command**: `npm run build`
4.  **Output Directory**: `dist`
5.  **Environment Variables**: Add your `.env` variables to the Vercel project settings.

### Cloudflare Pages
1.  Connect GitHub repo.
2.  **Build Command**: `npm run build`
3.  **Build Output Directory**: `dist`

## 🗄️ Asset Management (Cloudflare R2)

Assets (Models, HDRs, Textures) are stored in an R2 bucket to keep the repo light.

**Structure:**
```
your-r2-bucket/
├── models/
│   ├── standard/      # High quality models
│   └── low/           # Low poly fallbacks
├── hdr/               # Environment maps
└── textures/          # Shared textures
```

**Tools:**
We use custom scripts in `tools/` to manage these assets.
-   `npm run optimize:lowres`: Generates low-res versions of models.
-   `npm run pipeline:process-all`: Runs the full optimization pipeline.
