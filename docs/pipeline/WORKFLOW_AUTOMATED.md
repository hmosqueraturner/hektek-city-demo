# 🤖 WORKFLOW AUTOMATIZADO - HekTek City Pipeline

**Procesamiento automático de los 7 sets de edificios con un solo comando**

---

## 🎯 Workflow Completo (Automatizado)

### **PASO 1: Preparar StandardBuildings.blend** (Una sola vez)

1. **Abrir** `StandardBuildings.blend` en Blender 4.5.4
2. **Copiar JSONs** al directorio del addon:
   ```bash
   copy config\materials_roles.json src\blender_addons\
   copy config\materials_variants.json src\blender_addons\
   ```

3. **En el N-Panel → Material Roles**, ejecutar EN ORDEN:
   - `2️⃣ Clean-Up Complete`
   - `3️⃣ Apply Material Roles`
   - `4️⃣ Build MATERIAL_PALETTE`
   - `5️⃣ Generate Variant Materials`

4. **Guardar** el archivo (`Ctrl+S`)

---

### **PASO 2: Exportar GLBs por Colección** (En Blender) ✅ AUTOMATIZADO

Ahora el addon puede exportar TODOS los edificios automáticamente con un solo click:

#### **Opción A: Export AUTOMÁTICO (RECOMENDADO) 🚀**

1. En el N-Panel → **Material Roles**, ve a **Step 6: Export GLB**
2. Verifica que el **Output Dir** apunte a: `//assets/models/buildings/`
3. Click en **🚀 Export ALL Collections (Auto)**
4. ✅ El addon exportará automáticamente los 7 GLBs:
   - `Experience_clean.glb`
   - `Skills_clean.glb`
   - `Vision_clean.glb`
   - `Docs_clean.glb`
   - `About_clean.glb`
   - `Projects_clean.glb`
   - `Blog_clean.glb`

**Resultado esperado:**
```
============================================================
🚀 Iniciando exportación automática de todos los edificios...
============================================================

📦 Procesando: Experience...
✅ Experience exportado

📦 Procesando: Skills...
✅ Skills exportado

... (continúa para los 7 edificios)

============================================================
🎉 EXPORTACIÓN COMPLETADA
============================================================
✅ Edificios exportados: 7/7
📁 Archivos guardados en: D:\code\portfolio\hektek-city\assets\models\buildings\
```

#### **Opción B: Export manual por colección**

Si prefieres exportar uno a la vez:

1. En el N-Panel → **Material Roles**, ve a **Step 6: Export GLB**
2. Selecciona la colección en el dropdown (ej: `Experience`)
3. Click en **6️⃣ Export Collection as GLB**
4. Repite para los otros 6 sets

---

### **PASO 3: Procesar TODOS los edificios** (Automático) 🚀

Una vez que tengas los 7 archivos `*_clean.glb` exportados:

```bash
npm run pipeline:process-all
```

Este comando automáticamente:
1. ✅ Añade KHR_materials_variants a cada GLB
2. ✅ Optimiza con DRACO (reducción ~80%)
3. ✅ Genera archivos `*_final.glb` listos para React

**Salida esperada:**
```
🚀 Iniciando procesamiento de todos los edificios...

============================================================
🏢 Procesando: Experience
============================================================

📦 PASO 1/2: Añadiendo KHR_materials_variants...
✅ GLB loaded: 148 materials, 120 meshes
✅ Processing complete

⚡ PASO 2/2: Optimizando con DRACO...
📊 Initial size:  12.5 MB
📊 Final size:    2.8 MB
📉 Reduction:     77.60%
✅ Experience procesado exitosamente

... (repite para los otros 6 edificios)

============================================================
🎉 PROCESAMIENTO COMPLETADO
============================================================
✅ Edificios procesados: 7/7
📁 Archivos finales en: assets\models\buildings
ℹ️  Los archivos *_final.glb están listos para usar en React
```

---

## 📋 Archivos Generados

Después del procesamiento, tendrás:

```
assets/models/buildings/
├── Experience_clean.glb       (original exportado de Blender)
├── Experience_variants.glb    (con KHR_materials_variants)
├── Experience_final.glb       (optimizado, listo para React) ✅
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

## ⚡ Comandos NPM Disponibles

```bash
# Generar JSONs desde CSV (solo una vez)
npm run pipeline:generate-json

# Procesar TODOS los edificios automáticamente
npm run pipeline:process-all

# Validar instalación del pipeline
npm run pipeline:validate

# Comandos individuales (si necesitas procesar uno solo)
npm run pipeline:add-variants <input.glb> <output.glb>
npm run pipeline:optimize <input.glb> <output.glb>
```

---

## 🔧 SOLUCIÓN AL ERROR DE EXPORT

Si al exportar en Blender ves:
```
Error al exportar: Converting py args to operator properties: keyword 'export_colors' unrecognized
```

**Solución:**

1. Recarga el addon:
   - `Edit` → `Preferences` → `Add-ons`
   - Desmarca "Material Roles & Variants Pipeline"
   - Márcalo de nuevo

2. O reinicia Blender

Ya corregí el error en el addon (`export_colors` no existe en Blender 4.5.4).

---

## 📊 Checklist Completo

- [ ] ✅ JSONs generados: `npm run pipeline:generate-json`
- [ ] ✅ Addon instalado y actualizado en Blender
- [ ] ✅ StandardBuildings.blend: Clean-Up → Apply Roles → Build Palette → Generate Variants
- [ ] ✅ Guardar StandardBuildings.blend
- [ ] ✅ Exportar 7 GLBs clean (uno por cada set)
- [ ] ✅ Ejecutar: `npm run pipeline:process-all`
- [ ] ✅ Verificar archivos `*_final.glb` generados
- [ ] ✅ Usar en React

---

## 🎮 Uso en React

```jsx
import { AdvancedBuildingModel } from './components/AdvancedBuildingModel';

<AdvancedBuildingModel
  modelPath="/assets/models/buildings/Experience_final.glb"
  theme="Cyberpunk"  // o 'Original', 'Mars', 'Pandora'
  onThemeChange={(theme) => console.log('Theme:', theme)}
/>
```

---

**Made with ❤️ for HekTek City**
**Pipeline v1.0.0 - 2025**
