# LIZA Cockpit Design Document

**Feature**: Barra de Control Unificada (Unified Control Dock)  
**Component**: `LizaCockpit.jsx`  
**Date**: 2025-12-03  
**Status**: 🔵 Planning Phase  

---

## 1. Executive Summary

Este documento define el diseño completo del nuevo componente `LizaCockpit.jsx`, una **Barra de Control Unificada** (Dock) que se fijará en la parte inferior central de la pantalla. Este componente unificará el acceso a LIZA (el asistente de IA), funciones de navegación, y controles de UI en un solo punto de interacción premium y futurista.

### Objetivos del Diseño

1. **Centralización**: Consolidar controles dispersos en un Dock unificado
2. **Consistencia Visual**: Mantener el branding Neon Blue (#00F0FF) / Neon Red (#BB1111)
3. **UX Premium**: Glassmorphism, animaciones fluidas, y interacciones intuitivas
4. **No-Destructivo**: No modificar componentes existentes (`LizaChat.jsx`, `CockpitConsole.jsx`)

---

## 2. UX Analysis: Comportamiento y Estados de Animación

### 2.1 Estructura del Dock

El Dock estará dividido en **3 zonas**: Izquierda (Acciones Rápidas) | Centro (LIZA) | Derecha (Navegación)

```
┌─────────────────────────────────────────────────────────┐
│  [🚀 Tour]  [👁️ UI]  │  [⚡ LIZA]  │  [</> Input]  [🌍 Orbit]  │
└─────────────────────────────────────────────────────────┘
```

### 2.2 Animation States

#### Estado 1: Reposo (Default)
- Dock visible en la parte inferior central
- Botón LIZA: Pulsando suavemente (breathing animation)
- Glassmorphism activo: `backdrop-filter: blur(24px)`
- Altura: ~80px

#### Estado 2: LIZA Activado (Chat Desplegado)
- **Transición**: El chat emerge **hacia arriba** desde el botón central
- **Tipo**: Efecto "Holograma emergente" con scale + opacity
- **Duración**: 300ms con easing `cubic-bezier(0.34, 1.56, 0.64, 1)` (elastic)
- **Chat Panel**:
  - Posición: Centered above the dock
  - Dimensiones: 440px width × 600px height
  - Background: Glassmorphism con border neon
  - Animación de entrada: `translateY(100%) → translateY(0)` + `scale(0.8 → 1)`

#### Estado 3: Botones Hover
- Efecto de brillo (glow) aumentado
- Escalado sutil: `transform: scale(1.05)`
- Transición: 150ms

#### Estado 4: Botones Activos
- Border color change: Neon Blue → Neon Red
- Inner shadow pulsing
- Icono rotación o animación específica

### 2.3 Interacciones Clave

| Acción | Comportamiento |
|--------|----------------|
| Click en LIZA | Toggle chat panel (slide-up from bottom) |
| Click en Tour | Ejecutar `startTour('full')` |
| Click en Ocultar UI | Toggle `showLabels` state |
| Click en Input Manual | Navegar a sección específica o abrir input modal |
| Click en Orbit | Ejecutar `setCameraMode(STRINGS.GENERAL)` |

---

## 3. Component Mapping: Iconos de CreativeIcons.jsx

Análisis del archivo `src/components/CreativeIcons.jsx` revela que todos los iconos aceptan props consistentes:

```javascript
{ 
  size: Number,           // Default: 40
  color: String,          // Default: "#00F0FF" (Neon Blue)
  secondaryColor: String, // Default: "#BB1111" (Neon Red)
  style: Object          // Custom CSS
}
```

### 3.1 Mapeo de Funciones → Iconos

| Función | Icono Component | Justificación |
|---------|----------------|---------------|
| **Tour** | `RocketLaunchIcon` | Representa "lanzamiento" del tour automático |
| **Ocultar UI** | `TextToggleIcon` | Representa visibilidad de etiquetas de texto |
| **LIZA** | `LizaAwakeningIcon` | Icono custom de LIZA con consciousness core |
| **Input Manual** | `CodeIcon` | Representa entrada de comandos manuales |
| **Return to Orbit** | `OrbitIcon` | Representa regreso a vista general orbital |

### 3.2 Configuración de Tamaños

```javascript
const ICON_SIZES = {
  small: 28,      // Botones laterales (Tour, UI Toggle, etc.)
  medium: 40,     // Botón LIZA (default)
  large: 52       // Orbit icon (más prominence)
};
```

---

## 4. CSS/Tailwind Strategy: Glassmorphism y Z-Index

### 4.1 Glassmorphism Implementation

```css
.liza-cockpit-dock {
  /* Positioning */
  position: fixed;
  bottom: 32px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1000;

  /* Layout */
  display: flex;
  align-items: center;
  gap: 24px;
  padding: 16px 32px;
  
  /* Glassmorphism Core */
  background: rgba(11, 12, 16, 0.85);
  backdrop-filter: blur(24px) saturate(180%);
  -webkit-backdrop-filter: blur(24px) saturate(180%);
  
  /* Border & Shadow */
  border: 1px solid rgba(0, 240, 255, 0.3);
  border-radius: 28px;
  box-shadow: 
    0 8px 32px rgba(0, 0, 0, 0.4),
    0 0 40px rgba(0, 240, 255, 0.15),
    inset 0 1px 1px rgba(255, 255, 255, 0.1);
}
```

### 4.2 Z-Index Strategy

**Problema**: El Canvas 3D de React Three Fiber ocupa todo el viewport. Necesitamos asegurar que el Dock esté **por encima** sin romper la interacción con el canvas.

**Solución**:

```javascript
// Layer hierarchy
Canvas (z-index: auto, default stacking context)
  ↓
Html Overlays (z-index: 500-900) // Labels, tooltips
  ↓
LizaCockpit Dock (z-index: 1000)
  ↓
LizaChat Panel (z-index: 1001, when open)
```

**Reglas**:
- `pointer-events: auto` solo en el Dock y su contenido
- Canvas debe tener `pointer-events: auto` en su área de interacción
- Chat panel debe usar `pointer-events: auto` pero con un overlay backdrop que bloquea canvas interaction

### 4.3 Responsive Considerations

```css
/* Mobile Adaptation (future-proofing) */
@media (max-width: 768px) {
  .liza-cockpit-dock {
    bottom: 16px;
    padding: 12px 16px;
    gap: 12px;
    font-size: 0.9em;
  }
  
  .liza-chat-panel {
    width: 90vw;
    height: 70vh;
  }
}
```

### 4.4 Animaciones con Framer Motion

```javascript
// Dock entrance animation
const dockVariants = {
  hidden: { y: 100, opacity: 0 },
  visible: { 
    y: 0, 
    opacity: 1,
    transition: { 
      type: "spring", 
      stiffness: 300, 
      damping: 30 
    }
  }
};

// Chat panel animation
const chatVariants = {
  hidden: { 
    y: 50, 
    scale: 0.9, 
    opacity: 0 
  },
  visible: { 
    y: 0, 
    scale: 1, 
    opacity: 1,
    transition: { 
      type: "spring", 
      stiffness: 400, 
      damping: 25 
    }
  },
  exit: { 
    y: 50, 
    scale: 0.9, 
    opacity: 0,
    transition: { duration: 0.2 }
  }
};
```

---

## 5. Implementation Plan (Step-by-Step)

### Phase 1: Component Structure Creation

#### Step 1.1: Create Base Components
**Files to CREATE**:

1. **`src/components/Cockpit/LizaCockpit.jsx`** (Main component)
   - Import dependencies (React, Framer Motion, Ant Design, CreativeIcons)
   - Define component structure (3 sections: Left, Center, Right)
   - Implement state management (isChatOpen, etc.)
   - Props interface: `{ cameraMode, setCameraMode, startTour, showLabels, setShowLabels, currentSection }`

2. **`src/components/Cockpit/CockpitButton.jsx`** (Reusable button)
   - Generic button wrapper for icons
   - Props: `{ icon, onClick, tooltip, isActive, size, variant }`
   - Hover/active states
   - Framer Motion animations

3. **`src/components/Cockpit/LizaCockpit.css`** (Styles)
   - Glassmorphism styles
   - Animation keyframes
   - Responsive breakpoints

#### Step 1.2: Create Chat Integration Component
**Files to CREATE**:

4. **`src/components/Cockpit/LizaCockpitChat.jsx`**
   - Copy from `src/components/LIZA/LizaChat.jsx` (NO modificar el original)
   - Adaptar para integración con Cockpit
   - Cambiar animación de entrada (slide-from-right → slide-from-bottom)
   - Props: Same as LizaChat + `onClose` callback

### Phase 2: Integration with MapRPG.jsx

#### Step 2.1: Import and Initialize
**Files to MODIFY**:

1. **`src/components/MapRPG.jsx`** (Lines to modify: ~50-60, ~630-640)
   - Import `LizaCockpit` component
   - Replace or comment out `<CockpitConsole />` (línea ~636)
   - Add `<LizaCockpit />` with props
   - Ensure LIZA hooks (`useLizaChat`, `useLizaTour`) are passed down

**Changes**:
```javascript
// Line ~55 (Imports section)
import LizaCockpit from "./Cockpit/LizaCockpit";

// Line ~636 (Replace CockpitConsole)
{/* Old: <CockpitConsole ... /> */}
<LizaCockpit
  cameraMode={cameraMode}
  setCameraMode={setCameraMode}
  startTour={() => startTour('full')}
  stopTour={stopTour}
  isTourPlaying={!!tour}
  showLabels={showLabels}
  setShowLabels={setShowLabels}
  currentSection={currentSection}
  // LIZA props
  lizaMessages={lizaMessages}
  lizaIsLoading={lizaIsLoading}
  onLizaMessage={sendLizaMessage}
/>
```

### Phase 3: Component Implementation Details

#### Step 3.1: LizaCockpit.jsx Structure
```jsx
import React, { useState } from 'react';
import { motion, AnimatePresence } from 'framer-motion';
import { Tooltip } from 'antd';
import {
  RocketLaunchIcon,
  TextToggleIcon,
  LizaAwakeningIcon,
  CodeIcon,
  OrbitIcon
} from '../CreativeIcons';
import CockpitButton from './CockpitButton';
import LizaCockpitChat from './LizaCockpitChat';
import { STRINGS } from '../../utils/stringValues';
import './LizaCockpit.css';

const LizaCockpit = ({
  cameraMode,
  setCameraMode,
  startTour,
  stopTour,
  isTourPlaying,
  showLabels,
  setShowLabels,
  currentSection,
  // LIZA
  lizaMessages,
  lizaIsLoading,
  onLizaMessage
}) => {
  const [isChatOpen, setIsChatOpen] = useState(false);

  return (
    <>
      {/* Main Dock */}
      <motion.div className="liza-cockpit-dock" variants={...}>
        {/* LEFT SECTION */}
        <div className="cockpit-section-left">
          <CockpitButton
            icon={<RocketLaunchIcon size={28} />}
            onClick={() => startTour()}
            tooltip="Start Tour"
            isActive={isTourPlaying}
          />
          <CockpitButton
            icon={<TextToggleIcon size={28} />}
            onClick={() => setShowLabels(!showLabels)}
            tooltip={showLabels ? "Hide Labels" : "Show Labels"}
            isActive={showLabels}
          />
        </div>

        {/* CENTER SECTION */}
        <div className="cockpit-section-center">
          <CockpitButton
            icon={<LizaAwakeningIcon size={48} />}
            onClick={() => setIsChatOpen(!isChatOpen)}
            tooltip="Activate LIZA"
            isActive={isChatOpen}
            variant="hero"
          />
        </div>

        {/* RIGHT SECTION */}
        <div className="cockpit-section-right">
          <CockpitButton
            icon={<CodeIcon size={28} />}
            onClick={() => {/* TODO: Manual input logic */}}
            tooltip="Manual Input"
          />
          <CockpitButton
            icon={<OrbitIcon size={36} />}
            onClick={() => setCameraMode(STRINGS.GENERAL)}
            tooltip="Return to Orbit"
          />
        </div>
      </motion.div>

      {/* Chat Panel (Appears above dock) */}
      <AnimatePresence>
        {isChatOpen && (
          <LizaCockpitChat
            messages={lizaMessages}
            isLoading={lizaIsLoading}
            onMessage={onLizaMessage}
            onClose={() => setIsChatOpen(false)}
          />
        )}
      </AnimatePresence>
    </>
  );
};

export default LizaCockpit;
```

#### Step 3.2: CockpitButton.jsx Structure
```jsx
import React from 'react';
import { motion } from 'framer-motion';
import { Tooltip } from 'antd';

const CockpitButton = ({ 
  icon, 
  onClick, 
  tooltip, 
  isActive = false, 
  variant = 'default' 
}) => {
  const buttonClass = `cockpit-button cockpit-button-${variant} ${isActive ? 'active' : ''}`;
  
  return (
    <Tooltip title={tooltip}>
      <motion.button
        className={buttonClass}
        onClick={onClick}
        whileHover={{ scale: 1.05 }}
        whileTap={{ scale: 0.95 }}
      >
        {icon}
      </motion.button>
    </Tooltip>
  );
};

export default CockpitButton;
```

### Phase 4: Styling Implementation

#### Step 4.1: LizaCockpit.css
```css
/* Base Dock Styles */
.liza-cockpit-dock {
  position: fixed;
  bottom: 32px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1000;
  
  display: flex;
  align-items: center;
  gap: 24px;
  padding: 16px 32px;
  
  background: rgba(11, 12, 16, 0.85);
  backdrop-filter: blur(24px) saturate(180%);
  -webkit-backdrop-filter: blur(24px) saturate(180%);
  
  border: 1px solid rgba(0, 240, 255, 0.3);
  border-radius: 28px;
  box-shadow: 
    0 8px 32px rgba(0, 0, 0, 0.4),
    0 0 40px rgba(0, 240, 255, 0.15);
}

/* Sections */
.cockpit-section-left,
.cockpit-section-center,
.cockpit-section-right {
  display: flex;
  align-items: center;
  gap: 12px;
}

/* Button Base */
.cockpit-button {
  background: transparent;
  border: 1px solid rgba(0, 240, 255, 0.3);
  border-radius: 12px;
  padding: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.cockpit-button:hover {
  border-color: rgba(0, 240, 255, 0.8);
  box-shadow: 0 0 20px rgba(0, 240, 255, 0.4);
}

.cockpit-button.active {
  border-color: #BB1111;
  background: rgba(187, 17, 17, 0.1);
}

.cockpit-button-hero {
  padding: 16px;
  border-radius: 50%;
  border-width: 2px;
}

/* Chat Panel (positioned above dock) */
.liza-cockpit-chat-panel {
  position: fixed;
  bottom: 140px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1001;
  
  width: 440px;
  height: 600px;
  
  /* Same glassmorphism as dock */
  background: rgba(11, 12, 16, 0.92);
  backdrop-filter: blur(32px) saturate(180%);
  border: 1px solid rgba(0, 240, 255, 0.4);
  border-radius: 20px;
  box-shadow: 
    0 12px 48px rgba(0, 0, 0, 0.6),
    0 0 60px rgba(0, 240, 255, 0.2);
}
```

### Phase 5: Testing & Validation

#### Step 5.1: Functional Testing
- [ ] Dock renders correctly at bottom center
- [ ] All buttons respond to clicks
- [ ] LIZA chat opens/closes with animation
- [ ] Tour starts correctly
- [ ] Labels toggle works
- [ ] Orbit return works
- [ ] Z-index layering is correct (no canvas blocking)

#### Step 5.2: Visual Testing
- [ ] Glassmorphism effect is visible
- [ ] Icons display with correct colors (Neon Blue/Red)
- [ ] Animations are smooth (60fps)
- [ ] Hover states work on all buttons
- [ ] Active states show correct styling
- [ ] Chat panel centers correctly above dock

#### Step 5.3: Responsive Testing
- [ ] Dock adapts on smaller screens
- [ ] Chat panel fits on mobile (future consideration)

---

## 6. Files Summary

### Files to CREATE (4 new files)
1. `src/components/Cockpit/LizaCockpit.jsx` - Main unified dock component
2. `src/components/Cockpit/CockpitButton.jsx` - Reusable button wrapper
3. `src/components/Cockpit/LizaCockpitChat.jsx` - Chat integration (copy of LizaChat)
4. `src/components/Cockpit/LizaCockpit.css` - Styling for dock and chat

### Files to MODIFY (1 file)
1. `src/components/MapRPG.jsx` - Integration of LizaCockpit component (Lines ~55, ~636)

### Files to REFERENCE (No modifications)
- `src/components/CreativeIcons.jsx` - Icon components
- `src/components/LIZA/LizaChat.jsx` - Original chat (reference for copy)
- `src/components/Cockpit/CockpitConsole.jsx` - Legacy console (reference for props)

---

## 7. Risk Assessment & Mitigation

| Riesgo | Probabilidad | Impacto | Mitigación |
|--------|--------------|---------|------------|
| Z-index conflicts con Canvas 3D | Media | Alto | Usar z-index 1000+ y pointer-events strategy |
| Glassmorphism no funciona en navegadores antiguos | Baja | Bajo | Fallback a background sólido con opacity |
| Animaciones con bajo rendimiento | Baja | Medio | Framer Motion optimizado, usar `will-change: transform` |
| Conflicto con CockpitConsole existente | Baja | Medio | Comentar en lugar de eliminar, permitir rollback fácil |

---

## 8. Future Enhancements

1. **Voice Activation**: Integrar con `useSpeechRecognition` hook
2. **Keyboard Shortcuts**: Añadir atajos (e.g., `Ctrl+K` para LIZA)
3. **Customizable Layout**: Permitir al usuario reorganizar botones
4. **Themes**: Dark/Light mode toggle
5. **Notifications**: Badge counters para mensajes no leídos de LIZA

---

## 9. Dependencies

### Librerías Necesarias (ya instaladas)
- `react` >= 18.0
- `framer-motion` >= 10.0
- `antd` >= 5.0
- `@react-three/fiber` (para contexto Canvas)
- `@react-three/drei` (para Html overlays)

### Assets
- `src/components/CreativeIcons.jsx` (todos los iconos)
- Color variables: `#00F0FF` (Neon Blue), `#BB1111` (Neon Red)

---

## 10. Success Metrics

- ✅ Dock se renderiza sin errores
- ✅ Todas las funciones del CockpitConsole anterior están cubiertas
- ✅ UX es más intuitivo (menos clicks para acceder a LIZA)
- ✅ Rendimiento: < 5% de CPU usage adicional
- ✅ Visual: Cumple con estándares de branding HEKTEK

---

## Appendix A: Color Palette Reference

```javascript
const COLORS = {
  neonBlue: '#00F0FF',
  neonRed: '#BB1111',
  darkBg: 'rgba(11, 12, 16, 0.85)',
  darkBgStrong: 'rgba(11, 12, 16, 0.95)',
  borderBlue: 'rgba(0, 240, 255, 0.3)',
  borderBlueActive: 'rgba(0, 240, 255, 0.8)',
  borderRed: 'rgba(187, 17, 17, 0.5)',
  glowBlue: 'rgba(0, 240, 255, 0.4)',
  glowRed: 'rgba(187, 17, 17, 0.4)',
  textPrimary: '#ffffff',
  textSecondary: 'rgba(255, 255, 255, 0.7)',
};
```

---

**END OF DESIGN DOCUMENT**
