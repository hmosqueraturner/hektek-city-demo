# 🚀 WORKFLOW - Material Variants Pipeline para HekTek City

**Guía específica para trabajar con StandardBuildings.blend y exportar edificios individuales**

---

## 📋 Tabla de Contenidos

1. [Requisitos Previos](#requisitos-previos)
2. [Estructura del Proyecto](#estructura-del-proyecto)
3. [Instalación del Addon](#instalación-del-addon)
4. [Workflow Completo](#workflow-completo)
5. [Exportación por Edificio](#exportación-por-edificio)
6. [Procesamiento Node.js](#procesamiento-nodejs)
7. [Integración en React](#integración-en-react)
8. [Troubleshooting](#troubleshooting)

---

## 🔧 Requisitos Previos

### Software Necesario

- **Blender 4.5.4** (exactamente esta versión)
- **Node.js** 18+ y npm
- **Python** 3.8+
- **Git** (para control de versiones)

### Verificar Instalación

```bash
# Verificar versiones
blender --version  # Debe ser 4.5.x
node --version     # Debe ser >= 18.0.0
python3 --version  # Debe ser >= 3.8
```

---

## 📁 Estructura del Proyecto

```
hektek-city/
├── assets/
│   ├── blender/
│   │   └── StandardBuildings.blend      # Tu .blend MASTER
│   ├── datosAll - material_report_final_NewBuildings.csv  # CSV de materiales
│   └── models/
│       └── buildings/                   # GLBs exportados
│           ├── Experience.glb
│           ├── Skills.glb
│           ├── Vision.glb
│           ├── Blog.glb
│           ├── Projects.glb
│           ├── About.glb
│           └── Docs.glb
├── config/
│   ├── materials_roles.json            # Generado automáticamente
│   └── materials_variants.json         # Generado automáticamente
├── scripts/
│   ├── blender/
│   │   └── generate_json_from_csv.py
│   └── gltf/
│       ├── add-khr-variants.js
│       └── optimize-glb.js
├── src/
│   ├── blender_addons/
│   │   └── material_roles_variants/
│   │       └── __init__.py
│   └── components/
│       └── AdvancedBuildingModel.jsx
└── validate_pipeline.py
```

---

## 🎨 Instalación del Addon

### Método 1: Instalación desde Blender (Recomendado)

1. Abre **Blender 4.5.4**
2. Ve a `Edit` → `Preferences` → `Add-ons`
3. Click en `Install...`
4. Navega a `D:\code\portfolio\hektek-city\src\blender_addons\material_roles_variants\__init__.py`
5. Selecciona el archivo y click en `Install Add-on`
6. Busca "Material Roles" en la lista y activa el checkbox
7. Guarda las preferencias

### Método 2: Instalación Manual

```bash
# Windows
copy src\blender_addons\material_roles_variants\__init__.py "%APPDATA%\Blender Foundation\Blender\4.5\scripts\addons\material_roles_variants.py"

# Linux/Mac
cp src/blender_addons/material_roles_variants/__init__.py ~/.config/blender/4.5/scripts/addons/material_roles_variants.py
```

### Verificación

1. Abre Blender
2. Presiona `N` en la vista 3D
3. Deberías ver la pestaña **"Material Roles"**

---

## 🔄 Workflow Completo

### PASO 1: Generar JSONs desde CSV (Una vez)

Este paso se ejecuta **UNA SOLA VEZ** o cuando actualices el CSV de materiales.

```bash
# Desde la raíz del proyecto
npm run pipeline:generate-json
```

Esto generará:
- `config/materials_roles.json` (148 materiales)
- `config/materials_variants.json` (137 roles × 4 themes)

**Resultado esperado:**
```
✅ materials_roles.json generado: 148 materiales
✅ materials_variants.json generado
   - Original: 137 roles
   - Cyberpunk: 137 roles
   - Mars: 137 roles
   - Pandora: 137 roles
```

---

### PASO 2: Trabajar en StandardBuildings.blend (MASTER)

Este es tu archivo **MAESTRO** donde aplicas los material roles a todos los edificios.

#### 2.1: Abrir StandardBuildings.blend

```bash
blender assets/blender/StandardBuildings.blend
```

#### 2.2: Copiar JSONs al directorio del addon

Los JSONs generados deben estar en el mismo directorio que el addon para que funcionen.

```bash
# Copiar JSONs al directorio del addon
# Windows (PowerShell)
copy config\materials_roles.json src\blender_addons\material_roles_variants\
copy config\materials_variants.json src\blender_addons\material_roles_variants\

# Linux/Mac
cp config/materials_roles.json src/blender_addons/material_roles_variants/
cp config/materials_variants.json src/blender_addons/material_roles_variants/
```

#### 2.3: Aplicar Material Roles (En Blender)

1. Presiona `N` para abrir el N-Panel
2. Ve a la pestaña **"Material Roles"**
3. Ejecuta los siguientes pasos en orden:

**Paso 2: Clean-Up**
- Click en `2️⃣ Clean-Up Complete`
- Esto purga materiales no usados

**Paso 3: Apply Roles**
- Click en `3️⃣ Apply Material Roles`
- Esto reemplaza todos los materiales por sus roles (ej: `MAT_Docs_Ala_Der` → `MAT_Mat_docs_ala_der`)

**Paso 4: Build Palette**
- Click en `4️⃣ Build MATERIAL_PALETTE`
- Esto crea un objeto oculto con TODOS los materiales

**Paso 5: Generate Variants**
- Click en `5️⃣ Generate Variant Materials`
- Esto genera `MAT_Role__Cyberpunk`, `MAT_Role__Mars`, `MAT_Role__Pandora`

**Paso 6: Guardar**
- `File` → `Save`
- ✅ **StandardBuildings.blend está listo**

---

### PASO 3: Exportar Edificios Individuales

Cada edificio tiene su propio `.blend` con markers, empties, ubicación, etc. Ahora necesitas **exportar cada uno**.

#### Archivos .blend individuales:

- `Experience.blend`
- `Skills.blend`
- `Vision.blend`
- `Blog.blend`
- `Projects.blend`
- `About.blend`
- `Docs.blend`

#### Proceso para CADA edificio:

1. **Abrir el .blend del edificio**
   ```bash
   blender assets/blender/Experience.blend
   ```

2. **En el N-Panel → Material Roles**
   - Los materiales ya deberían tener los roles aplicados (si usaste el mismo StandardBuildings.blend)
   - Si no, repite el proceso del PASO 2

3. **Exportar GLB**
   - Click en `6️⃣ Export Clean GLB`
   - Guarda como: `assets/models/buildings/Experience_clean.glb`

4. **Repetir para cada edificio**

**Nombres de archivo sugeridos:**
- `Experience_clean.glb`
- `Skills_clean.glb`
- `Vision_clean.glb`
- `Blog_clean.glb`
- `Projects_clean.glb`
- `About_clean.glb`
- `Docs_clean.glb`

---

### PASO 4: Aplicar KHR_materials_variants (Node.js)

Para **CADA GLB exportado**, aplica la extensión KHR_materials_variants:

```bash
# Copiar materials_variants.json a la ubicación correcta
cp config/materials_variants.json assets/models/buildings/

# Para Experience
node scripts/gltf/add-khr-variants.js \
  assets/models/buildings/Experience_clean.glb \
  config/materials_variants.json \
  assets/models/buildings/Experience_variants.glb

# Para Skills
node scripts/gltf/add-khr-variants.js \
  assets/models/buildings/Skills_clean.glb \
  config/materials_variants.json \
  assets/models/buildings/Skills_variants.glb

# Repetir para cada edificio...
```

**Resultado esperado:**
```
🚀 Starting KHR_materials_variants injection...
📖 Loading GLB: assets/models/buildings/Experience_clean.glb
✅ GLB loaded: 148 materials, 120 meshes
📦 Creating variants: Cyberpunk, Mars, Pandora
✅ Processing complete:
   - Meshes processed: 120
   - Primitives with variants: 350
   - Variants created: 3
🎉 Success! Variants added to Experience_variants.glb
```

---

### PASO 5: Optimizar GLBs (Opcional pero Recomendado)

Reduce el tamaño de los GLBs en ~70-85%:

```bash
# Para Experience
node scripts/gltf/optimize-glb.js \
  assets/models/buildings/Experience_variants.glb \
  assets/models/buildings/Experience_final.glb \
  --draco --cleanup

# Para Skills
node scripts/gltf/optimize-glb.js \
  assets/models/buildings/Skills_variants.glb \
  assets/models/buildings/Skills_final.glb \
  --draco --cleanup

# Repetir para cada edificio...
```

**Resultado esperado:**
```
🚀 Starting GLB optimization pipeline...
📊 Initial size:  12.5 MB
📊 Final size:    2.8 MB
📉 Reduction:     77.60% (9.7 MB saved)
🎉 Success! Optimized GLB saved to: Experience_final.glb
```

---

## ⚛️ Integración en React

### Uso Básico

```jsx
import { AdvancedBuildingModel } from './components/AdvancedBuildingModel';
import { useRef, useState } from 'react';

function HekTekCity() {
  const buildingRef = useRef();
  const [theme, setTheme] = useState('Original');

  return (
    <Canvas>
      <AdvancedBuildingModel
        ref={buildingRef}
        modelPath="/assets/models/buildings/Experience_final.glb"
        theme={theme}
        position={[0, 0, 0]}
        onLoad={(info) => {
          console.log('Building loaded:', info);
        }}
        onThemeChange={(newTheme) => {
          console.log('Theme changed to:', newTheme);
        }}
      />

      {/* UI para cambiar themes */}
      <div style={{ position: 'fixed', top: 20, right: 20 }}>
        {['Original', 'Cyberpunk', 'Mars', 'Pandora'].map(t => (
          <button key={t} onClick={() => setTheme(t)}>
            {t}
          </button>
        ))}
      </div>
    </Canvas>
  );
}
```

### Uso Avanzado con Ref API

```jsx
function HekTekCity() {
  const buildingRef = useRef();

  const handleButtonClick = () => {
    // Cambiar theme programáticamente
    buildingRef.current?.switchTheme('Cyberpunk');

    // Obtener info del modelo
    const info = buildingRef.current?.getModelInfo();
    console.log('Model info:', info);

    // Obtener themes disponibles
    const themes = buildingRef.current?.getAvailableThemes();
    console.log('Available themes:', themes);
  };

  return (
    <Canvas>
      <AdvancedBuildingModel
        ref={buildingRef}
        modelPath="/assets/models/buildings/Experience_final.glb"
      />
      <button onClick={handleButtonClick}>Switch Theme</button>
    </Canvas>
  );
}
```

---

## 🐛 Troubleshooting

### Problema: "materials_roles.json no encontrado" en Blender

**Solución:**
```bash
# Copiar JSONs al directorio del addon
copy config\materials_roles.json src\blender_addons\material_roles_variants\
copy config\materials_variants.json src\blender_addons\material_roles_variants\
```

### Problema: El addon no aparece en Blender

**Solución:**
1. Verifica versión de Blender: `blender --version` (debe ser 4.5.x)
2. Reinstala el addon desde Preferences
3. Reinicia Blender

### Problema: "KHR_materials_variants extension not found" en React

**Solución:**
- Asegúrate de haber ejecutado `add-khr-variants.js` en el GLB
- Verifica que estás cargando el archivo `*_variants.glb` y no `*_clean.glb`

### Problema: Los themes no cambian

**Solución:**
1. Abre el GLB en un visor glTF (ej: https://gltf-viewer.donmccurdy.com/)
2. Verifica que la extensión KHR_materials_variants esté presente
3. Verifica que los materiales variantes (`MAT_Role__Theme`) existan

---

## 🎯 Script de Procesamiento Completo

Para facilitar el proceso, puedes crear un script bash:

```bash
#!/bin/bash
# process_all_buildings.sh

BUILDINGS=("Experience" "Skills" "Vision" "Blog" "Projects" "About" "Docs")

for BUILDING in "${BUILDINGS[@]}"
do
  echo "Processing $BUILDING..."

  # Add variants
  node scripts/gltf/add-khr-variants.js \
    "assets/models/buildings/${BUILDING}_clean.glb" \
    "config/materials_variants.json" \
    "assets/models/buildings/${BUILDING}_variants.glb"

  # Optimize
  node scripts/gltf/optimize-glb.js \
    "assets/models/buildings/${BUILDING}_variants.glb" \
    "assets/models/buildings/${BUILDING}_final.glb" \
    --draco --cleanup

  echo "✅ $BUILDING processed"
done

echo "🎉 All buildings processed!"
```

---

## 📊 Resumen del Workflow

### Una Vez (Setup)
1. ✅ Generar JSONs desde CSV
2. ✅ Instalar addon en Blender
3. ✅ Aplicar roles en StandardBuildings.blend

### Por Cada Edificio
1. ✅ Abrir .blend individual
2. ✅ Exportar GLB clean
3. ✅ Aplicar KHR_materials_variants
4. ✅ Optimizar con DRACO
5. ✅ Usar en React

### Tiempos Estimados
- **Setup inicial:** ~10 minutos
- **Por edificio:** ~3 minutos
- **Total (7 edificios):** ~30 minutos

---

## 🎓 Comandos Rápidos de Referencia

```bash
# Generar JSONs
npm run pipeline:generate-json

# Validar pipeline
npm run pipeline:validate

# Añadir variantes a un GLB
node scripts/gltf/add-khr-variants.js input.glb config/materials_variants.json output.glb

# Optimizar GLB
node scripts/gltf/optimize-glb.js input.glb output.glb --draco --cleanup
```

---

**Made with ❤️ for HekTek City**
**Pipeline v1.0.0 - 2025**
