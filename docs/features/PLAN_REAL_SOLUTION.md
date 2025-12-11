# 🎯 Plan Real: Modificar Materiales GLB en Runtime desde R3F

**Fecha:** 2025-11-19
**Problema:** Sistema de variantes actual es demasiado complejo y manual para un sistema dinámico controlado por historia/gameplay.

---

## 🔴 PROBLEMA CON LA SOLUCIÓN ACTUAL:

1. **KHR_materials_variants** es para temas estáticos (Cyberpunk/Mars/Pandora)
2. Requiere pipeline manual de 10+ pasos
3. No permite cambios dinámicos durante gameplay
4. No se puede previsualizar sin exportar todo

**Tu caso de uso:** Historia/gameplay decide cómo se ve un edificio → Necesitas control programático, no temas predefinidos.

---

## ✅ SOLUCIÓN CORRECTA:

### **Opción A: Modificar Materiales Directamente en R3F (RECOMENDADO)**

**Cómo funciona:**
1. Cargas el GLB normal (sin variantes)
2. En runtime, accedes a los materiales del modelo
3. Modificas propiedades (color, metallic, roughness, emission, etc.)
4. Los cambios son instantáneos

**Ventajas:**
- ✅ Sin pipeline manual
- ✅ Control total desde JavaScript
- ✅ Cambios dinámicos durante gameplay
- ✅ Fácil de previsualizar/debuggear
- ✅ Puedes guardar/cargar estados

**Código ejemplo:**

```jsx
import { useGLTF } from '@react-three/drei';
import { useEffect } from 'react';

function DynamicBuilding({ modelPath, theme, storyState }) {
  const { scene } = useGLTF(modelPath);

  useEffect(() => {
    // Recorrer todos los materiales del modelo
    scene.traverse((child) => {
      if (child.isMesh && child.material) {
        const mat = child.material;

        // Aplicar cambios según lógica de juego
        if (theme === 'cyberpunk') {
          mat.color.set('#ff6600');
          mat.metalness = 0.9;
          mat.roughness = 0.1;
          mat.emissive.set('#ff3300');
          mat.emissiveIntensity = 0.5;
        } else if (theme === 'mars') {
          mat.color.set('#8b0000');
          mat.metalness = 0.3;
          mat.roughness = 0.9;
        } else if (storyState.isDestroyed) {
          mat.color.set('#333333');
          mat.roughness = 1.0;
        }
        // ... más lógica según tu historia
      }
    });
  }, [scene, theme, storyState]);

  return <primitive object={scene} />;
}
```

**Archivos necesarios:**
- Solo los GLB normales en `public/assets/models/quality/standard/*.glb`
- Un archivo de configuración JSON (opcional) que mapee estados de historia a propiedades visuales

---

### **Opción B: Sistema de Configuración JSON + R3F**

**Estructura:**

```json
// config/visual-states.json
{
  "buildings": {
    "Experience": {
      "materials": {
        "MAT_Mat_build_base_silver": {
          "default": {
            "color": "#c0c0c0",
            "metalness": 0.8,
            "roughness": 0.5
          },
          "cyberpunk": {
            "color": "#ff6600",
            "metalness": 0.95,
            "roughness": 0.1,
            "emissive": "#ff3300",
            "emissiveIntensity": 0.5
          },
          "mars": {
            "color": "#8b0000",
            "metalness": 0.3,
            "roughness": 0.9
          },
          "destroyed": {
            "color": "#333333",
            "roughness": 1.0,
            "metalness": 0.0
          }
        }
      }
    }
  }
}
```

**Hook de React para aplicar estados:**

```jsx
// hooks/useDynamicMaterials.js
import { useEffect } from 'react';
import visualStates from '../config/visual-states.json';

export function useDynamicMaterials(scene, buildingName, currentState) {
  useEffect(() => {
    if (!scene) return;

    const buildingConfig = visualStates.buildings[buildingName];
    if (!buildingConfig) return;

    scene.traverse((child) => {
      if (child.isMesh && child.material) {
        const materialName = child.material.name;
        const stateConfig = buildingConfig.materials[materialName]?.[currentState];

        if (stateConfig) {
          const mat = child.material;

          if (stateConfig.color) mat.color.set(stateConfig.color);
          if (stateConfig.metalness !== undefined) mat.metalness = stateConfig.metalness;
          if (stateConfig.roughness !== undefined) mat.roughness = stateConfig.roughness;
          if (stateConfig.emissive) {
            mat.emissive.set(stateConfig.emissive);
            mat.emissiveIntensity = stateConfig.emissiveIntensity || 1.0;
          }
        }
      }
    });
  }, [scene, buildingName, currentState]);
}

// Uso:
function Building({ name, storyState }) {
  const { scene } = useGLTF(`/assets/models/quality/standard/${name}.glb`);
  useDynamicMaterials(scene, name, storyState);
  return <primitive object={scene} />;
}
```

---

### **Opción C: Hybrid - Temas Base + Modificaciones Dinámicas**

**Para qué:** Si quieres temas predefinidos (Cyberpunk/Mars/Pandora) PERO también control dinámico.

1. Usa KHR_materials_variants para temas base
2. Luego aplica modificaciones adicionales en runtime según historia

```jsx
function HybridBuilding({ name, baseTheme, dynamicModifiers }) {
  const { scene, materials } = useGLTF(`/assets/models/quality/standard/${name}.glb`);

  // Aplicar tema base (KHR_materials_variants)
  useEffect(() => {
    if (materials) {
      switchVariant(scene, baseTheme);
    }
  }, [scene, materials, baseTheme]);

  // Aplicar modificaciones dinámicas encima
  useEffect(() => {
    scene.traverse((child) => {
      if (child.isMesh && dynamicModifiers[child.material.name]) {
        const mods = dynamicModifiers[child.material.name];
        Object.assign(child.material, mods);
      }
    });
  }, [scene, dynamicModifiers]);

  return <primitive object={scene} />;
}
```

---

## 🛠️ PLAN DE IMPLEMENTACIÓN

### **FASE 1: Simplificar y Validar (1-2 horas)**

1. **Crear un componente de prueba simple:**
   - Carga un GLB (sin variantes)
   - Modifica materiales en runtime
   - Valida que funciona

2. **Crear hook `useDynamicMaterials`:**
   - Acepta configuración JSON
   - Aplica cambios a materiales
   - Maneja transiciones suaves (opcional)

3. **Archivo de configuración mínimo:**
   - Define 2-3 estados visuales para un edificio
   - Valida que se aplican correctamente

**Resultado esperado:** Un edificio que cambia de aspecto al modificar un estado en React.

---

### **FASE 2: Automatización de Pipeline Blender (opcional, solo si necesitas KHR_materials_variants)**

**Script único que hace TODO:**

```bash
# tools/pipeline/full-pipeline.sh
#!/bin/bash

# Paso 1: Generar JSON desde CSV (si cambió)
npm run pipeline:generate-json

# Paso 2: Copiar JSON a addon
cp config/materials_roles.json tools/blender/addons/
cp config/materials_variants.json tools/blender/addons/

# Paso 3: Ejecutar Blender headless para exportar todos los edificios
blender --background your_file.blend --python tools/blender/auto_export_all.py

# Paso 4: Procesar todos los GLB
npm run pipeline:process-all

echo "✅ Pipeline completo terminado"
```

**Blender script headless:**

```python
# tools/blender/auto_export_all.py
import bpy
import sys
import os

# Importar addon functions
sys.path.append(os.path.join(os.path.dirname(__file__), 'addons'))
from material_roles_variants_addon import (
    load_json_configurations,
    build_material_palette,
    generate_variant_materials,
    export_collection_as_glb
)

collections = ['Experience', 'Skills', 'Vision', 'Docs', 'About', 'Projects', 'Blog']

for collection_name in collections:
    print(f"\n{'='*60}")
    print(f"Procesando: {collection_name}")
    print('='*60)

    # Ejecutar pasos del addon
    load_json_configurations()
    build_material_palette()
    generate_variant_materials()
    export_collection_as_glb(collection_name)

print("\n✅ Todos los edificios exportados")
```

**Un solo comando:**
```bash
npm run pipeline:full
```

---

### **FASE 3: Sistema de Versionado Automático**

**package.json:**
```json
{
  "scripts": {
    "addon:version": "node tools/blender/update-addon-version.js",
    "addon:install": "npm run addon:version && python tools/blender/install-addon.py"
  }
}
```

**Auto-incrementar versión:**
```js
// tools/blender/update-addon-version.js
import fs from 'fs';

const addonPath = 'tools/blender/addons/material_roles_variants_addon.py';
let content = fs.readFileSync(addonPath, 'utf-8');

// Incrementar versión automáticamente
const versionMatch = content.match(/"version": \((\d+), (\d+), (\d+)\)/);
if (versionMatch) {
  const [, major, minor, patch] = versionMatch;
  const newPatch = parseInt(patch) + 1;
  content = content.replace(
    /"version": \(\d+, \d+, \d+\)/,
    `"version": (${major}, ${minor}, ${newPatch})`
  );
  fs.writeFileSync(addonPath, content);
  console.log(`✅ Addon version updated to ${major}.${minor}.${newPatch}`);
}
```

---

## 📋 REQUERIMIENTOS MÍNIMOS PARA GLB

Para que funcione la solución R3F directa:

1. **Nombres de materiales consistentes:**
   - Usa el script `FIX_ALL_MATERIALS_NOW.py` UNA VEZ
   - Todos los materiales deben seguir formato `MAT_Mat_nombre`

2. **Exportar desde Blender:**
   - Formato: GLB
   - Include: Selected Objects + Materials
   - NO necesitas MATERIAL_PALETTE
   - NO necesitas variantes
   - Solo asegúrate que los materiales sean Principled BSDF

3. **Ubicación:**
   - `public/assets/models/quality/standard/BuildingName.glb`

**Eso es todo.**

---

## 🎮 FLUJO FINAL RECOMENDADO

### **Para desarrollo/testing:**

1. Exporta GLB desde Blender (paso manual, una vez por edificio)
2. Editas `config/visual-states.json` para definir estados visuales
3. Tu código React lee el JSON y aplica cambios
4. Ves los cambios instantáneamente en el navegador
5. Ajustas el JSON hasta que te guste

### **Para producción:**

Si decides que necesitas KHR_materials_variants:
```bash
npm run pipeline:full
```

Un solo comando que hace todo.

---

## 🚀 SIGUIENTE PASO INMEDIATO

¿Qué prefieres?

**A) Implementar solución R3F pura** (modificación directa de materiales, sin pipeline):
- Creo hook `useDynamicMaterials`
- Creo archivo `config/visual-states.json` con ejemplos
- Actualizo MapVariations para usarlo
- Validamos que funciona

**B) Crear script único para pipeline completo** (si decides usar KHR_materials_variants):
- Creo `npm run pipeline:full` que hace TODO
- Creo script Blender headless para exportar automáticamente
- Sistema de versionado automático
- Validamos end-to-end

**C) Ambas** (híbrido):
- Temas base con KHR_materials_variants
- Control dinámico adicional con R3F

**Dime qué opción prefieres y la implemento AHORA.**

---

**Mi recomendación:** Opción A (R3F puro) porque:
- ✅ Cero pipeline manual
- ✅ Control total programático
- ✅ Perfecto para sistema de historia
- ✅ Fácil de iterar y debuggear
