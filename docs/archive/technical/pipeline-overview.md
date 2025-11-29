# ⚡ HekTek City - Material Roles & Variants Pipeline

**Sistema profesional AAA para gestión de material roles y variantes temáticas**

Blender 4.5.4 → glTF/GLB → React Three Fiber

---

## 🎯 ¿Qué es esto?

Este pipeline te permite cambiar el tema visual completo de HekTek City en tiempo real (< 16ms):

- **Original** - El estilo base de HekTek City
- **Cyberpunk** - Estética neon y futurista
- **Mars** - Paleta marciana rojiza
- **Pandora** - Colores alienígenas y bioluminiscentes

Todo sin recargar los modelos, solo cambiando materiales.

---

## 📊 Estadísticas

- **148 materiales** únicos procesados
- **137 roles** definidos
- **4 variantes** temáticas (Original, Cyberpunk, Mars, Pandora)
- **548 materiales** totales generados (137 roles × 4 themes)
- **<16ms** theme switching (1 frame @ 60fps)
- **70-85%** reducción de tamaño con DRACO

---

## 🚀 Inicio Rápido

### 1. Instalar Dependencias

Ya están instaladas en tu `package.json`:

```bash
npm install
```

### 2. Generar JSONs (Una vez)

```bash
npm run pipeline:generate-json
```

Esto lee el CSV y genera:
- `config/materials_roles.json`
- `config/materials_variants.json`

### 3. Instalar Addon en Blender

1. Abre Blender 4.5.4
2. Edit → Preferences → Add-ons → Install
3. Selecciona: `src/blender_addons/material_roles_variants/__init__.py`
4. Activa el addon

### 4. Leer el Workflow Completo

📖 **[WORKFLOW.md](docs/pipeline/WORKFLOW.md)** - Guía completa paso a paso

---

## 📁 Estructura del Pipeline

```
hektek-city/
├── src/
│   ├── blender_addons/
│   │   └── material_roles_variants/     # Addon de Blender 4.5.4
│   └── components/
│       └── AdvancedBuildingModel.jsx    # Componente React con variants
├── scripts/
│   ├── blender/
│   │   └── generate_json_from_csv.py    # Generador de JSONs
│   └── gltf/
│       ├── add-khr-variants.js          # Aplicador KHR_materials_variants
│       └── optimize-glb.js              # Optimizador con DRACO/KTX2
├── config/
│   ├── materials_roles.json             # Material → Rol
│   └── materials_variants.json          # Rol → Material por theme
├── docs/
│   └── pipeline/
│       ├── README.md                    # Documentación completa
│       ├── QUICKSTART.md                # Guía de 5 minutos
│       ├── PROJECT_STRUCTURE.md         # Estructura del proyecto
│       └── WORKFLOW.md                  # Workflow específico HekTek City
└── validate_pipeline.py                 # Validador del sistema
```

---

## 🔄 Workflow Resumido

### En Blender (StandardBuildings.blend)

1. **Import CSV & Generate JSONs** (una vez)
2. **Clean-Up** - Purga materiales no usados
3. **Apply Roles** - Reemplaza materiales por roles
4. **Build Palette** - Crea objeto con todos los materiales
5. **Generate Variants** - Genera materiales de variantes
6. **Export GLB** - Exporta cada edificio

### En Terminal

```bash
# Aplicar KHR_materials_variants
node scripts/gltf/add-khr-variants.js \
  Experience_clean.glb \
  config/materials_variants.json \
  Experience_variants.glb

# Optimizar (opcional pero recomendado)
node scripts/gltf/optimize-glb.js \
  Experience_variants.glb \
  Experience_final.glb \
  --draco --cleanup
```

### En React

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
        onThemeChange={(newTheme) => console.log('Theme:', newTheme)}
      />

      {/* Botones para cambiar theme */}
      <button onClick={() => setTheme('Cyberpunk')}>Cyberpunk</button>
      <button onClick={() => setTheme('Mars')}>Mars</button>
      <button onClick={() => setTheme('Pandora')}>Pandora</button>
    </Canvas>
  );
}
```

---

## 📦 Scripts Disponibles

```bash
# Pipeline
npm run pipeline:generate-json    # Generar JSONs desde CSV
npm run pipeline:validate          # Validar instalación del pipeline

# Desarrollo
npm run dev                        # Iniciar servidor de desarrollo
npm run build                      # Build para producción
npm run preview                    # Preview del build

# Optimización
npm run optimize:lowres            # Generar versiones lowres
```

---

## 📖 Documentación Completa

- 📋 **[INDEX](docs/pipeline/README.md)** - Punto de entrada principal
- ⭐ **[QUICKSTART](docs/pipeline/QUICKSTART.md)** - Comienza aquí (5 min)
- 🔄 **[WORKFLOW](docs/pipeline/WORKFLOW.md)** - Workflow específico HekTek City
- 📁 **[STRUCTURE](docs/pipeline/PROJECT_STRUCTURE.md)** - Arquitectura del sistema

---

## 🎨 Addon de Blender

### Ubicación

```
src/blender_addons/material_roles_variants/__init__.py
```

### Características

- ✅ **6 operadores profesionales**
- ✅ **Panel UI completo** en N-Panel
- ✅ **Compatible** con Blender 4.5.4
- ✅ **Workflow** de principio a fin
- ✅ **Validación** completa de APIs

### Operadores

1. **Import CSV & Generate JSONs** - Carga CSV y genera JSONs
2. **Clean-Up Complete** - Purga materiales/nodos no usados
3. **Apply Material Roles** - Reemplaza materiales por roles
4. **Build MATERIAL_PALETTE** - Crea objeto con todos los materiales
5. **Generate Variant Materials** - Genera MAT_Role__Theme
6. **Export Clean GLB** - Exporta con configuración óptima

---

## ⚛️ Componente React

### Ubicación

```
src/components/AdvancedBuildingModel.jsx
```

### API

```jsx
const buildingRef = useRef();

// Cambiar theme
buildingRef.current.switchTheme('Cyberpunk');

// Obtener info
const info = buildingRef.current.getModelInfo();
console.log(info);
// {
//   materialsCount: 148,
//   hasVariants: true,
//   variantsCount: 3,
//   availableThemes: ['Original', 'Cyberpunk', 'Mars', 'Pandora'],
//   currentTheme: 'Original',
//   loadingState: 'loaded'
// }

// Obtener themes disponibles
const themes = buildingRef.current.getAvailableThemes();
// ['Original', 'Cyberpunk', 'Mars', 'Pandora']

// Reset a original
buildingRef.current.resetToOriginal();
```

---

## 🔧 Scripts Node.js

### add-khr-variants.js

Añade la extensión KHR_materials_variants a un GLB.

```bash
node scripts/gltf/add-khr-variants.js \
  input.glb \
  config/materials_variants.json \
  output.glb
```

### optimize-glb.js

Optimiza GLB con DRACO, KTX2, deduplicación, etc.

```bash
node scripts/gltf/optimize-glb.js input.glb output.glb [options]

# Opciones:
--draco              # Compresión DRACO (default: true)
--ktx2               # Compresión KTX2 texturas (default: false)
--cleanup            # Limpiar metadata (default: true)
--reorder            # Reordenar buffers (default: true)
--no-draco           # Deshabilitar DRACO
```

---

## 🐛 Troubleshooting

### Addon no aparece en Blender

```bash
# Verifica instalación
ls ~/.config/blender/4.5/scripts/addons/ | grep material_roles_variants

# Reinstala
# Windows
copy src\blender_addons\material_roles_variants\__init__.py "%APPDATA%\Blender Foundation\Blender\4.5\scripts\addons\material_roles_variants.py"
```

### "materials_roles.json no encontrado"

```bash
# Copiar JSONs al directorio del addon
copy config\materials_roles.json src\blender_addons\material_roles_variants\
copy config\materials_variants.json src\blender_addons\material_roles_variants\
```

### Themes no cambian en React

1. Verifica que usas `*_variants.glb` (no `*_clean.glb`)
2. Verifica que ejecutaste `add-khr-variants.js`
3. Abre el GLB en https://gltf-viewer.donmccurdy.com/
4. Verifica que la extensión KHR_materials_variants esté presente

---

## 🏆 Características Destacadas

### 🎨 Diseño AAA

- Workflow profesional de estudio de videojuegos
- Pipeline idempotente (sin duplicación de datos)
- Validación completa en cada paso
- Documentación exhaustiva

### ⚡ Performance

- Theme switching: **<16ms** (1 frame @ 60fps)
- Reducción de tamaño: **70-85%** con DRACO
- Zero overhead en runtime
- Memory footprint mínimo

### 🔧 Developer Experience

- Instalador automático
- Validador completo
- Ejemplos de código listos para usar
- API bien documentada

---

## 💎 Tecnologías

- **Blender:** 4.5.4
- **glTF:** 2.0
- **KHR_materials_variants:** Spec oficial de Khronos
- **Three.js:** r160+
- **React Three Fiber:** 8+
- **@gltf-transform:** 4.2+
- **DRACO:** 1.5.7+
- **Node.js:** 18+

---

## 📄 Licencia

Este pipeline es **propietario** y parte del proyecto HekTek City.

© 2025 HekTek City. Todos los derechos reservados.

---

## 🎉 ¡Listo para Empezar!

1. ✅ Lee el **[WORKFLOW.md](docs/pipeline/WORKFLOW.md)**
2. ✅ Instala el addon en Blender
3. ✅ Genera los JSONs: `npm run pipeline:generate-json`
4. ✅ Sigue el workflow para cada edificio

---

**Made with ❤️ for HekTek City**
**Pipeline v1.0.0 - 2025**
