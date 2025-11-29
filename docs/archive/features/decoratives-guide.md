# Guía Completa: Modelos Decorativos en HekTek City

## 📋 Resumen de Cambios

Se ha implementado un sistema completo para cargar modelos decorativos GLB en tu aplicación con las siguientes características:

### ✅ Archivos Creados/Modificados:

1. **EnhancedModels.jsx** - Agregado `EnhancedDecorativeModel`
2. **DecorativeGroup.jsx** - Nuevo componente helper (CREADO)
3. **assetsHelper.js** - Agregadas funciones para decorativos
4. **MapRPG.jsx** - Listo para integrar decorativos

---

## 🎯 ¿Qué hace cada componente?

### 1. EnhancedDecorativeModel
**Propósito:** Carga modelos GLB individuales sin lógica de cámara ni empties.

**Características:**
- ✅ Manejo automático de errores
- ✅ Sistema de fallback (si no encuentra el modelo con theme, carga el original)
- ✅ Tracking de estado de carga
- ✅ Control de sombras (castShadow/receiveShadow)
- ✅ Compatible con sistema de calidad y themes

**Props:**
```javascript
<EnhancedDecorativeModel
  modelName="jellyfishHT"          // (requerido) Nombre del archivo GLB
  position={[x, y, z]}              // Default: [0, 0, 0]
  scale={1}                         // Default: 1 (puede ser número o [x,y,z])
  rotation={[x, y, z]}              // Default: [0, 0, 0] en radianes
  quality="standard"                // 'standard' | 'hd'
  theme={null}                      // null | 'scifi' | 'alien'
  castShadow={true}                 // Default: true
  receiveShadow={true}              // Default: true
  onLoadingStateChange={callback}   // Opcional
/>
```

### 2. ThemedDecorativeModel
**Propósito:** Selecciona automáticamente el modelo correcto según el theme actual.

**Ventaja:** No necesitas escribir condicionales para cambiar modelos según el theme.

```javascript
<ThemedDecorativeModel
  decorativeKey="DECO_A"            // 'DECO_A' | 'DECO_B' | 'DECO_C' | 'DECO_D'
  position={[x, y, z]}
  scale={1}
  rotation={[x, y, z]}
  quality={assetQuality}
  theme={assetTheme}                // Automáticamente usa el modelo correcto
/>
```

**Mapeo automático:**
- `theme = null` → usa `DECO_ORIGINAL.DECO_A` (ej: 'creatureWater')
- `theme = 'scifi'` → usa `DECO_SCIFI.DECO_A` (ej: 'podTransport')
- `theme = 'alien'` → usa `DECO_ALIEN.DECO_A` (ej: 'bioluminCreature')

### 3. DecorativeGroup
**Propósito:** Renderiza múltiples decorativos desde un array de configuración.

```javascript
<DecorativeGroup
  decoratives={[
    {
      key: 'DECO_A',
      position: [5, 0, 10],
      scale: 0.6,
      rotation: [0, Math.PI / 4, 0],
    },
    {
      key: 'DECO_B',
      position: [-5, 0, 15],
      scale: 0.8,
      rotation: [0, 0, 0],
    },
  ]}
  quality={assetQuality}
  theme={assetTheme}
  onLoadingStateChange={callback}
/>
```

### 4. TerrainDecoratives
**Propósito:** Cambia automáticamente los decorativos según el terreno actual.

```javascript
<TerrainDecoratives
  terrainName={currentTerrain}      // 'HTLand' | 'MarsTown' | 'LakeCity'
  quality={assetQuality}
  theme={assetTheme}
  onLoadingStateChange={callback}
/>
```

**Decorativos por terreno (predefinidos):**
- **HTLand:** 3 decorativos variados
- **MarsTown:** 2 decorativos espaciados
- **LakeCity:** 3 decorativos acuáticos

---

## 🚀 Implementación Rápida (3 pasos)

### PASO 1: Actualizar imports en MapRPG.jsx

```javascript
import {
  EnhancedTerrainModel,
  LoadingStateDisplay,
  EnhancedBuildingModel,
  EnhancedDecorativeModel,      // ← AGREGAR
} from "./EnhancedModels";

import { 
  ThemedDecorativeModel, 
  DecorativeGroup, 
  TerrainDecoratives,
  DECORATIVE_PRESETS 
} from "./DecorativeGroup";      // ← AGREGAR COMPLETO
```

### PASO 2: Actualizar loadingStates (opcional)

```javascript
const [loadingStates, setLoadingStates] = useState({
  terrain: null,
  skills: null,
  experience: null,
  vision: null,
  decoratives: {},              // ← AGREGAR
});

// Helper para decorativos
const handleDecorativeLoadingState = (key, state) => {
  setLoadingStates(prev => ({
    ...prev,
    decoratives: {
      ...prev.decoratives,
      [key]: state
    }
  }));
};
```

### PASO 3: Agregar decorativos en el Canvas

**Ubicación:** Después de Vision building, antes de CameraControls

```javascript
{/* ======================================= 
    DECORATIVE MODELS 
======================================= */}

{/* Opción más simple - Decorativos automáticos por terreno */}
<TerrainDecoratives
  terrainName={currentTerrain}
  quality={assetQuality}
  theme={assetTheme}
  onLoadingStateChange={handleDecorativeLoadingState}
/>

{/* O usar presets por área */}
<DecorativeGroup
  decoratives={DECORATIVE_PRESETS.SKILLS_AREA}
  quality={assetQuality}
  theme={assetTheme}
/>
```

---

## 📚 Métodos de Uso (del más simple al más complejo)

### ⭐ NIVEL 1: TerrainDecoratives (RECOMENDADO)
**Más fácil, automático, cero configuración**

```javascript
<TerrainDecoratives
  terrainName={currentTerrain}
  quality={assetQuality}
  theme={assetTheme}
/>
```

✅ Cambia automáticamente con el terreno
✅ Cambia automáticamente con el theme
✅ Posiciones predefinidas optimizadas

---

### ⭐ NIVEL 2: DecorativeGroup con Presets
**Fácil, usa configuraciones predefinidas**

```javascript
<DecorativeGroup
  decoratives={DECORATIVE_PRESETS.SKILLS_AREA}
  quality={assetQuality}
  theme={assetTheme}
/>
```

**Presets disponibles:**
- `DECORATIVE_PRESETS.SKILLS_AREA` (2 decorativos)
- `DECORATIVE_PRESETS.EXPERIENCE_AREA` (2 decorativos)
- `DECORATIVE_PRESETS.VISION_AREA` (2 decorativos)
- `DECORATIVE_PRESETS.TERRAIN_DECORATION` (3 decorativos)
- `DECORATIVE_PRESETS.CIRCULAR_PATTERN` (8 decorativos en círculo)

---

### ⭐ NIVEL 3: DecorativeGroup Custom
**Moderado, defines tus propias posiciones**

```javascript
<DecorativeGroup
  decoratives={[
    {
      key: 'DECO_A',
      position: [5, 0, 10],
      scale: 0.6,
      rotation: [0, Math.PI / 4, 0],
    },
    {
      key: 'DECO_B',
      position: [-5, 0, 15],
      scale: 0.8,
      rotation: [0, 0, 0],
    },
  ]}
  quality={assetQuality}
  theme={assetTheme}
/>
```

---

### ⭐ NIVEL 4: ThemedDecorativeModel
**Control individual, cambio automático de theme**

```javascript
<Suspense fallback={null}>
  <ThemedDecorativeModel
    decorativeKey="DECO_A"
    position={[0, 0, 20]}
    scale={1}
    rotation={[0, 0, 0]}
    quality={assetQuality}
    theme={assetTheme}
  />
</Suspense>
```

---

### ⭐ NIVEL 5: EnhancedDecorativeModel
**Control total, especifica el modelo exacto**

```javascript
<Suspense fallback={null}>
  <EnhancedDecorativeModel
    modelName="jellyfishHT"
    position={[10, 2, 8]}
    scale={0.6}
    rotation={[0, 0, 0]}
    quality={assetQuality}
    theme={assetTheme}
  />
</Suspense>
```

---

## 🎨 Modelos Disponibles

### DECO_ORIGINAL (Theme: null)
```javascript
ASSET_CONFIG.DECO_ORIGINAL.DECO_A  // 'creatureWater'
ASSET_CONFIG.DECO_ORIGINAL.DECO_B  // 'jellyfishHT'
ASSET_CONFIG.DECO_ORIGINAL.DECO_C  // 'coralHT'
ASSET_CONFIG.DECO_ORIGINAL.DECO_D  // 'customMush'
```

### DECO_SCIFI (Theme: 'scifi')
```javascript
ASSET_CONFIG.DECO_SCIFI.DECO_A  // 'podTransport'
ASSET_CONFIG.DECO_SCIFI.DECO_B  // 'robotHT'
ASSET_CONFIG.DECO_SCIFI.DECO_C  // 'scifiMush'
ASSET_CONFIG.DECO_SCIFI.DECO_D  // 'boatHT'
```

### DECO_ALIEN (Theme: 'alien')
```javascript
ASSET_CONFIG.DECO_ALIEN.DECO_A  // 'bioluminCreature'
ASSET_CONFIG.DECO_ALIEN.DECO_C  // 'scifiMush'
ASSET_CONFIG.DECO_ALIEN.DECO_D  // 'jellyfishHT'
```

---

## 📁 Estructura de Carpetas Esperada

```
LOCAL_BASE_URL/models/
├── quality/
│   └── standard/
│       ├── themes/
│       │   ├── scifi/
│       │   │   ├── podTransport.glb
│       │   │   ├── robotHT.glb
│       │   │   ├── scifiMush.glb
│       │   │   └── boatHT.glb
│       │   └── alien/
│       │       ├── bioluminCreature.glb
│       │       ├── scifiMush.glb
│       │       └── jellyfishHT.glb
│       ├── creatureWater.glb      (original)
│       ├── jellyfishHT.glb        (original)
│       ├── coralHT.glb            (original)
│       └── customMush.glb         (original)
```

**Nota:** Los archivos originales (sin theme) van directamente en `quality/standard/`
Los archivos con theme van en `quality/standard/themes/{theme}/`

---

## 🔧 Funciones Helper en AssetManager

```javascript
// Obtener todos los decorativos del theme actual
const decos = AssetManager.getDecorativeModels(assetTheme);
// Retorna: { DECO_A: 'modelName', DECO_B: 'modelName', ... }

// Obtener URL de un decorativo específico
const url = AssetManager.getDecorativeModel('jellyfishHT', {
  quality: 'standard',
  theme: 'scifi'
});
```

---

## 💡 Ejemplos Prácticos

### Ejemplo 1: Decorar área de Skills
```javascript
<DecorativeGroup
  decoratives={[
    {
      key: 'DECO_A',
      position: [-5, 0, 30],
      scale: 0.6,
      rotation: [0, Math.PI / 4, 0],
    },
    {
      key: 'DECO_B',
      position: [-8, 0, 25],
      scale: 0.5,
      rotation: [0, -Math.PI / 3, 0],
    },
  ]}
  quality={assetQuality}
  theme={assetTheme}
/>
```

### Ejemplo 2: Decorativo animado rotando
```javascript
function RotatingDecorative({ position }) {
  const groupRef = useRef();
  
  useFrame((state, delta) => {
    if (groupRef.current) {
      groupRef.current.rotation.y += delta * 0.5;
    }
  });
  
  return (
    <group ref={groupRef}>
      <ThemedDecorativeModel
        decorativeKey="DECO_B"
        position={position}
        scale={0.8}
        quality={assetQuality}
        theme={assetTheme}
      />
    </group>
  );
}

// Uso:
<RotatingDecorative position={[10, 1, 10]} />
```

### Ejemplo 3: Decorativos interactivos
```javascript
const [selectedDeco, setSelectedDeco] = useState(null);

<group
  onClick={(e) => {
    e.stopPropagation();
    setSelectedDeco('deco1');
    console.log('¡Decorativo clickeado!');
  }}
  onPointerOver={(e) => {
    e.stopPropagation();
    document.body.style.cursor = 'pointer';
  }}
  onPointerOut={(e) => {
    e.stopPropagation();
    document.body.style.cursor = 'default';
  }}
>
  <ThemedDecorativeModel
    decorativeKey="DECO_A"
    position={[0, 0, 20]}
    scale={selectedDeco === 'deco1' ? 1.2 : 1}
    quality={assetQuality}
    theme={assetTheme}
  />
</group>
```

### Ejemplo 4: Patrón circular de decorativos
```javascript
const circularDecos = useMemo(() => {
  const result = [];
  const radius = 25;
  const count = 8;
  const keys = ['DECO_A', 'DECO_B', 'DECO_C', 'DECO_D'];
  
  for (let i = 0; i < count; i++) {
    const angle = (i / count) * Math.PI * 2;
    result.push({
      key: keys[i % keys.length],
      position: [
        Math.cos(angle) * radius,
        0,
        Math.sin(angle) * radius
      ],
      scale: 0.5,
      rotation: [0, angle + Math.PI / 2, 0],
    });
  }
  return result;
}, []);

<DecorativeGroup
  decoratives={circularDecos}
  quality={assetQuality}
  theme={assetTheme}
/>
```

---

## 🐛 Troubleshooting

### Problema: Los modelos no se cargan
**Solución:**
1. Verifica que los archivos GLB existen en la ruta correcta
2. Revisa la consola del navegador para ver errores específicos
3. Verifica que `VITE_LOCAL_MODEL` y `VITE_R2_MODELS_PATH` están configurados en `.env`

### Problema: Los decorativos no cambian con el theme
**Solución:**
1. Asegúrate de estar usando `ThemedDecorativeModel` o pasando la prop `theme`
2. Verifica que los archivos GLB existen en la carpeta del theme correspondiente
3. El sistema de fallback cargará la versión original si no encuentra el theme

### Problema: Las sombras no se ven
**Solución:**
1. Asegúrate de tener `shadows` en el Canvas: `<Canvas shadows ...>`
2. Verifica que `castShadow={true}` y `receiveShadow={true}` en los decorativos
3. Revisa que la iluminación tenga `castShadow` habilitado

### Problema: Rendimiento lento con muchos decorativos
**Solución:**
1. Usa `quality="standard"` en lugar de `quality="hd"` para decorativos
2. Considera usar instancing para múltiples copias del mismo modelo
3. Usa `useMemo` para cálculos de posiciones
4. Limita el número de decorativos visibles simultáneamente

---

## ✅ Checklist de Implementación

- [ ] Importar `EnhancedDecorativeModel` en MapRPG.jsx
- [ ] Importar componentes de `DecorativeGroup.jsx`
- [ ] Actualizar estado `loadingStates` con tracking de decorativos
- [ ] Crear función `handleDecorativeLoadingState`
- [ ] Agregar sección de decorativos en el Canvas
- [ ] Probar con `TerrainDecoratives` primero
- [ ] Verificar que los archivos GLB existen en las rutas correctas
- [ ] Probar cambio de theme (original → scifi → alien)
- [ ] Probar cambio de terreno
- [ ] Verificar sombras y iluminación
- [ ] Optimizar rendimiento si es necesario

---

## 📖 Referencia Rápida de Props

### EnhancedDecorativeModel
| Prop | Tipo | Default | Descripción |
|------|------|---------|-------------|
| modelName | string | requerido | Nombre del archivo GLB |
| position | [x,y,z] | [0,0,0] | Posición en el espacio 3D |
| scale | number o [x,y,z] | 1 | Escala del modelo |
| rotation | [x,y,z] | [0,0,0] | Rotación en radianes |
| quality | string | 'standard' | 'standard' o 'hd' |
| theme | string o null | null | 'scifi', 'alien' o null |
| castShadow | boolean | true | Si proyecta sombras |
| receiveShadow | boolean | true | Si recibe sombras |
| onLoadingStateChange | function | undefined | Callback de estado |

### ThemedDecorativeModel
| Prop | Tipo | Default | Descripción |
|------|------|---------|-------------|
| decorativeKey | string | requerido | 'DECO_A', 'DECO_B', 'DECO_C', 'DECO_D' |
| position | [x,y,z] | [0,0,0] | Posición en el espacio 3D |
| scale | number o [x,y,z] | 1 | Escala del modelo |
| rotation | [x,y,z] | [0,0,0] | Rotación en radianes |
| quality | string | 'standard' | 'standard' o 'hd' |
| theme | string o null | null | Auto-selecciona modelo por theme |
| castShadow | boolean | true | Si proyecta sombras |
| receiveShadow | boolean | true | Si recibe sombras |
| onLoadingStateChange | function | undefined | Callback de estado |

---

## 🎯 Recomendaciones Finales

1. **Empieza simple:** Usa `TerrainDecoratives` primero para ver todo funcionando
2. **Itera:** Una vez que funcione, personaliza con `DecorativeGroup` o componentes individuales
3. **Optimiza después:** No te preocupes por el rendimiento hasta que tengas contenido
4. **Usa presets:** Los `DECORATIVE_PRESETS` son un buen punto de partida
5. **Mantén consistencia:** Usa la misma escala relativa para todos los decorativos de un área

---

## 📞 Próximos Pasos Sugeridos

1. Implementar `TerrainDecoratives` en MapRPG.jsx
2. Probar con diferentes terrenos y themes
3. Agregar decorativos personalizados con `DecorativeGroup`
4. Experimentar con animaciones (rotación, bobbing, etc.)
5. Optimizar rendimiento según necesidad
6. Considerar agregar efectos de partículas a decorativos específicos

---

¡Listo para usar! 🚀
