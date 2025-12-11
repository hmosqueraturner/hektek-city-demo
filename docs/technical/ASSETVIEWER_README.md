# Component Documentation Index

## AssetViewer Component

### Overview
Universal asset viewer component for displaying R2 assets with HEKTEK branding.

**Status**: ✅ Implemented (2025-12-02)  
**Location**: `src/components/AssetViewer/`  
**Testing**: `/about` route (`AboutMeScene.jsx`)

---

### Quick Links

**Technical Documentation**:
- [Technical Analysis](../features/ASSETVIEWER_ANALYSIS.md) - Architecture review, mockups, design decisions
- [Implementation Walkthrough](./ASSETVIEWER_WALKTHROUGH.md) - Complete implementation details, testing guide
- [Git Deliverables](./ASSETVIEWER_GIT_DELIVERABLES.md) - Commit messages, PR description, changelog

**Additional Resources**:
- [LIZA Image Assets Guide](../features/LIZA_IMAGE_ASSETS_GUIDE.md) - Branding assets for LIZA
- [Visual Mockups](../assets/screenshots/assetviewer/) - Design mockups

---

### Component Structure

```
src/components/AssetViewer/
├── AssetViewer.jsx          # Main component (mode switcher)
├── AssetViewerFrame.jsx     # Branded frame with glassmorphism
├── SceneViewer.jsx          # 3D GLB rendering (R3F)
├── CarouselViewer.jsx       # Image carousel (Ant Design)
├── SingleImageViewer.jsx    # Single image display
└── index.js                 # Barrel exports
```

---

### Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| **scene** | 3D GLB/GLTF models | Interactive 3D assets |
| **set** | Image carousel | Photo galleries, portfolios |
| **single** | Single image | Banners, featured images |

---

### Usage

```jsx
import { AssetViewer } from '../components/AssetViewer';

// 3D Model
<AssetViewer mode="scene" sceneUrl="robotHT.glb" title="Robot" />

// Image Carousel
<AssetViewer mode="set" images={['img1.png', 'img2.png']} title="Gallery" />

// Single Image
<AssetViewer mode="single" imageUrl="banner.png" title="Banner" />
```

---

### R2 Path Handling

**Automatic URL Construction**:
- Models: `https://assets.hectortechno.com/models/quality/standard/{filename}`
- Images: `https://assets.hectortechno.com/img/show/{path}`

**Example**:
```jsx
// Input
sceneUrl="robotHT.glb"

// Output
"https://assets.hectortechno.com/models/quality/standard/robotHT.glb"
```

---

### Features

✅ **HEKTEK Branding**:
- Glassmorphism backgrounds (`rgba(0, 0, 0, 0.6)`)
- Neon cyan borders (`#00F0FF`)
- CreativeIcons integration
- Hover effects

✅ **3D Rendering** (Scene Mode):
- React Three Fiber + @react-three/drei
- OrbitControls (drag to rotate, scroll to zoom)
- Environment lighting (preset: "city")
- Dual directional lights (cyan + red themed)

✅ **Image Carousel** (Set Mode):
- Custom arrow controls (← →)
- Dot indicators (cyan active)
- Manual navigation (autoplay off by default)
- Image constraints (maxHeight: 450px)
- Responsive padding

✅ **Single Image** (Single Mode):
- Centered display
- Aspect ratio preservation
- Container-aware sizing
- Cyan shadow effect

---

### Dependencies

**Existing** (no new packages required):
- `react`
- `@react-three/fiber`
- `@react-three/drei`
- `antd`

---

### Testing

**Route**: `http://localhost:5173/about`

**Test Cases**:
1. ✅ 3D model loads and rotates (robotHT.glb)
2. ✅ Carousel displays 5 LIZA images with controls
3. ✅ Single image shows LIZA banner
4. ✅ Responsive behavior on resize
5. ✅ No console errors
6. ✅ No breaking changes to production

---

### Statistics

- **Files created**: 6
- **Lines of code**: ~180
- **Dependencies added**: 0
- **Breaking changes**: 0
- **Implementation time**: ~5 hours

---

### Future Enhancements

**Possible**:
- Fullscreen mode toggle
- Download button for assets
- Lightbox for images
- Animation controls for 3D models
- Integration with ProjectsConsole
- LIZA chat asset responses

---

### Related Components

- **ViewerLayout** - Container wrapper (used in testing)
- **CreativeIcons** - Icon system (branding)
- **AboutViewer** - Production viewer (untouched)
- **AboutMeScene** - Testing integration

---

### Maintenance Notes

**Path Structure**:
- Models MUST be in `/models/quality/standard/`
- Images MUST be in `/img/show/`
- Component constructs full URLs automatically

**Styling**:
- Frame height defaults to `400px`
- Width defaults to `100%`
- Both are configurable via props

**Performance**:
- `useGLTF` caches models automatically
- Images load on-demand
- No lazy loading implemented yet

---

**Last Updated**: 2025-12-02  
**Status**: Production Ready (Testing Phase)
