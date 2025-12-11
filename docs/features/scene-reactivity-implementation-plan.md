# Plan de Implementación: Extensión de Reactividad Visual

**Fecha**: 2025-12-08  
**Estado**: Pendiente Aprobación  
**Análisis**: [scene-reactivity-enhancement.md](../technical/scene-reactivity-enhancement.md)

---

## Objetivo

Extender el sistema de clasificación de materiales de 5 → 11 categorías para que LIZA pueda aplicar cambios visuales más precisos y expresivos a toda la escena 3D.

---

## Archivos a Modificar

### 1. `src/utils/liza/liza-theme-generator.js`

#### A) Expandir `classifyMaterial()` (línea ~15-24)

**Antes**:
```javascript
function classifyMaterial(materialName) {
  const name = materialName.toLowerCase();
  
  if (/terrain|land|ground/i.test(name)) return 'TERRAIN';
  if (/water|aqua|liquid/i.test(name)) return 'WATER';
  if (/glass|glaze|window/i.test(name)) return 'GLASS';
  if (/steel|metal|silver|chrome/i.test(name)) return 'METAL';
  
  return 'BUILDING';
}
```

**Después**:
```javascript
function classifyMaterial(materialName) {
  const name = materialName.toLowerCase();
  
  // Environment
  if (/terrain|land|ground/i.test(name)) return 'TERRAIN';
  if (/water|aqua|liquid/i.test(name)) return 'WATER';
  
  // Vegetation & Organic
  if (/grass|tree|bark|azalea|cypress|alien_tree|hongo|pedal/i.test(name)) return 'VEGETATION';
  
  // Architecture
  if (/glass|glaze|window|facade_glass|scifiglass/i.test(name)) return 'GLASS';
  if (/floor\d*/i.test(name)) return 'FLOOR';
  if (/wall|ceiling|roof|doors/i.test(name)) return 'WALLS';
  
  // Technical
  if (/steel|metal|silver|chrome|tripo_part/i.test(name)) return 'METAL';
  if (/light|screen|emissive|disco|cube_light/i.test(name)) return 'EMISSIVE';
  
  // Special Objects (Tripo AI models)
  if (/ctrl_/i.test(name)) return 'GAMEPAD';
  if (/docs_/i.test(name)) return 'SPACECRAFT';
  
  return 'BUILDING';
}
```

---

#### B) Añadir `adjustColorForVegetation()` (después de línea ~178)

```javascript
/**
 * Adjust color for vegetation based on optional hint
 * @param {string} hexColor - Base color
 * @param {string|null} hint - bioluminescent, dead, toxic, alien
 */
function adjustColorForVegetation(hexColor, hint = null) {
  const r = parseInt(hexColor.slice(1, 3), 16);
  const g = parseInt(hexColor.slice(3, 5), 16);
  const b = parseInt(hexColor.slice(5, 7), 16);
  
  let newR, newG, newB;
  
  switch(hint) {
    case 'bioluminescent':
      // Saturated glowing - shift toward accent
      const avgBio = (r + g + b) / 3;
      const glowFactor = 1.8;
      newR = Math.floor(avgBio + (r - avgBio) * glowFactor);
      newG = Math.floor(avgBio + (g - avgBio) * glowFactor);
      newB = Math.floor(avgBio + (b - avgBio) * glowFactor);
      break;
      
    case 'dead':
      // Desaturated browns
      const avgDead = (r + g + b) / 3;
      newR = Math.floor(avgDead * 0.6 + 50);
      newG = Math.floor(avgDead * 0.5 + 30);
      newB = Math.floor(avgDead * 0.4);
      break;
      
    case 'toxic':
      // Neon greens/yellows
      newR = Math.floor(r * 0.3);
      newG = Math.min(Math.floor(g * 1.5 + 80), 255);
      newB = Math.floor(b * 0.4);
      break;
    
    case 'alien':
      // Unusual colors (purples, cyans)
      newR = Math.floor(r * 1.2);
      newG = Math.floor(g * 0.7);
      newB = Math.min(Math.floor(b * 1.6), 255);
      break;
      
    default:
      // Natural green-ish tint
      newR = Math.floor(r * 0.5);
      newG = Math.floor(Math.min(g * 1.2, 255));
      newB = Math.floor(b * 0.6);
  }
  
  newR = Math.max(0, Math.min(255, newR));
  newG = Math.max(0, Math.min(255, newG));
  newB = Math.max(0, Math.min(255, newB));
  
  return `#${newR.toString(16).padStart(2, '0')}${newG.toString(16).padStart(2, '0')}${newB.toString(16).padStart(2, '0')}`;
}
```

---

#### C) Extender `getMaterialPropertiesForCategory()` (línea ~184-240)

Añadir después del case 'BUILDING':

```javascript
    case 'VEGETATION':
      return {
        color: adjustColorForVegetation(aiTheme.accentColor, aiTheme.vegetationHint),
        roughness: 0.85,
        metalness: 0.0,
        emissive: adjustColorForVegetation(aiTheme.accentColor, aiTheme.vegetationHint),
        emissiveIntensity: aiTheme.vegetationHint === 'bioluminescent' 
          ? baseEmissive * 2.0 
          : baseEmissive * 0.2
      };
    
    case 'FLOOR':
      return {
        color: adjustColorForTerrain(aiTheme.primaryColor, terrainHint),
        roughness: 0.85,
        metalness: 0.1,
        emissive: aiTheme.emissiveColor || '#000000',
        emissiveIntensity: baseEmissive * 0.1
      };
    
    case 'WALLS':
      return {
        color: aiTheme.primaryColor,
        roughness: 0.7,
        metalness: 0.2,
        emissive: aiTheme.emissiveColor || aiTheme.accentColor,
        emissiveIntensity: baseEmissive * 0.15
      };
    
    case 'EMISSIVE':
      return {
        color: aiTheme.accentColor,
        roughness: 0.1,
        metalness: 0.1,
        emissive: aiTheme.accentColor,
        emissiveIntensity: baseEmissive * 2.5
      };
    
    case 'GAMEPAD':
      return {
        color: aiTheme.primaryColor,
        roughness: 0.4,
        metalness: 0.6,
        emissive: aiTheme.accentColor,
        emissiveIntensity: baseEmissive * 0.6
      };
    
    case 'SPACECRAFT':
      return {
        color: aiTheme.primaryColor,
        roughness: 0.3,
        metalness: 0.7,
        emissive: aiTheme.accentColor,
        emissiveIntensity: baseEmissive * 0.5
      };
```

---

### 2. `src/utils/liza/liza-tools.js`

#### Añadir `vegetationHint` al schema (línea ~65, después de waterHint)

```javascript
        vegetationHint: {
          type: "STRING",
          description: "OPTIONAL: Vegetation aesthetic hint. Options: 'bioluminescent' (glowing plants), 'dead' (dried/lifeless), 'toxic' (neon polluted), 'alien' (otherworldly colors). Default: auto-derived green tint",
          enum: ["bioluminescent", "dead", "toxic", "alien"]
        }
```

---

### 3. `src/utils/liza/liza-prompts.js`

#### Añadir guidelines para vegetationHint (línea ~68, después de WATER HINTS)

```javascript
VEGETATION HINTS:
- "bioluminescent": Glowing organic life, high emissive
- "dead": Dried, lifeless, brown tones
- "toxic": Neon polluted plants, green-yellow glow
- "alien": Otherworldly colors (purples, cyans)

WHEN TO USE VEGETATION HINTS:
✅ USE for forest/jungle themes
✅ USE when user mentions "glowing plants" or "bioluminescent"
✅ USE for post-apocalyptic (dead) or radioactive (toxic) scenes
❌ SKIP for urban themes with minimal vegetation
```

---

## Orden de Implementación

1. **`liza-theme-generator.js`** - Core changes
   - `classifyMaterial()` expansion
   - `adjustColorForVegetation()` new function
   - `getMaterialPropertiesForCategory()` new cases

2. **`liza-tools.js`** - Schema update
   - Add `vegetationHint` parameter

3. **`liza-prompts.js`** - Guidelines
   - Add VEGETATION HINTS section

---

## Testing

### Fase 1: MapVariations (sin API)
1. Seleccionar building con vegetación
2. Cambiar theme manualmente
3. Verificar clasificación correcta en console logs

### Fase 2: LIZA Prompts (con API)
```
Test prompts:
- "Hazlo ver como una jungla bioluminiscente"
- "Transforma en un desierto post-apocalíptico"
- "Estilo Pandora de Avatar"
- "Ambiente de videojuego retro"
```

---

## Rollback

Si algo falla, los cambios son 100% aditivos:
- Nuevas categorías solo añaden precisión
- Default 'BUILDING' sigue funcionando
- Sin cambios breaking en esquema

---

## Git Deliverables (Post-Implementación)

**Commit message**:
```
feat(liza): extend material classification to 11 categories

- Add VEGETATION, FLOOR, WALLS, EMISSIVE, GAMEPAD, SPACECRAFT categories
- Add adjustColorForVegetation() helper with hints support
- Add vegetationHint to function tool schema
- Improve visual expressiveness for AI-driven themes
```

**PR Description**:
```
## Summary
Extends LIZA's visual control by classifying materials into 11 semantic 
categories (up from 5), enabling more precise and expressive theme application.

## Changes
- `liza-theme-generator.js`: New categories and color adjustment
- `liza-tools.js`: vegetationHint parameter
- `liza-prompts.js`: Updated guidelines

## Testing
- Manual testing via MapVariations
- LIZA prompt testing with various descriptions
```

---

**Estado**: Esperando aprobación → "Proceder con la implementación"
