# Guía de Migración: Sistema de Decorativos con Themes

## 📋 Checklist de Migración

### ✅ Completado (por estos cambios)

- [x] `assetsHelper.js` actualizado con nuevos themes
- [x] `assetsHelper.js` con configuración THEME_CONFIGS
- [x] `assetsHelper.js` con modelos decorativos por theme
- [x] `MapRPG.jsx` con validación de caché
- [x] `MapRPG.jsx` con controles comentados (Quality, HDR, Terrain)
- [x] `MapRPG.jsx` con función switchTheme optimizada
- [x] `stringValues.js` actualizado con nuevos themes

### 🔲 Pendiente (para implementar cuando uses decorativos)

- [ ] Modificar `DecorativeGroup.jsx` para usar AssetManager.getDecorativeModels()
- [ ] Actualizar `DecorativePositions.js` con posiciones para cada terrain
- [ ] Probar carga de decorativos con cada theme
- [ ] Verificar que decorativos cambien correctamente entre themes

---

## 🚀 Cómo Integrar el Sistema de Decorativos

### Paso 1: Modificar DecorativeGroup.jsx

**ANTES:**

```javascript
// Código antiguo (ejemplo)
function DecorativeGroup({ theme }) {
  const models = {
    scifi: 'podTransport',
    alien: 'jellyfishHT',
  };
  
  return <Model name={models[theme]} />;
}
```

**DESPUÉS:**

```javascript
import { AssetManager } from '../utils/assetsHelper';

function DecorativeGroup({ assetTheme, assetQuality }) {
  // Obtener modelos decorativos del theme actual
  const decorativeModels = useMemo(() => {
    return AssetManager.getDecorativeModels(assetTheme);
  }, [assetTheme]);
  
  return (
    <group>
      <EnhancedDecorativeModel
        modelName={decorativeModels.DECO_A}
        quality={assetQuality}
        theme={assetTheme}
        position={[0, 0, 0]}
      />
      <EnhancedDecorativeModel
        modelName={decorativeModels.DECO_B}
        quality={assetQuality}
        theme={assetTheme}
        position={[10, 0, 0]}
      />
    </group>
  );
}
```

### Paso 2: Actualizar DecorativePositions.js

Necesitas definir posiciones específicas para cada terrain:

```javascript
export const DECORATIVE_POSITIONS = {
  HTLand: {
    // Posiciones para HTLand (usado por: original, scifi, cyberpunk, mars, desert)
    zone1: [
      { position: [10, 0, 10], type: 'A', scale: 1.0 },
      { position: [15, 0, 12], type: 'B', scale: 1.2 },
      { position: [20, 0, 15], type: 'C', scale: 0.8 },
    ],
    zone2: [
      { position: [-10, 0, -10], type: 'A', scale: 1.1 },
      { position: [-15, 0, -12], type: 'D', scale: 1.3 },
    ],
  },
  
  LakeCity: {
    // Posiciones para LakeCity (usado por: alien, pandora)
    zone1: [
      { position: [5, 0.2, 5], type: 'A', scale: 0.9 },
      { position: [8, 0.2, 8], type: 'B', scale: 1.0 },
      { position: [12, 0.2, 10], type: 'C', scale: 0.7 },
    ],
    zone2: [
      { position: [-5, 0.2, -5], type: 'D', scale: 1.2 },
      { position: [-8, 0.2, -8], type: 'A', scale: 0.8 },
    ],
  },
};

// Función helper para obtener posiciones según el terrain actual
export function getDecorativePositionsForTerrain(terrainName) {
  return DECORATIVE_POSITIONS[terrainName] || DECORATIVE_POSITIONS.HTLand;
}
```

### Paso 3: Integrar en MapRPG.jsx

Agrega el componente de decorativos en tu Canvas:

```javascript
import { TerrainDecoratives } from './DecorativeGroup';
import { getDecorativePositionsForTerrain } from './DecorativePositions';

// Dentro del componente MapRPG, en el Canvas:
<Canvas>
  {/* ... Terrain, Buildings, etc ... */}
  
  {/* Decorativos según el terrain actual */}
  <Suspense fallback={null}>
    <TerrainDecoratives
      assetTheme={assetTheme}
      assetQuality={assetQuality}
      terrainName={currentTerrain}
      positions={getDecorativePositionsForTerrain(currentTerrain)}
    />
  </Suspense>
</Canvas>
```

---

## 🎯 Ejemplo Completo: TerrainDecoratives Component

Crea o modifica el componente que maneja todos los decorativos:

```javascript
import React, { useMemo } from 'react';
import { AssetManager } from '../utils/assetsHelper';
import { EnhancedDecorativeModel } from './EnhancedModels';

export function TerrainDecoratives({ 
  assetTheme, 
  assetQuality, 
  terrainName,
  positions 
}) {
  // Obtener modelos decorativos del theme actual
  const decorativeModels = useMemo(() => {
    return AssetManager.getDecorativeModels(assetTheme);
  }, [assetTheme]);
  
  // Aplanar todas las zonas en un solo array
  const allPositions = useMemo(() => {
    if (!positions) return [];
    
    return Object.values(positions).flat();
  }, [positions]);
  
  // Log para debugging
  console.log(`Rendering decoratives for terrain: ${terrainName}, theme: ${assetTheme || 'original'}`);
  console.log('Decorative models:', decorativeModels);
  console.log('Total decoratives:', allPositions.length);
  
  return (
    <group name={`decoratives-${terrainName}-${assetTheme || 'original'}`}>
      {allPositions.map((pos, index) => {
        // Obtener el nombre del modelo según el tipo
        let modelName;
        switch (pos.type) {
          case 'A':
            modelName = decorativeModels.DECO_A;
            break;
          case 'B':
            modelName = decorativeModels.DECO_B;
            break;
          case 'C':
            modelName = decorativeModels.DECO_C;
            break;
          case 'D':
            modelName = decorativeModels.DECO_D;
            break;
          default:
            modelName = decorativeModels.DECO_A;
        }
        
        return (
          <EnhancedDecorativeModel
            key={`deco-${terrainName}-${index}`}
            modelName={modelName}
            quality={assetQuality}
            theme={assetTheme}
            position={pos.position}
            scale={pos.scale || 1}
            rotation={pos.rotation || [0, 0, 0]}
          />
        );
      })}
    </group>
  );
}
```

---

## 🔄 Flujo de Datos: Cambio de Theme con Decorativos

```
Usuario hace click en "SciFi Theme"
         ↓
switchTheme('scifi') en MapRPG
         ↓
AssetManager.getThemeConfig('scifi')
    → terrain: HTLand
    → hdr: HekTek-skils
         ↓
Validación de Recursos:
    ✅ Terrain HTLand ya cargado → NO recarga
    ✅ HDR HekTek-skils → Carga nuevo HDR
         ↓
AssetManager.getDecorativeModels('scifi')
    → DECO_A: podTransport
    → DECO_B: robotHT
    → DECO_C: scifiMush
    → DECO_D: boatHT
         ↓
TerrainDecoratives re-render
    → Carga modelos SciFi en posiciones de HTLand
         ↓
Buildings re-render
    → Experience.glb (scifi version)
    → Skills.glb (scifi version)
    → Vision.glb (scifi version)
```

---

## 📁 Estructura de Archivos en R2

Para que todo funcione, asegúrate de tener esta estructura:

```bash
cloudflare-r2-bucket/
└── models/
    └── quality/
        └── standard/
            ├── HTLand.glb              # Terrain para themes: original, scifi, cyberpunk, mars, desert
            ├── LakeCity.glb            # Terrain para themes: alien, pandora
            │
            ├── themes/
            │   ├── scifi/
            │   │   ├── Experience.glb
            │   │   ├── Skills.glb
            │   │   ├── Vision.glb
            │   │   ├── podTransport.glb    # DECO_A
            │   │   ├── robotHT.glb         # DECO_B
            │   │   ├── scifiMush.glb       # DECO_C
            │   │   └── boatHT.glb          # DECO_D
            │   │
            │   ├── cyberpunk/
            │   │   ├── Experience.glb
            │   │   ├── Skills.glb
            │   │   ├── Vision.glb
            │   │   ├── podTransport.glb    # DECO_A (mismo que scifi)
            │   │   ├── robotHT.glb         # DECO_B
            │   │   ├── scifiMush.glb       # DECO_C
            │   │   └── boatHT.glb          # DECO_D
            │   │
            │   ├── alien/
            │   │   ├── Experience.glb
            │   │   ├── Skills.glb
            │   │   ├── Vision.glb
            │   │   ├── bioluminCreature.glb # DECO_A
            │   │   ├── coralHT.glb          # DECO_B
            │   │   ├── customMush.glb       # DECO_C
            │   │   └── jellyfishHT.glb      # DECO_D
            │   │
            │   ├── pandora/
            │   │   ├── Experience.glb
            │   │   ├── Skills.glb
            │   │   ├── Vision.glb
            │   │   ├── bioluminCreature.glb # DECO_A (mismo que alien)
            │   │   ├── coralHT.glb          # DECO_B
            │   │   ├── customMush.glb       # DECO_C
            │   │   └── jellyfishHT.glb      # DECO_D
            │   │
            │   ├── mars/
            │   │   ├── Experience.glb
            │   │   ├── Skills.glb
            │   │   ├── Vision.glb
            │   │   ├── podTransport.glb     # DECO_A
            │   │   ├── robotHT.glb          # DECO_B
            │   │   ├── scifiMush.glb        # DECO_C
            │   │   └── boatHT.glb           # DECO_D
            │   │
            │   └── desert/
            │       ├── Experience.glb
            │       ├── Skills.glb
            │       ├── Vision.glb
            │       ├── creatureWater.glb    # DECO_A
            │       ├── jellyfishHT.glb      # DECO_B
            │       ├── coralHT.glb          # DECO_C
            │       └── customMush.glb       # DECO_D
            │
            └── (sin theme = original)
                ├── Experience.glb
                ├── Skills.glb
                ├── Vision.glb
                ├── creatureWater.glb        # DECO_A
                ├── jellyfishHT.glb          # DECO_B
                ├── coralHT.glb              # DECO_C
                └── customMush.glb           # DECO_D

└── hdr/
    └── quality/
        └── standard/
            ├── HekTek-custom.exr        # Para: original, alien
            ├── HekTek-skils.exr         # Para: scifi, mars
            ├── HekTek-magic-garden.exr  # Para: cyberpunk, pandora
            └── HekTek-comet.exr         # Para: desert
```

---

## 🧪 Testing: Verificar que Todo Funciona

### Test 1: Cambio entre themes con mismo terrain

```javascript
// En la consola del navegador:
// 1. Inicia en original (HTLand, HekTek-custom)
// 2. Cambia a scifi
// Resultado esperado:
// - Terrain HTLand NO recarga ✅
// - HDR cambia a HekTek-skils ✅
// - Edificios cargan versión scifi ✅
// - Decorativos cambian de creatureWater/jellyfishHT a podTransport/robotHT ✅
```

### Test 2: Cambio entre themes con diferente terrain

```javascript
// 1. Estás en scifi (HTLand, HekTek-skils)
// 2. Cambia a alien
// Resultado esperado:
// - Terrain cambia a LakeCity ✅
// - HDR cambia a HekTek-custom ✅
// - Edificios cargan versión alien ✅
// - Decorativos cargan en posiciones de LakeCity ✅
// - Decorativos usan modelos alien (bioluminCreature, etc) ✅
```

### Test 3: Verificar caché

```javascript
// 1. Cambia: original → scifi → cyberpunk → mars → desert
// Resultado esperado:
// - Terrain HTLand carga UNA sola vez ✅
// - HDR se recarga según cada theme ✅
// - Edificios y decorativos se recargan cada vez ✅

// 2. Cambia: alien → pandora
// Resultado esperado:
// - Terrain LakeCity carga UNA sola vez ✅
// - HDR cambia de HekTek-custom a HekTek-magic-garden ✅
```

---

## ⚠️ Problemas Comunes y Soluciones

### Problema 1: Decorativos no aparecen

**Causa**: Modelos no están subidos a R2 o path incorrecto

**Solución**:

1. Verifica que los archivos GLB existen en R2
2. Verifica la estructura de carpetas
3. Chequea la consola para errores 404
4. Usa DevTools → Network para ver qué URLs se están intentando cargar

### Problema 2: Decorativos no cambian con el theme

**Causa**: Component no está recibiendo assetTheme actualizado

**Solución**:

```javascript
// Asegúrate de pasar assetTheme como prop
<TerrainDecoratives
  assetTheme={assetTheme}  // ← Importante
  assetQuality={assetQuality}
  terrainName={currentTerrain}
/>

// Y usar useMemo para recalcular cuando cambie
const decorativeModels = useMemo(() => {
  return AssetManager.getDecorativeModels(assetTheme);
}, [assetTheme]); // ← Dependencia importante
```

### Problema 3: Decorativos en posiciones incorrectas

**Causa**: Usando posiciones de un terrain en otro terrain

**Solución**:

```javascript
// Obtener posiciones según el terrain actual, NO el theme
const positions = getDecorativePositionsForTerrain(currentTerrain);

// HTLand: Y = 0 (terreno plano)
// LakeCity: Y = 0.2 (sobre el agua)
```

### Problema 4: Performance issues al cambiar themes

**Causa**: Re-render innecesarios o falta de optimización

**Solución**:

```javascript
// 1. Usa React.memo para componentes decorativos
const DecorativeModel = React.memo(EnhancedDecorativeModel);

// 2. Usa useMemo para cálculos pesados
const decorativeModels = useMemo(() => 
  AssetManager.getDecorativeModels(assetTheme),
  [assetTheme]
);

// 3. Usa key estables
key={`deco-${terrainName}-${index}`}  // ✅ Bueno
key={Math.random()}                    // ❌ Malo
```

---

## 📊 Métricas de Éxito

Después de implementar, verifica:

- [ ] Todos los 7 themes cargan correctamente
- [ ] Los decorativos cambian según el theme
- [ ] Las posiciones son correctas para cada terrain
- [ ] El caché funciona (recursos no se recargan innecesariamente)
- [ ] No hay errores 404 en la consola
- [ ] El cambio de theme es fluido (<2 segundos)
- [ ] La memoria no crece indefinidamente al cambiar themes

---

## 🎓 Recursos Adicionales

1. **THEME_SYSTEM_CHANGES.md** - Documentación completa de cambios
2. **THEME_SYSTEM_EXAMPLES.jsx** - Ejemplos de código
3. **assetsHelper.js** - Funciones de AssetManager
4. **MapRPG.jsx** - Implementación de referencia

---

## 📞 Soporte

Si encuentras problemas:

1. Revisa los logs en consola
2. Verifica la estructura de archivos en R2
3. Usa ThemeTestHelpers para debugging
4. Chequea que todas las dependencias estén pasadas correctamente

---

**Última actualización**: 2025-10-23  
**Versión**: 1.0  
**Estado**: ✅ Listo para implementar decorativos
