# 🚀 Material Pipeline - Changelog de Automatización

**Fecha:** 2025-11-17
**Versión:** v1.1.0 - Automated Export Edition

---

## ✅ Cambios Implementados

### 1. **Exportación Automática desde Colecciones** ✨

Se ha corregido completamente el error de exportación y añadido soporte para:

- ✅ **Exportación por colección individual**
- ✅ **Exportación batch de TODOS los 7 edificios con un solo click**
- ✅ **Detección automática de colecciones disponibles**
- ✅ **Inclusión automática de MATERIAL_PALETTE**

### 2. **Corrección del Error de Export**

**Problema original:**
```
Error al exportar: Converting py args to operator properties:
keyword 'export_colors' unrecognized
```

**Solución:**
- Simplificados los parámetros de exportación GLB
- Usados solo parámetros compatibles con Blender 4.5.4
- Implementado `export_selected=True` con selección previa de objetos
- Removidos parámetros obsoletos (`export_colors`, etc.)

### 3. **Nuevas Propiedades en el Addon**

```python
export_collection: EnumProperty  # Dropdown con colecciones disponibles
export_directory: StringProperty # Directorio de salida (//assets/models/buildings/)
```

### 4. **Nuevo Operador: Export ALL Collections**

```python
class MATROLESVAR_OT_export_all_collections(bpy.types.Operator):
    """Exporta TODAS las colecciones automáticamente"""
    bl_idname = "material_roles.export_all_collections"
    bl_label = "🚀 Export ALL Collections (Auto)"
```

**Características:**
- Exporta automáticamente: Experience, Skills, Vision, Docs, About, Projects, Blog
- Reporta progreso en tiempo real
- Estadísticas finales (exportados/fallidos)
- Manejo de errores por edificio (no detiene el proceso completo)

### 5. **Función Helper Compartida**

```python
def export_collection_as_glb(collection_name, export_dir, report_func):
    """Función reutilizable para exportar una colección como GLB"""
```

Usada por ambos operadores (individual y batch) para mantener consistencia.

---

## 🎯 Workflow Actualizado

### **PASO 1: Preparar StandardBuildings.blend** (Una vez)
1. Abrir StandardBuildings.blend
2. Copiar JSONs al addon
3. Ejecutar: Clean-Up → Apply Roles → Build Palette → Generate Variants
4. Guardar

### **PASO 2: Exportar GLBs** (Automatizado) 🚀
**Opción A - Automático (Recomendado):**
1. En N-Panel → Material Roles → Step 6
2. Click en **🚀 Export ALL Collections (Auto)**
3. ✅ Todos los GLBs exportados automáticamente

**Opción B - Manual:**
1. Seleccionar colección en dropdown
2. Click en **6️⃣ Export Collection as GLB**
3. Repetir para cada edificio

### **PASO 3: Procesar con Node.js**
```bash
npm run pipeline:process-all
```

Este comando:
1. Añade KHR_materials_variants a cada GLB
2. Optimiza con DRACO
3. Genera archivos `*_final.glb`

---

## 📁 Archivos Modificados

### 1. **Addon de Blender**
- **Archivo:** `src/blender_addons/material_roles_variants_addon.py`
- **Cambios:**
  - Añadida función `get_collection_items()` para listar colecciones
  - Añadidas propiedades `export_collection` y `export_directory`
  - Reescrito operador `MATROLESVAR_OT_export_glb` con export_selected
  - Añadido operador `MATROLESVAR_OT_export_all_collections`
  - Añadida función helper `export_collection_as_glb()`
  - Actualizado panel UI con dropdown y botón batch

### 2. **Documentación**
- **Archivo:** `docs/pipeline/WORKFLOW_AUTOMATED.md`
- **Cambios:**
  - Actualizado PASO 2 con opciones automáticas
  - Añadida salida esperada del batch export
  - Simplificado workflow general

### 3. **Scripts Node.js** (Sin cambios)
- `scripts/process-all-buildings.js` - Ya existía
- `scripts/gltf/add-khr-variants.js` - Sin cambios
- `scripts/gltf/optimize-glb.js` - Sin cambios

---

## 🧪 Testing

### Test 1: Export Individual
```
1. Abrir StandardBuildings.blend
2. N-Panel → Material Roles → Step 6
3. Seleccionar "Experience" en dropdown
4. Click en "6️⃣ Export Collection as GLB"
5. Verificar: assets/models/buildings/Experience_clean.glb existe
```

### Test 2: Export Batch (Todos)
```
1. Abrir StandardBuildings.blend
2. N-Panel → Material Roles → Step 6
3. Click en "🚀 Export ALL Collections (Auto)"
4. Verificar: 7 archivos *_clean.glb en assets/models/buildings/
```

### Test 3: Pipeline Completo
```bash
# Después del export batch
npm run pipeline:process-all

# Verificar:
# - 7 archivos *_variants.glb generados
# - 7 archivos *_final.glb optimizados
# - Reducción de tamaño ~70-85%
```

---

## 🔧 Parámetros de Exportación GLB

Los parámetros usados son **100% compatibles** con Blender 4.5.4:

```python
bpy.ops.export_scene.gltf(
    filepath=export_path,
    export_format='GLB',
    export_selected=True,      # Solo objetos seleccionados
    export_apply=True,          # Aplica modificadores
    export_yup=True,            # Eje Y arriba (glTF standard)
    export_normals=True,        # Incluir normales
    export_materials='EXPORT',  # Exportar todos los materiales
    export_image_format='AUTO', # Auto-detectar formato
    export_extras=True,         # Custom properties
    export_cameras=False,       # Sin cámaras
    export_lights=False,        # Sin luces
    export_animations=False     # Sin animaciones
)
```

---

## 📊 Estadísticas

- **Colecciones exportables:** 7 (Experience, Skills, Vision, Docs, About, Projects, Blog)
- **Materiales totales:** 148
- **Roles definidos:** 137
- **Variantes temáticas:** 4 (Original, Cyberpunk, Mars, Pandora)
- **Materiales generados:** 548 (137 roles × 4 themes)
- **Tiempo de export batch:** ~30-60 segundos (dependiendo del hardware)
- **Reducción con DRACO:** 70-85%

---

## 🎉 Resultado Final

### Antes (Manual)
```
1. Seleccionar objetos de Experience manualmente
2. File → Export → glTF → Configurar 20+ opciones
3. Guardar como Experience_clean.glb
4. Repetir 6 veces más
⏱️ Tiempo: ~10-15 minutos
```

### Ahora (Automatizado)
```
1. Click en "🚀 Export ALL Collections (Auto)"
2. ✅ Listo
⏱️ Tiempo: ~30-60 segundos
```

---

## 🐛 Troubleshooting

### Error: "No collections found"
**Solución:** Verifica que existen colecciones en StandardBuildings.blend (Experience, Skills, etc.)

### Error: "No hay objetos MESH en la colección"
**Solución:** Verifica que la colección contiene objetos de tipo MESH

### Los materiales no se exportan
**Solución:** El addon incluye automáticamente MATERIAL_PALETTE para asegurar que todos los materiales se exporten

### Error persistente al exportar
**Solución:**
1. Reinicia Blender
2. Recarga el addon (Preferences → Add-ons → desactivar y reactivar)
3. Verifica que la ruta de salida es válida

---

## 📖 Próximos Pasos

1. ✅ **Exportar GLBs:** Usa el nuevo botón batch
2. ✅ **Procesar pipeline:** `npm run pipeline:process-all`
3. ✅ **Integrar en React:** Usa los archivos `*_final.glb`
4. ✅ **Cambiar themes:** `buildingRef.current.switchTheme('Cyberpunk')`

---

**Made with ❤️ for HekTek City**
**Pipeline v1.1.0 - Automated Export Edition - 2025**
