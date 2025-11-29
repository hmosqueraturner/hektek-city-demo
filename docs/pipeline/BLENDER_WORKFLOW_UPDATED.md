# 🎨 Flujo de Trabajo Actualizado - Blender Addon

**Fecha:** 2025-11-19
**Problema resuelto:** Manejo de materiales importados (BlenderKit, Tripo.ai, etc.)

---

## 🎯 Problema Original

Cuando importas materiales desde:
- **BlenderKit** → Nombres como `BK_Metal_001`, `BK_Wood_Oak`
- **Tripo.ai** → Nombres como `Material_tripo_part_0`, `Material_tripo_part_1`
- **Otros addons** → Nombres inconsistentes

El addon original NO procesaba estos materiales porque no estaban en el JSON.

---

## ✅ Solución Implementada

El addon ahora:
1. **Genera CSV con TODOS los materiales** (automático)
2. **`Apply Material Roles` ahora es OPCIONAL** y auto-genera roles para materiales sin mapeo
3. **El pipeline Node.js mapea correctamente** usando el sistema de roles bidireccional

---

## 📋 Flujo Recomendado (NUEVO)

### **OPCIÓN A: SIN renombrar materiales (MÁS RÁPIDO)**

**Mejor si:** Ya tienes nombres decentes y solo quieres variantes

```
1. Blender: 0️⃣ Generate CSV
   → Genera CSV con TODOS los materiales actuales

2. Editor: Editar CSV manualmente
   → Configura variantes (SWAP o PROPERTIES)
   → NO cambies "Material Name"

3. Terminal: npm run pipeline:generate-json
   → Genera materials_roles.json + materials_variants.json

4. Terminal: Copiar JSON a addons/

5. Blender: 1️⃣ Load JSON

6. Blender: SKIP 3️⃣ Apply Roles ⏭️
   → NO ejecutes este paso

7. Blender: 4️⃣ Build MATERIAL_PALETTE

8. Blender: 5️⃣ Generate Variant Materials

9. Blender: 6️⃣ Export GLB
   → Los materiales mantienen nombres originales

10. Terminal: npm run pipeline:process-all
    → El script mapea automáticamente usando JSON
```

**Ventajas:**
- ✅ Más rápido
- ✅ Nombres originales se preservan
- ✅ No necesitas re-generar CSV

---

### **OPCIÓN B: CON renombrar materiales (ESTANDARIZACIÓN)**

**Mejor si:** Quieres nombres consistentes tipo `MAT_Mat_nombre`

```
1. Blender: 0️⃣ Generate CSV
   → Genera CSV con materiales actuales

2. Editor: Editar CSV manualmente
   → Configura variantes

3. Terminal: npm run pipeline:generate-json

4. Terminal: Copiar JSON a addons/

5. Blender: 1️⃣ Load JSON

6. Blender: 3️⃣ Apply Material Roles ✅
   → EJECUTA este paso
   → Renombra materiales según roles
   → Auto-genera roles para materiales sin mapeo

7. Blender: 0️⃣ Generate CSV DE NUEVO
   → Ahora con nombres estandarizados

8. Editor: Revisar CSV y ajustar configuración

9. Terminal: npm run pipeline:generate-json

10. Terminal: Copiar JSON a addons/

11. Blender: 1️⃣ Load JSON

12. Blender: 4️⃣ Build MATERIAL_PALETTE

13. Blender: 5️⃣ Generate Variant Materials

14. Blender: 6️⃣ Export GLB

15. Terminal: npm run pipeline:process-all
```

**Ventajas:**
- ✅ Nombres estandarizados
- ✅ Más fácil identificar materiales
- ✅ Útil si tienes muchos materiales importados

**Desventajas:**
- ⏱️ Más pasos
- 🔄 Requiere re-generar CSV

---

## 🆕 Mejoras en `3️⃣ Apply Material Roles`

### **Antes:**
- Solo procesaba materiales YA en el JSON
- Materiales importados (sin rol) se ignoraban
- No había feedback sobre materiales no mapeados

### **Ahora:**
```python
✅ Materiales con rol en JSON:
   → Renombra según rol definido

✅ Materiales SIN rol en JSON:
   → Auto-genera rol usando generate_material_id()
   → Ejemplo: "BK_Metal_001" → "MAT_Mat_bk_metal_001"

✅ Materiales ya renombrados:
   → Los salta (evita duplicados)

✅ Reporta:
   - Materiales reemplazados
   - Materiales saltados
   - Materiales auto-generados
```

**Salida esperada:**
```
🎯 Aplicando Material Roles (Estandarización de Nombres)
============================================================

🔄 Procesando: MAT_Glass_Window → MAT_Mat_glass_window
   ♻️  Reutilizando material existente
   ✅ Asignado a objeto: Building_Wall

🆕 Material sin rol, generando automáticamente:
   BK_Metal_001 → MAT_Mat_bk_metal_001
   ✨ Creando nuevo material
   ✅ Asignado a objeto: Door_Frame

============================================================
✅ Completado: 15 materiales reemplazados
⏭️  Saltados: 3 (ya eran roles)
🆕 Auto-generados: 5 (sin rol previo)
============================================================

⚠️  IMPORTANTE: Se generaron roles automáticamente.
   Deberías:
   1. Generar CSV de nuevo (0️⃣)
   2. Editar CSV para configurar variantes
   3. Generar JSON (npm run pipeline:generate-json)
   4. Re-cargar JSON en Blender (1️⃣)
```

---

## 🔧 Ejemplos de Nombres Generados

### **BlenderKit:**
```
Original:        BK_Metal_Brushed_001
Auto-generado:   MAT_Mat_bk_metal_brushed_001
```

### **Tripo.ai:**
```
Original:        Material_tripo_part_0
Auto-generado:   MAT_Mat_material_tripo_part_0
```

### **Modelos personalizados:**
```
Original:        Ancient Rune Metal Red
Auto-generado:   MAT_Mat_ancientrunemetalred
```

---

## ⚠️ Casos Especiales

### **Material con nombre duplicado:**

Si tienes:
- `Material.001`
- `Material.002`

El addon genera:
- `MAT_Mat_material001`
- `MAT_Mat_material002`

### **Material con caracteres especiales:**

```
Original:        Glass_Window (2024)
Auto-generado:   MAT_Mat_glass_window_2024

Original:        Metal-Panel_v1.5
Auto-generado:   MAT_Mat_metalpanel_v15
```

---

## 🎨 Workflow Completo Ejemplo

Tienes un proyecto con:
- 10 materiales propios (ya con buenos nombres)
- 15 materiales de BlenderKit
- 8 materiales de un modelo de Tripo.ai

**Paso a paso:**

### **1. Generar CSV inicial**
```python
# En Blender
0️⃣ Generate CSV
```
**Resultado:** CSV con 33 materiales, todos con Role IDs auto-generados

### **2. (OPCIONAL) Estandarizar nombres**
```python
# En Blender
3️⃣ Apply Material Roles
```
**Resultado:**
- 10 materiales propios → Renombrados según roles
- 15 BlenderKit → Auto-generados roles
- 8 Tripo.ai → Auto-generados roles

### **3. Re-generar CSV con nombres nuevos**
```python
# En Blender
0️⃣ Generate CSV
```
**Resultado:** CSV actualizado con nombres estandarizados

### **4. Configurar variantes en CSV**
Edita manualmente:
```csv
Material Name,Role ID,Variant Type,Original,Cyberpunk,Mars,...
MAT_Mat_bk_metal_brushed_001,MAT_Mat_bk_metal_brushed_001,PROPERTIES,MAT_Mat_bk_metal_brushed_001,MAT_Mat_bk_metal_brushed_001__Cyberpunk,...
```

### **5. Generar JSON**
```bash
npm run pipeline:generate-json
```

### **6. Workflow normal del addon**
```python
1️⃣ Load JSON
4️⃣ Build MATERIAL_PALETTE
5️⃣ Generate Variant Materials
6️⃣ Export GLB
```

### **7. Procesar con pipeline**
```bash
npm run pipeline:process-all
```

**Resultado final:**
- ✅ 33 materiales procesados
- ✅ Primitives with variants: >0
- ✅ Variantes funcionando en React

---

## 📊 Comparación: Antes vs Ahora

| Característica | Antes | Ahora |
|----------------|-------|-------|
| Materiales sin rol | ❌ Ignorados | ✅ Auto-generados |
| Renombrado | ⚠️ Obligatorio | ✅ Opcional |
| Feedback | ❌ Mínimo | ✅ Detallado |
| Materiales importados | ❌ Manual | ✅ Automático |
| Duplicados | ⚠️ Posibles | ✅ Evitados |

---

## 🚀 Recomendación Final

**Para tu caso (materiales de BlenderKit + Tripo.ai):**

1. **Primera vez:** Usa **OPCIÓN B** (con renombrado)
   - Estandariza todos los nombres
   - Genera CSV limpio
   - Configura variantes

2. **Siguientes veces:** Usa **OPCIÓN A** (sin renombrado)
   - Solo genera CSV
   - Configura nuevos materiales
   - Exporta directamente

**Tiempo estimado:**
- OPCIÓN A: ~5 minutos
- OPCIÓN B: ~15 minutos (primera vez), luego ~5 minutos

---

## ✅ Checklist de Verificación

Después de ejecutar el workflow:

- [ ] CSV generado tiene TODOS los materiales
- [ ] Si ejecutaste Apply Roles, materiales fueron renombrados
- [ ] JSON generado sin errores
- [ ] JSON copiado a `tools/blender/addons/`
- [ ] JSON cargado en Blender (mensaje de confirmación)
- [ ] MATERIAL_PALETTE construido (>100 materiales)
- [ ] Variantes generadas (materiales con `__Theme`)
- [ ] GLB exportado a `buildings/Name_clean.glb`
- [ ] Pipeline ejecutado: `Primitives with variants > 0`
- [ ] Archivos en `public/.../Name.glb`
- [ ] Probado en MapVariations - cambio de temas funciona

---

**Última actualización:** 2025-11-19
**Autor:** Claude Code + Hector
