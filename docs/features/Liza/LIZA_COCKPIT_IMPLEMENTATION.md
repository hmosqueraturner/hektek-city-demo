# LizaCockpit - Unified Control Dock Feature Guide

**Component**: `LizaCockpit.jsx`  
**Version**: 5.6.0+  
**Status**: ✅ Production  
**Category**: UI/UX Enhancement

---

## 🎯 Overview

**LizaCockpit** is a unified glassmorphic control dock that consolidates all cockpit controls and LIZA AI assistant into a single, premium interface positioned at the bottom-center of the screen.

### Purpose

Replace the fragmented control interface (separate `CockpitConsole` + `LizaChat`) with a cohesive, visually stunning control surface that:
- Centralizes all user interactions in one location
- Provides instant access to LIZA AI assistant
- Offers direct theme switching capability
- Maintains the cyberpunk aesthetic with glassmorphism

---

## 🎨 Visual Design

### Layout Structure

```
┌─────────────────────────────────────────────────────────────┐
│                                                               │
│  [🚀 Tour] [👁️ Toggle UI]  |  [⚡ LIZA ⚡]  |  [🎨 Theme] [🌍 Orbit]  │
│    Actions                |     Hero     |    Controls        │
│                                                               │
└─────────────────────────────────────────────────────────────┘
         ▲                        ▲                  ▲
      Izquierda              Centro              Derecha
```

### Sections

**Left Section** - Quick Actions:
- 🚀 **Tour Button**: Start/stop guided tour
- 👁️ **UI Toggle**: Show/hide building labels

**Center Section** - LIZA Hero:
- ⚡ **LIZA Button**: Activate AI assistant chat
- Breathing animation indicates consciousness
- Largest button with circular shape

**Right Section** - Controls:
- 🎨 **Theme Selector**: Switch environments (Original/Cyberpunk/Mars/Pandora)
- 🌍 **Orbit Button**: Return to general view

### Color Palette

| Color | Hex | Usage |
|-------|-----|-------|
| Neon Blue | `#00F0FF` | Primary highlights, active states |
| Neon Red | `#BB1111` | Secondary highlights, LIZA active |
| Dark BG | `rgba(11, 12, 16, 0.85)` | Dock background |
| Border Blue | `rgba(0, 240, 255, 0.3)` | Borders, dividers |

---

## 🧩 Component Architecture

### File Structure

```
src/components/Cockpit/
├── LizaCockpit.jsx           # Main unified dock
├── CockpitButton.jsx         # Reusable button component
├── LizaCockpitChat.jsx       # Integrated chat panel
└── LizaCockpit.css           # Glassmorphism styling
```

### Component Hierarchy

```
LizaCockpit
├── motion.div (Dock)
│   ├── .cockpit-section-left
│   │   ├── CockpitButton (Tour)
│   │   └── CockpitButton (UI Toggle)
│   ├── .cockpit-divider
│   ├── .cockpit-section-center
│   │   └── CockpitButton (LIZA Hero)
│   ├── .cockpit-divider
│   └── .cockpit-section-right
│       ├── Select (Theme)
│       └── CockpitButton (Orbit)
└── AnimatePresence
    └── LizaCockpitChat (conditional)
```

---

## ⚙️ Props Interface

### LizaCockpit Props

```typescript
interface LizaCockpitProps {
  // Navigation
  cameraMode: string;
  setCameraMode: (mode: string) => void;
  currentSection: {
    key: string;
    label: string;
    position: number[];
  } | null;
  
  // Actions
  startTour: () => void;
  stopTour: () => void;
  isTourPlaying: boolean;
  showLabels: boolean;
  setShowLabels: (show: boolean) => void;
  
  // Theme Control (NEW in v5.6.0)
  assetTheme: string | null;
  setAssetTheme: (theme: string | null) => void;
  
  // LIZA Integration
  lizaMessages: Array<{role: string, content: string}>;
  lizaIsLoading: boolean;
  onLizaMessage: (message: string) => void;
}
```

### CockpitButton Props

```typescript
interface CockpitButtonProps {
  icon: React.ReactNode;
  onClick: () => void;
  tooltip?: string;
  isActive?: boolean;
  disabled?: boolean;
  variant?: 'default' | 'hero';
  className?: string;
}
```

---

## 🎬 Animations

### 1. Dock Entrance

**Type**: Spring animation  
**Trigger**: Component mount  
**Duration**: ~300ms  
**Effect**: Slide-up from bottom with opacity fade-in

```javascript 
const dockVariants = {
  hidden: { y: 100, opacity: 0 },
  visible: {
    y: 0,
    opacity: 1,
    transition: {
      type: "spring",
      stiffness: 300,
      damping: 30,
      delay: 0.2
    }
  }
};
```

### 2. LIZA Breathing

**Type**: Keyframe animation  
**Duration**: 3s loop  
**Effect**: Pulsing glow effect

```css
@keyframes breathe {
  0%, 100% { box-shadow: 0 0 10px rgba(0, 240, 255, 0.3); }
  50% { box-shadow: 0 0 25px rgba(0, 240, 255, 0.6); }
}
```

### 3. Chat Emergence

**Type**: Spring animation  
**Trigger**: LIZA button click  
**Duration**: ~350ms  
**Effect**: Holographic slide-up with scale

```javascript
const chatVariants = {
  hidden: { y: 50, scale: 0.9, opacity: 0 },
  visible: {
    y: 0,
    scale: 1,
    opacity: 1,
    transition: {
      type: "spring",
      stiffness: 400,
      damping: 25
    }
  }
};
```

### 4. Button Interactions

**Hover**: Scale 1.05 + glow increase (150ms)  
**Tap**: Scale 0.95 (100ms)  
**Active**: Border color change (Neon Blue → Neon Red)

---

## 🎯 Usage Examples

### Basic Integration

```jsx
import LizaCockpit from './components/Cockpit/LizaCockpit';

function MapRPG() {
  const [cameraMode, setCameraMode] = useState('GENERAL');
  const [assetTheme, setAssetTheme] = useState(null);
  const { messages, sendMessage, isLoading } = useLizaChat();
  
  return (
    <>
      <Canvas>{/* 3D Scene */}</Canvas>
      
      <LizaCockpit
        cameraMode={cameraMode}
        setCameraMode={setCameraMode}
        startTour={() => startTour('full')}
        stopTour={stopTour}
        isTourPlaying={!!tour}
        showLabels={showLabels}
        setShowLabels={setShowLabels}
        currentSection={currentSection}
        assetTheme={assetTheme}
        setAssetTheme={switchTheme}
        lizaMessages={messages}
        lizaIsLoading={isLoading}
        onLizaMessage={sendMessage}
      />
    </>
  );
}
```

### Custom Button

```jsx
import CockpitButton from './components/Cockpit/CockpitButton';
import { CustomIcon } from './components/CreativeIcons';

<CockpitButton
  icon={<CustomIcon size={28} color="#00F0FF" />}
  onClick={() => console.log('Clicked')}
  tooltip="Custom Action"
  isActive={isActive}
  variant="default"
/>
```

---

## 🎨 Styling Guidelines

### Glassmorphism Requirements

For any custom additions to the dock:

```css
/* Required for glassmorphism */
background: rgba(11, 12, 16, 0.85);
backdrop-filter: blur(24px) saturate(180%);
-webkit-backdrop-filter: blur(24px) saturate(180%);

/* Required for branding */
border: 1px solid rgba(0, 240, 255, 0.3);
box-shadow: 
  0 8px 32px rgba(0, 0, 0, 0.4),
  0 0 40px rgba(0, 240, 255, 0.15);
```

### Icon Sizing

| Variant | Size | Use Case |
|---------|------|----------|
| Small | 24px | Compact buttons |
| Default | 28px | Standard actions |
| Medium | 36px | Important actions (Orbit) |
| Hero | 48px | Center button (LIZA) |
| Large | 52px | Special emphasis |

---

## 📱 Responsive Behavior

### Breakpoints

**Desktop** (> 768px):
- Full dock with all buttons
- Theme selector width: 140px
- Gap: 16px between sections

**Tablet** (480px - 768px):
- Slightly reduced padding
- Theme selector maintained
- Gap: 12px

**Mobile** (< 480px):
- Dividers hidden
- Reduced gaps (8px)
- Theme selector  becomes scrollable if needed

### Centering Strategy

```css
/* Works on all screen sizes */
.liza-cockpit-dock {
  position: fixed;
  left: 50%;
  transform: translateX(-50%);
  
  /* Prevent overflow */
  max-width: 95vw;
}
```

---

## 🔧 Customization Guide

### Adding a New Button

1. **Import icon** from `CreativeIcons.jsx`
2. **Add to appropriate section** (left/right)
3. **Implement handler** function
4. **Add tooltip** for accessibility

```jsx
// Example: Add "Settings" button
<CockpitButton
  icon={<SettingsIcon size={28} color="#00F0FF" />}
  onClick={handleSettings}
  tooltip="Settings"
  isActive={isSettingsOpen}
/>
```

### Changing Theme Options

Edit the theme array in `LizaCockpit.jsx`:

```javascript
// Current themes
{['Original', 'Cyberpunk', 'Mars', 'Pandora'].map(...)}

// Add new theme
{['Original', 'Cyberpunk', 'Mars', 'Pandora', 'Desert'].map(...)}

// Update getThemeStyle() function to include Desert icon/color
```

### Custom Animations

Override Framer Motion variants:

```javascript
const customDockVariants = {
  hidden: { y: 200, opacity: 0, scale: 0.8 },
  visible: {
    y: 0,
    opacity: 1,
    scale: 1,
    transition: { type: "spring", stiffness: 500 }
  }
};

<motion.div variants={customDockVariants} ...>
```

---

## 🧪 Testing

### Visual Tests

- [ ] Dock renders at bottom-center
- [ ] All buttons display correct icons
- [ ] Breathing animation runs smoothly
- [ ] Hover states trigger glow effects
- [ ] Active states show red border

### Functional Tests

- [ ] Tour button starts/stops tour
- [ ] UI toggle shows/hides labels
- [ ] LIZA button opens chat
- [ ] Chat emerges with animation
- [ ] Theme selector changes environment
- [ ] Orbit button returns to general view

### Browser Tests

- [x] Chrome 100+
- [x] Firefox 100+
- [x] Safari 14.5+
- [x] Edge 100+
- [x] iOS Safari 14.5+

---

## 🐛 Troubleshooting

### Issue: Dock not centered

**Symptom**: Dock appears offset to left/right  
**Solution**: Ensure CSS uses `left: 50%` + `transform: translateX(-50%)`

```css
.liza-cockpit-dock {
  left: 50% !important;
  transform: translateX(-50%) !important;
}
```

### Issue: Chat panel doesn't appear

**Symptom**: Clicking LIZA button does nothing  
**Diagnosis**: Check `isChatOpen` state and `AnimatePresence`

```jsx
// Verify state management
const [isChatOpen, setIsChatOpen] = useState(false);

// Verify AnimatePresence wrapper
<AnimatePresence>
  {isChatOpen && <LizaCockpitChat ... />}
</AnimatePresence>
```

### Issue: Theme selector doesn't work

**Symptom**: Selecting theme has no effect  
**Diagnosis**: Verify `setAssetTheme` prop and `switchTheme` function

```jsx
// In MapRPG.jsx
<LizaCockpit 
  assetTheme={assetTheme}
  setAssetTheme={switchTheme}  // Must be switchTheme, not setAssetTheme
/>
```

---

## 📚 Related Documentation

- [LIZA Feature Guide](./LIZA-FEATURE.md)
- [LizaCockpit Design Document](./LIZA_COCKPIT_DESIGN.md)
- [v5.6.0 Release Notes](../../releases/v5.6.0.md)
- [Architecture Overview](../../ARCHITECTURE.md)

---

## 🔄 Version History

- **v5.6.0** (2025-12-03): Initial release
  - Unified dock implementation
  - Theme selector integration
  - Holographic chat emergence

---

**Maintainer**: Hector Mosquera Turner  
**Status**: ✅ Production Ready  
**Last Updated**: December 3, 2025
