# ⚡ Quick Start - Pipeline Automatizado

**Guía ultra-rápida de 5 minutos para procesar todos tus edificios**

---

## 🎯 Objetivo

Exportar y procesar los 7 edificios de HekTek City con material variants en **menos de 5 minutos**.

---

## 📋 Checklist Rápido

### ✅ Requisitos Previos (Una vez)

```bash
# 1. Verificar que tienes los JSONs generados
ls config/materials_roles.json
ls config/materials_variants.json

# Si no existen, generarlos:
npm run pipeline:generate-json
```

```bash
# 2. Copiar JSONs al addon de Blender
copy config\materials_roles.json src\blender_addons\material_roles_variants\
copy config\materials_variants.json src\blender_addons\material_roles_variants\
```

---

## 🚀 Proceso Completo (3 Pasos)

### **PASO 1: En Blender (2 min)**

1. Abre `StandardBuildings.blend`
2. Presiona `N` → Pestaña **Material Roles**
3. Ejecuta en orden (si es la primera vez):
   - `2️⃣ Clean-Up`
   - `3️⃣ Apply Material Roles`
   - `4️⃣ Build MATERIAL_PALETTE`
   - `5️⃣ Generate Variant Materials`
   - `Ctrl+S` (guardar)
4. **Export automático:**
   - En **Step 6: Export GLB**
   - Verifica que Output Dir = `//assets/models/buildings/`
   - Click en **🚀 Export ALL Collections (Auto)**
   - ✅ Espera ~30-60 segundos

**Resultado:** 7 archivos `*_clean.glb` en `assets/models/buildings/`

---

### **PASO 2: En Terminal (1 min)**

```bash
# Procesa TODOS los GLBs automáticamente
npm run pipeline:process-all
```

**Este comando:**
- ✅ Añade KHR_materials_variants a cada GLB
- ✅ Optimiza con DRACO (~70-85% reducción)
- ✅ Genera archivos `*_final.glb`

**Resultado:** 7 archivos `*_final.glb` listos para React

---

### **PASO 3: En React (30 seg)**

```jsx
import { AdvancedBuildingModel } from './components/AdvancedBuildingModel';

function App() {
  const buildingRef = useRef();
  const [theme, setTheme] = useState('Original');

  return (
    <Canvas>
      <AdvancedBuildingModel
        ref={buildingRef}
        modelPath="/assets/models/buildings/Experience_final.glb"
        theme={theme}
      />

      <button onClick={() => setTheme('Cyberpunk')}>Cyberpunk</button>
      <button onClick={() => setTheme('Mars')}>Mars</button>
      <button onClick={() => setTheme('Pandora')}>Pandora</button>
    </Canvas>
  );
}
```

**Resultado:** Theme switching en <16ms 🔥

---

## 🎉 ¡Listo!

Ahora tienes:
- ✅ 7 edificios con material variants
- ✅ 4 themes disponibles (Original, Cyberpunk, Mars, Pandora)
- ✅ Optimización DRACO (70-85% más pequeños)
- ✅ Theme switching instantáneo en React

---

## 🔄 Para Actualizaciones

Si modificas materiales o añades nuevos:

```bash
# 1. Regenerar JSONs
npm run pipeline:generate-json

# 2. En Blender, repetir pasos 2-5
# 3. Export batch de nuevo
# 4. Reprocesar pipeline
npm run pipeline:process-all
```

---

## 📊 Archivos Generados

```
assets/models/buildings/
├── Experience_clean.glb       (Blender export)
├── Experience_variants.glb    (Con KHR_materials_variants)
├── Experience_final.glb       (Optimizado, usa este) ✅
├── Skills_clean.glb
├── Skills_variants.glb
├── Skills_final.glb           ✅
├── Vision_clean.glb
├── Vision_variants.glb
├── Vision_final.glb           ✅
├── Docs_clean.glb
├── Docs_variants.glb
├── Docs_final.glb             ✅
├── About_clean.glb
├── About_variants.glb
├── About_final.glb            ✅
├── Projects_clean.glb
├── Projects_variants.glb
├── Projects_final.glb         ✅
├── Blog_clean.glb
├── Blog_variants.glb
└── Blog_final.glb             ✅
```

**Usa los archivos `*_final.glb` en tu aplicación React.**

---

## 🆘 Problemas Comunes

### "Cannot find module gltf-pipeline"
```bash
npm install
```

### "materials_roles.json no encontrado" en Blender
```bash
copy config\*.json src\blender_addons\material_roles_variants\
```

### "No collections found" en Blender
Verifica que StandardBuildings.blend tiene colecciones: Experience, Skills, Vision, etc.

### Themes no cambian en React
1. Verifica que usas `*_final.glb` (no `*_clean.glb`)
2. Abre el GLB en https://gltf-viewer.donmccurdy.com/
3. Verifica que KHR_materials_variants está presente

---

## 📖 Documentación Completa

- **[WORKFLOW_AUTOMATED.md](WORKFLOW_AUTOMATED.md)** - Workflow detallado
- **[CHANGELOG_AUTOMATION.md](CHANGELOG_AUTOMATION.md)** - Lista de cambios
- **[PIPELINE.md](../../PIPELINE.md)** - Documentación completa del sistema

---

**⏱️ Tiempo total: ~5 minutos**
**🎯 Resultado: 7 edificios con 4 themes cada uno**
**🚀 Performance: Theme switching <16ms**

---

**Made with ❤️ for HekTek City**
