# Cambios al Sistema de Themes - HekTek City

## Fecha: 2025-10-23
## Resumen de Cambios Implementados

Este documento detalla los cambios realizados para implementar el nuevo sistema de themes con configuraciones fijas para terrenos y HDR.

---

## 1. Archivo: `assetsHelper.js`

### 1.1 Nuevos Themes Agregados

Se actualizó la lista de themes disponibles:

```javascript
THEMES: {
  ORIGINAL: null,
  SCIFI: "scifi",
  CYBERPUNK: "cyberpunk",
  ALIEN: "alien",
  PANDORA: "pandora",
  MARS: "mars",
  DESERT: "desert",
}
```

### 1.2 Nueva Configuración: THEME_CONFIGS

Se agregó un nuevo objeto de configuración que mapea cada theme a su terreno y HDR específico:

```javascript
THEME_CONFIGS: {
  null: { // original theme
    terrain: "HTLand",
    hdr: "HekTek-custom",
  },
  scifi: {
    terrain: "HTLand",
    hdr: "HekTek-skils",
  },
  cyberpunk: {
    terrain: "HTLand",
    hdr: "HekTek-magic-garden",
  },
  alien: {
    terrain: "LakeCity",
    hdr: "HekTek-custom",
  },
  pandora: {
    terrain: "LakeCity",
    hdr: "HekTek-magic-garden",
  },
  mars: {
    terrain: "HTLand",
    hdr: "HekTek-skils",
  },
  desert: {
    terrain: "HTLand",
    hdr: "HekTek-comet",
  },
}
```

### 1.3 Modelos Decorativos por Theme

Se agregaron configuraciones de modelos decorativos para cada nuevo theme:

- **DECO_CYBERPUNK**: podTransport, robotHT, scifiMush, boatHT
- **DECO_PANDORA**: bioluminCreature, coralHT, customMush, jellyfishHT
- **DECO_MARS**: podTransport, robotHT, scifiMush, boatHT
- **DECO_DESERT**: creatureWater, jellyfishHT, coralHT, customMush

### 1.4 Nuevas Funciones del AssetManager

#### `getThemeConfig(theme)`
Nueva función que retorna la configuración de terreno y HDR para un theme específico:

```javascript
getThemeConfig: (theme = null) => {
  return ASSET_CONFIG.THEME_CONFIGS[theme] || ASSET_CONFIG.THEME_CONFIGS[null];
}
```

#### `getDecorativeModels(theme)` - Actualizada
Se actualizó para soportar todos los nuevos themes usando un switch statement.

---

## 2. Archivo: `MapRPG.jsx`

### 2.1 Inicialización Automática

El componente ahora inicializa automáticamente el terreno y HDR según el theme inicial:

```javascript
// Get initial theme configuration
const initialThemeConfig = AssetManager.getThemeConfig(null);
const [hdrEnvironment, setHdrEnvironment] = useState(initialThemeConfig.hdr);
const [currentTerrain, setCurrentTerrain] = useState(initialThemeConfig.terrain);
```

### 2.2 Función `switchTheme()` - Con Validación de Caché

Se actualizó la función `switchTheme` para que automáticamente cambie el terreno y HDR según el theme seleccionado, **CON VALIDACIÓN** para evitar cargas innecesarias:

```javascript
const switchTheme = (theme) => {
  // Get the configuration for this theme
  const themeConfig = AssetManager.getThemeConfig(theme);
  
  // Only update terrain if it's different (validation to avoid unnecessary loading)
  if (themeConfig.terrain !== currentTerrain) {
    setCurrentTerrain(themeConfig.terrain);
    console.log(`Theme ${theme || "original"}: Loading terrain ${themeConfig.terrain}`);
  }
  
  // Only update HDR if it's different (validation to avoid unnecessary loading)
  if (themeConfig.hdr !== hdrEnvironment) {
    setHdrEnvironment(themeConfig.hdr);
    console.log(`Theme ${theme || "original"}: Loading HDR ${themeConfig.hdr}`);
  }
  
  setAssetTheme(theme);
  console.log(`Switched to ${theme || "original"} theme`);
};
```

**IMPORTANTE**: Esta validación asegura que:
- Si dos themes comparten el mismo terreno (ej: original, scifi, cyberpunk, mars, desert → todos usan HTLand), el terreno NO se recarga al cambiar entre ellos
- Si dos themes comparten el mismo HDR, el HDR NO se recarga al cambiar entre ellos
- El sistema de caché de R2 funciona eficientemente porque solo se hacen requests cuando realmente es necesario

### 2.3 Debug Panel - Controles Comentados

Se comentaron los siguientes controles del Debug Panel ya que ahora se manejan automáticamente por el theme:

1. **Quality Controls** - Comentado completamente
2. **HDR Environment Controls** - Comentado completamente  
3. **Terrain Controls** - Comentado completamente

Las funciones `switchQuality`, `switchHdrEnvironment`, y `switchTerrain` se mantienen en el código por si se necesitan en el futuro, pero no están visibles en el UI.

### 2.4 Current Settings Display - Mejorado

Se actualizó la visualización de configuración activa para mostrar de forma jerárquica:

```
Active Theme: scifi
└─ Terrain: HTLand
└─ HDR: HekTek-skils
Quality: standard
Section: none
```

---

## 3. Archivo: `stringValues.js`

### 3.1 Actualización de ASSET_THEMES

Se actualizó la constante `ASSET_THEMES` para incluir todos los nuevos themes:

```javascript
export const ASSET_THEMES = {
  ORIGINAL: null,
  SCIFI: "scifi",
  CYBERPUNK: "cyberpunk",
  ALIEN: "alien",
  PANDORA: "pandora",
  MARS: "mars",
  DESERT: "desert",
};
```

---

## 4. Configuración Final de Themes

### Mapeo Completo Theme → Terrain + HDR:

| Theme      | Terrain  | HDR                     |
|------------|----------|-------------------------|
| original   | HTLand   | HekTek-custom          |
| scifi      | HTLand   | HekTek-skils           |
| cyberpunk  | HTLand   | HekTek-magic-garden    |
| alien      | LakeCity | HekTek-custom          |
| pandora    | LakeCity | HekTek-magic-garden    |
| mars       | HTLand   | HekTek-skils           |
| desert     | HTLand   | HekTek-comet           |

---

## 5. Optimizaciones de Caché

### 5.1 Validación Antes de Cargar Recursos

El sistema ahora valida ANTES de cargar recursos:

```javascript
// Solo carga si es diferente
if (themeConfig.terrain !== currentTerrain) {
  setCurrentTerrain(themeConfig.terrain);
}

if (themeConfig.hdr !== hdrEnvironment) {
  setHdrEnvironment(themeConfig.hdr);
}
```

### 5.2 Beneficios de la Validación

1. **Reduce Requests Innecesarios**: Si cambias de "original" → "scifi" → "cyberpunk", el terreno HTLand solo se carga UNA vez
2. **Aprovecha el Caché de R2**: Los recursos ya cargados permanecen en caché del navegador
3. **Mejora la Performance**: No hay recargas innecesarias de modelos GLB y texturas HDR pesadas
4. **Reduce Costos**: Menos requests al CDN de R2

### 5.3 Escenarios de Optimización

**Escenario 1**: Cambio de themes con mismo terreno
```
original (HTLand) → scifi (HTLand)
✅ Terreno: NO recarga (ya está cargado)
✅ HDR: Cambia (HekTek-custom → HekTek-skils)
✅ Edificios: Cargan versión scifi
```

**Escenario 2**: Cambio de themes con diferente terreno
```
scifi (HTLand) → alien (LakeCity)
✅ Terreno: Carga LakeCity (diferente)
✅ HDR: NO recarga (ambos usan HekTek-custom)
✅ Edificios: Cargan versión alien
```

**Escenario 3**: Cambio de themes con mismo HDR y terreno
```
scifi (HTLand, HekTek-skils) → mars (HTLand, HekTek-skils)
✅ Terreno: NO recarga (mismo)
✅ HDR: NO recarga (mismo)
✅ Edificios: Cargan versión mars
```

---

## 6. Impacto en Sistema de Decorativos

Los cambios realizados son **COMPATIBLES** con el sistema de decorativos existente:

### 6.1 Decorativos por Theme

Cada theme tiene su propio set de modelos decorativos definidos en `assetsHelper.js`:

- **original**: creatureWater, jellyfishHT, coralHT, customMush
- **scifi**: podTransport, robotHT, scifiMush, boatHT
- **cyberpunk**: podTransport, robotHT, scifiMush, boatHT
- **alien**: bioluminCreature, coralHT, customMush, jellyfishHT
- **pandora**: bioluminCreature, coralHT, customMush, jellyfishHT
- **mars**: podTransport, robotHT, scifiMush, boatHT
- **desert**: creatureWater, jellyfishHT, coralHT, customMush

### 6.2 Uso en DecorativeGroup

El componente `DecorativeGroup` debe usar:

```javascript
import { AssetManager } from '../utils/assetsHelper';

// Obtener modelos decorativos según el theme actual
const decorativeModels = AssetManager.getDecorativeModels(assetTheme);

// Luego usar: decorativeModels.DECO_A, decorativeModels.DECO_B, etc.
```

---

## 7. Testing Recomendado

### 7.1 Tests Funcionales

1. ✅ Verificar que al iniciar la app, cargue con theme "original" (HTLand + HekTek-custom)
2. ✅ Cambiar entre themes que comparten terreno (original → scifi → cyberpunk → mars → desert)
   - El terreno NO debe recargar
3. ✅ Cambiar entre themes que comparten HDR (original → alien, scifi → mars)
   - El HDR NO debe recargar
4. ✅ Cambiar entre themes con diferentes recursos (scifi → alien)
   - Ambos recursos deben cambiar
5. ✅ Verificar que los edificios cambien correctamente con cada theme
6. ✅ Verificar que los modelos decorativos se asignen correctamente

### 7.2 Tests de Console Logs

Revisa la consola del navegador. Deberías ver logs como:
```
Theme scifi: Loading HDR HekTek-skils
Switched to scifi theme
(NO debería aparecer log de terrain si ya estaba HTLand)
```

### 7.3 Tests de Network

Abre DevTools → Network → Filter by "glb" o "exr":
- Verifica que los recursos NO se recarguen si ya están en caché
- Verifica los headers `cache-control` de R2

---

## 8. Notas Importantes para Producción

### 8.1 Código en Producción

- ❌ NO eliminar las funciones `switchQuality`, `switchHdrEnvironment`, `switchTerrain`
- ✅ Mantenerlas comentadas en el UI pero funcionales en el código
- ✅ Los controles comentados pueden ser descomentados fácilmente si se necesitan en el futuro

### 8.2 Estructura de Carpetas en R2

Asegúrate de que la estructura en R2 sea:

```
models/
├── quality/
│   └── standard/
│       ├── HTLand.glb
│       ├── LakeCity.glb
│       ├── themes/
│       │   ├── scifi/
│       │   │   ├── Experience.glb
│       │   │   ├── Skills.glb
│       │   │   ├── Vision.glb
│       │   │   └── [decorativos scifi]
│       │   ├── cyberpunk/
│       │   │   └── [edificios y decorativos]
│       │   ├── alien/
│       │   ├── pandora/
│       │   ├── mars/
│       │   └── desert/

hdr/
├── quality/
│   └── standard/
│       ├── HekTek-custom.exr
│       ├── HekTek-skils.exr
│       ├── HekTek-magic-garden.exr
│       └── HekTek-comet.exr
```

### 8.3 Configuración de Caché en R2

Verifica que la variable de entorno esté configurada:
```
VITE_CACHE_REVALIDATE=max-age=31536000
```

Esto asegura que los assets se cacheen por 1 año (31536000 segundos).

---

## 9. Ventajas del Nuevo Sistema

### 9.1 Para el Usuario
- ✅ Cambios de theme más rápidos (menos carga)
- ✅ Menor consumo de datos
- ✅ Experiencia más fluida

### 9.2 Para el Desarrollador
- ✅ Un solo control: Theme (en lugar de 3: Quality, Terrain, HDR)
- ✅ Configuración centralizada y fácil de mantener
- ✅ Lógica de validación para optimizar recursos
- ✅ Código más limpio y organizado

### 9.3 Para el Proyecto
- ✅ Reducción de costos de CDN (menos requests)
- ✅ Mejor uso del caché
- ✅ Escalable para futuros themes

---

## 10. Próximos Pasos

### 10.1 Inmediatos
1. Subir los modelos de edificios para cada theme a R2
2. Subir los modelos decorativos para cada theme
3. Probar todos los themes en desarrollo
4. Verificar que el caché funcione correctamente

### 10.2 Futuros (Opcional)
1. Agregar animaciones de transición entre themes
2. Agregar un loader específico cuando cambia el theme
3. Preload de assets del próximo theme probable
4. Analytics de uso de themes

---

## 11. Contacto y Soporte

Si tienes preguntas sobre esta implementación, revisa:
1. Este documento (THEME_SYSTEM_CHANGES.md)
2. Comentarios en el código de `assetsHelper.js`
3. Comentarios en el código de `MapRPG.jsx`

---

**Última actualización**: 2025-10-23  
**Versión**: 1.0  
**Estado**: ✅ Implementado y listo para testing
