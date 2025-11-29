# 🔧 Pipeline Corregido - Flujo de Trabajo

**Fecha:** 2025-11-19
**Estado:** Corregido y validado

---

## 📋 Resumen de Cambios

Se corrigió el pipeline completo para el sistema de variantes de materiales. Los cambios principales incluyen:

1. ✅ Nueva estructura de carpetas organizada
2. ✅ Addon de Blender actualizado con ruta correcta
3. ✅ Pipeline Node.js completamente reescrito
4. ✅ Optimización DRACO ahora es OPCIONAL
5. ✅ Versiones `_variants.glb` se guardan como backup
6. ✅ Archivos antiguos renombrados a `_old.glb`
7. ✅ Archivos `_clean.glb` removidos de public/

---

## 🗂️ Estructura de Carpetas

### **Antes (INCORRECTO):**
```
public/assets/models/buildings/
├── About_clean.glb ❌ (los _clean no deberían estar aquí)
├── About_variants.glb ⚠️ (se eliminaban)
└── About.glb ✅

assets/models/quality/standard/
├── About.glb ❌ (ubicación incorrecta)
└── About_clean.glb ❌ (ubicación incorrecta)
```

### **Ahora (CORRECTO):**
```
assets/models/quality/standard/
├── buildings/                    📁 Source files desde Blender
│   └── About_clean.glb          ✅ Exportado desde Blender
│
├── About_variants.glb            ✅ Backup con variantes (sin DRACO)
├── About.glb                     ✅ Versión final para desarrollo
├── About_old.glb                 💾 Backup de versión anterior
└── About_clean.glb               ✅ Clean original

public/assets/models/quality/standard/
└── About.glb                     ✅ Archivo final para producción
```

---

## 🔄 Flujo de Trabajo Completo

### **Paso 1: Blender Addon - Exportar GLB**

**Ubicación:** `tools/blender/addons/material_roles_variants_addon.py`

1. Abrir Blender con el proyecto
2. Ir a la pestaña **Material Roles** (N-Panel)
3. Ejecutar los pasos del addon:
   - `1️⃣ Load JSON Configurations`
   - `3️⃣ Apply Material Roles`
   - `4️⃣ Build MATERIAL_PALETTE` (incluye todos los empties)
   - `5️⃣ Generate Variant Materials`
   - `6️⃣ Export Collection as GLB`

**Resultado:**
```
assets/models/quality/standard/buildings/BuildingName_clean.glb
```

**Importante:**
- El archivo se exporta con el nombre `BuildingName_clean.glb`
- Incluye el objeto `MATERIAL_PALETTE` con todos los empties
- Los objetos están centrados en el origen (0, 0, 0)

---

### **Paso 2: Pipeline Node.js - Procesar Variantes**

**Comando básico:**
```bash
npm run pipeline:process-all
```

**Comando con opciones:**
```bash
# Sin optimización DRACO (recomendado para testing)
npm run pipeline:process-all

# Con optimización DRACO
npm run pipeline:process-all -- --draco

# Con DRACO y generación de low-res
npm run pipeline:process-all -- --draco --lowres
```

**Variables de entorno:**
```bash
ENABLE_DRACO=true npm run pipeline:process-all
ENABLE_LOWRES=true npm run pipeline:process-all
```

---

### **Paso 2.1: Añadir KHR_materials_variants**

**Script:** `tools/gltf/add-khr-variants.js`

**Entrada:**
- `assets/models/quality/standard/buildings/BuildingName_clean.glb`
- `config/materials_variants.json`

**Salida:**
- Temporal: `assets/models/quality/standard/buildings/BuildingName_variants.glb`

**Proceso:**
1. Lee el archivo `_clean.glb`
2. Lee la configuración de variantes desde `materials_variants.json`
3. Añade la extensión `KHR_materials_variants` al GLB
4. Crea los mappings de variantes por tema (Original, Cyberpunk, Mars, Pandora)

---

### **Paso 2.2: Guardar Backup _variants**

**Salida:**
```
assets/models/quality/standard/BuildingName_variants.glb
```

**Propósito:**
- Archivo con variantes KHR pero SIN optimización DRACO
- Se guarda como backup por si la optimización falla
- Útil para debugging y testing

---

### **Paso 2.3: Optimización DRACO (OPCIONAL)**

**Script:** `tools/gltf/optimize-glb.js`

**Si DRACO está habilitado (`--draco`):**
```
Input:  buildings/BuildingName_variants.glb
Output: public/assets/models/quality/standard/BuildingName.glb
```

**Si DRACO está deshabilitado (default):**
```
Copia directa de _variants.glb a public/BuildingName.glb
```

**Ventajas DRACO:**
- ✅ Reduce tamaño de archivo significativamente (30-50%)
- ❌ Tarda más en procesar
- ❌ Puede causar problemas en algunos visores

**Por eso es OPCIONAL ahora**

---

### **Paso 2.4: Generar Low-Res (OPCIONAL)**

**Script:** `tools/pipeline/generate-lowres.js`

Solo se ejecuta si se pasa el flag `--lowres`:
```bash
npm run pipeline:process-all -- --lowres
```

**Salida:**
```
public/assets/models/quality/low_res/BuildingName.glb
```

---

## 📊 Archivos Generados

| Archivo | Ubicación | Descripción |
|---------|-----------|-------------|
| `Name_clean.glb` | `assets/.../buildings/` | Exportado desde Blender (source) |
| `Name_variants.glb` | `assets/.../standard/` | Con KHR variantes, SIN DRACO (backup) |
| `Name.glb` | `assets/.../standard/` | Versión actual para desarrollo |
| `Name_old.glb` | `assets/.../standard/` | Backup de versión anterior |
| `Name.glb` | `public/.../standard/` | Archivo final para producción |

---

## 🧪 Testing del Flujo

### **Test 1: Exportar desde Blender**

1. Abre Blender
2. Selecciona una colección (ej: "Experience")
3. Exporta usando el addon
4. Verifica que existe:
   ```bash
   ls -lh assets/models/quality/standard/buildings/Experience_clean.glb
   ```

### **Test 2: Procesar sin DRACO**

```bash
npm run pipeline:process-all
```

Verifica archivos generados:
```bash
# Debe existir el backup con variantes
ls -lh assets/models/quality/standard/Experience_variants.glb

# Debe existir el archivo final en public
ls -lh public/assets/models/quality/standard/Experience.glb
```

### **Test 3: Verificar variantes en GLB**

Usa un visor GLB online o tu componente MapVariations:
- Carga `public/.../Experience.glb`
- Verifica que tiene la extensión `KHR_materials_variants`
- Cambia entre temas: Original, Cyberpunk, Mars, Pandora
- Verifica que los materiales cambian correctamente

### **Test 4: Procesar con DRACO**

```bash
npm run pipeline:process-all -- --draco
```

Compara tamaños:
```bash
ls -lh assets/models/quality/standard/Experience_variants.glb
ls -lh public/assets/models/quality/standard/Experience.glb
```

El archivo en public/ debe ser más pequeño.

---

## 🐛 Troubleshooting

### **Error: "No se encontró BuildingName_clean.glb"**

**Solución:**
1. Verifica que exportaste desde Blender
2. Verifica la ruta en el addon: debe ser `//assets/models/quality/standard/buildings/`
3. Verifica que el archivo existe:
   ```bash
   ls assets/models/quality/standard/buildings/
   ```

### **Error: "KHR_materials_variants extension not found"**

**Causas posibles:**
1. El archivo `config/materials_variants.json` no existe
   - Ejecuta: `npm run pipeline:generate-json`
2. El archivo no se procesó correctamente
   - Revisa los logs del pipeline
3. Los nombres de materiales no coinciden
   - Verifica el CSV y el JSON

### **Error: Archivos se ven mal en el visor**

**Posibles causas:**
1. Falta el objeto `MATERIAL_PALETTE` en el export de Blender
   - Ejecuta paso `4️⃣ Build MATERIAL_PALETTE` antes de exportar
2. Optimización DRACO causó problemas
   - Prueba sin `--draco`
3. Variantes mal configuradas
   - Verifica el CSV de materiales

### **Warning: "BuildingModel: focusEmpty / insideEmpty not found"**

**Causa:**
- Los empties `focusEmpty` e `insideEmpty` no están en el GLB

**Solución:**
1. Verifica que existen en Blender
2. Verifica que el paso `4️⃣ Build MATERIAL_PALETTE` los incluye
3. Exporta de nuevo

---

## 📝 Notas Importantes

1. **NO elimines los archivos `_variants.glb`** - Son backups importantes
2. **Los `_clean.glb` solo deben estar en** `assets/.../buildings/`
3. **Los archivos en `public/` son los que usa la aplicación React**
4. **DRACO es opcional** - Úsalo solo cuando estés seguro que funciona
5. **Siempre verifica con tus visores** antes de hacer commit

---

## 🚀 Comandos Rápidos

```bash
# Workflow completo básico (sin DRACO)
npm run pipeline:process-all

# Workflow completo con DRACO
npm run pipeline:process-all -- --draco

# Workflow completo con todo
npm run pipeline:process-all -- --draco --lowres

# Solo generar JSON desde CSV
npm run pipeline:generate-json

# Solo optimizar un archivo específico
npm run pipeline:optimize -- input.glb output.glb --draco

# Solo generar low-res
npm run optimize:lowres
```

---

## ✅ Checklist Pre-Commit

- [ ] Exporté todos los edificios desde Blender
- [ ] Ejecuté el pipeline sin errores
- [ ] Verifiqué archivos en MapVariations (cambio de temas funciona)
- [ ] Verifiqué archivos en MapRPG (se ven correctamente)
- [ ] No hay errores en consola del navegador
- [ ] Los archivos `_variants.glb` existen en assets/
- [ ] Los archivos finales `.glb` existen en public/
- [ ] NO hay archivos `_clean.glb` en public/

---

**Última actualización:** 2025-11-19
**Autor:** Claude Code + Hector
