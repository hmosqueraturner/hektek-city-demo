# Análisis: Extensión de Reactividad Visual - HEKTEK City

**Fecha**: 2025-12-08  
**Estado**: Análisis Técnico  
**Protocolo**: [AI_WORKFLOW_PROTOCOL.md](../technical/AI_WORKFLOW_PROTOCOL.md)

---

## 🎯 Visión del Sistema

### Filosofía Central

> **LIZA es la controladora creativa visual**. Los themes preestablecidos son SOLO para testing/debugging.

La intención es que LIZA:
1. **Interprete libremente** cualquier descripción del usuario
2. **Se inspire creativamente** según su entrenamiento
3. **Aplique cambios a TODA la escena** - todos los objetos, materiales, grupos
4. **El entorno sea RECEPTIVO** - que pueda EXPRESARSE visualmente

### Prioridades

| Camino | Propósito | Prioridad |
|--------|-----------|-----------|
| **Gemini API** → LIZA interpreta | Producción | 🔴 **PRINCIPAL** |
| Preprocessor → Keywords | Testing | 🟡 Secundario |

---

## 🔍 Arquitectura Actual

### Archivos Clave

| Archivo | Función | Impacto |
|---------|---------|---------|
| `liza-tools.js` | **Function tool schema** | 🔴 Crítico |
| `liza-theme-generator.js` | Classify materials + PBR | 🔴 Crítico |
| `liza-prompts.js` | System prompt guidelines | 🔴 Crítico |
| `api/liza/chat.js` | Gemini API endpoint | 🟡 Medio |
| `AIThemeContext.jsx` | React context | 🟢 Bajo |

### Function Tool Schema (liza-tools.js:22-69)

```javascript
apply_visual_theme: {
  parameters: {
    styleName: { type: "STRING" },      // ✅
    primaryColor: { type: "STRING" },   // ✅
    accentColor: { type: "STRING" },    // ✅
    emissiveColor: { type: "STRING" },  // ✅
    emissiveIntensity: { type: "NUMBER" },
    metalness: { type: "NUMBER" },
    roughness: { type: "NUMBER" },
    terrainHint: { enum: ["dark", "earthy", "alien", "volcanic", "icy", "desert"] },
    waterHint: { enum: ["clear", "toxic", "murky", "frozen", "glowing"] }
    // ❌ FALTA: vegetationHint, gamepadHint, spacecraftHint
  }
}
```

---

## 📊 Categorías de Materiales

### Actuales (5)

```javascript
classifyMaterial(): TERRAIN | WATER | GLASS | METAL | BUILDING
```

### Propuestas (11)

| Categoría | Pattern | Materiales | Hint Propuesto |
|-----------|---------|------------|----------------|
| TERRAIN | terrain, land, ground | ~2 | terrainHint ✅ |
| WATER | water, aqua, liquid | ~3 | waterHint ✅ |
| **VEGETATION** | grass, tree, hongo | ~10 | **vegetationHint** ❌ |
| GLASS | glass, glaze, window | ~8 | - |
| FLOOR | floor\d* | ~15 | - |
| WALLS | wall, ceiling, roof | ~8 | - |
| METAL | steel, tripo_part | ~35 | - |
| **EMISSIVE** | light, screen, disco | ~12 | - |
| **GAMEPAD** | ctrl_ | ~15 | **gamepadHint** ❌ |
| **SPACECRAFT** | docs_ | ~8 | **spacecraftHint** ❌ |
| BUILDING | default | ~40 | - |

---

## 🔧 Cambios Propuestos

### 1. `liza-theme-generator.js`

**A) Expandir `classifyMaterial()`**:
```javascript
// Añadir:
if (/grass|tree|bark|azalea|hongo|pedal/i.test(name)) return 'VEGETATION';
if (/floor\d*/i.test(name)) return 'FLOOR';
if (/wall|ceiling|roof|doors/i.test(name)) return 'WALLS';
if (/light|screen|emissive|disco|cube_light/i.test(name)) return 'EMISSIVE';
if (/ctrl_/i.test(name)) return 'GAMEPAD';
if (/docs_/i.test(name)) return 'SPACECRAFT';
if (/tripo_part/i.test(name)) return 'METAL';
```

**B) Añadir `adjustColorForVegetation()`**

**C) Extender `getMaterialPropertiesForCategory()`**: 6 nuevos cases

### 2. `liza-tools.js`

**Añadir hints al schema**:
```javascript
vegetationHint: {
  type: "STRING",
  description: "Optional vegetation hint",
  enum: ["bioluminescent", "dead", "toxic", "alien"]
}
```

### 3. `liza-prompts.js`

**Añadir guidelines para nuevas categorías**

---

## ⚡ Performance

| Operación | Actual | Propuesto | Delta |
|-----------|--------|-----------|-------|
| Total theme change | ~15-70ms | ~20-80ms | **Negligible** |

**Transiciones**: INSTANT (sin cambios)

---

## 📝 Próximos Pasos

1. ✅ **Análisis** - Este documento
2. ⏳ **Plan de Implementación** - Crear
3. ⏳ **Aprobación** - Esperar
4. ⏳ **Implementación** - Código

---

**Estado**: Esperando aprobación para Plan de Implementación
