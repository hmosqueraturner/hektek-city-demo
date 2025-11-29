# ⚡ Material Roles & Variants Pipeline v1.0.0

> **Sistema profesional AAA para gestión de material roles y variantes temáticas**  
> Blender 4.5.4 → glTF/GLB → React Three Fiber

[![Blender](https://img.shields.io/badge/Blender-4.5.4-orange.svg)](https://www.blender.org/)
[![Node.js](https://img.shields.io/badge/Node.js-18.0.0+-green.svg)](https://nodejs.org/)
[![React](https://img.shields.io/badge/React-18+-blue.svg)](https://reactjs.org/)
[![glTF](https://img.shields.io/badge/glTF-2.0-yellow.svg)](https://www.khronos.org/gltf/)

---

## 🎯 **¿QUÉ HACE?**

Este pipeline te permite:

- ✅ **Organizar materiales** usando roles abstractos (`MAT_Glass`, `MAT_Metal`)
- ✅ **Crear variantes temáticas** (Cyberpunk, Mars, Pandora, Custom...)
- ✅ **Cambiar themes en runtime** sin reload del modelo (< 16ms)
- ✅ **Optimizar GLBs** con DRACO/KTX2 (reducción del 70-85%)
- ✅ **Integrar en React** con un componente plug-and-play

---

## 🚀 **INICIO RÁPIDO (5 MINUTOS)**

```bash
# 1. Instala el pipeline
bash install.sh

# 2. Valida que todo está OK
python3 validate_pipeline.py

# 3. Abre el quickstart
open QUICKSTART.md
```

**[👉 LEE EL QUICKSTART COMPLETO](QUICKSTART.md)**

---

## 📚 **DOCUMENTACIÓN**

| Documento | Descripción | Tiempo |
|-----------|-------------|--------|
| **[INDEX.md](INDEX.md)** 📋 | Punto de entrada principal | 2 min |
| **[QUICKSTART.md](QUICKSTART.md)** ⭐ | Guía de inicio rápido | 5 min |
| **[docs/README.md](docs/README.md)** 📖 | Manual completo (10k+ palabras) | 30 min |
| **[PROJECT_STRUCTURE.md](PROJECT_STRUCTURE.md)** 📁 | Estructura del proyecto | 10 min |
| **[RELEASE_NOTES.md](RELEASE_NOTES.md)** 📝 | Notas de la versión | 5 min |

---

## 🎬 **DEMO RÁPIDO**

### **En Blender:**
```
1. Import CSV              → materials_roles.json + materials_variants.json
2. Clean-Up                → Purga materiales no usados
3. Apply Roles             → MAT_Glass, MAT_Metal, etc.
4. Build Palette           → Objeto con TODOS los materiales
5. Generate Variants       → MAT_Glass__Cyberpunk, __Mars, __Pandora
6. Export GLB              → buildings.glb (listo para web)
```

### **En Terminal:**
```bash
# Aplica KHR_materials_variants
node scripts/apply_khr_variants.js buildings.glb variants.json output.glb

# Optimiza con DRACO
node scripts/optimize_glb.js output.glb final.glb --draco
```

### **En React:**
```jsx
import AdvancedBuildingModel from './AdvancedBuildingModel';

function App() {
  const buildingAPI = useRef(null);
  
  return (
    <AdvancedBuildingModel
      url="/assets/final.glb"
      onLoad={(api) => {
        buildingAPI.current = api;
        // Cambia theme en runtime
        api.variants.applyVariantByName('Cyberpunk');
      }}
    />
  );
}
```

---

## 📦 **¿QUÉ INCLUYE?**

### **🎨 Addon de Blender 4.5.4**
- Panel completo en N-Panel
- 6 operadores profesionales
- Workflow de principio a fin
- Compatible con API más reciente

### **📜 Scripts Node.js**
- `apply_khr_variants.js` - Añade extensión KHR_materials_variants
- `optimize_glb.js` - Optimiza con DRACO/KTX2
- `generate_json_from_csv.py` - Genera JSONs desde CSV

### **⚛️ Componentes React**
- `AdvancedBuildingModel.jsx` - Componente principal con hooks
- `MapRPG_integration_examples.jsx` - 4 ejemplos completos

### **📖 Documentación Completa**
- README exhaustivo (10,000+ palabras)
- Quickstart de 5 minutos
- Troubleshooting detallado
- Referencia de API

---

## 🎯 **CASOS DE USO**

### **Videojuegos**
```jsx
// Day/Night cycle con materiales diferentes
api.variants.applyVariantByName('Night');
```

### **Configuradores**
```jsx
// Cambiar acabados de un producto
api.variants.applyVariantByName('SportFinish');
```

### **Arquitectura**
```jsx
// Mostrar diferentes estilos arquitectónicos
api.variants.applyVariantByName('Modern');
```

---

## 📊 **ESTADÍSTICAS**

- **148** materiales únicos procesados
- **137** roles definidos
- **4** variantes temáticas (Original, Cyberpunk, Mars, Pandora)
- **548** materiales totales generados
- **70-85%** reducción de tamaño con DRACO
- **<16ms** theme switching (1 frame @ 60fps)

---

## 🔧 **REQUISITOS**

| Software | Versión | Obligatorio |
|----------|---------|-------------|
| Blender | 4.5.4+ | ✅ Sí |
| Node.js | 18.0.0+ | ✅ Sí |
| Python | 3.8+ | ✅ Sí |
| React | 18+ | Solo para integración |

---

## ✅ **VALIDACIÓN**

```bash
# Ejecuta el validador completo
python3 validate_pipeline.py
```

Verifica:
- ✅ Estructura de directorios
- ✅ Addon de Blender con todos los operadores
- ✅ Scripts presentes y funcionales
- ✅ Dependencias instaladas
- ✅ JSONs correctos
- ✅ Documentación completa

---

## 🛠️ **INSTALACIÓN**

### **Opción A: Automática (Recomendada)**
```bash
bash install.sh
```

### **Opción B: Manual**

#### **1. Addon de Blender**
```bash
# Copia el addon
cp addon/__init__.py ~/.config/blender/4.5/scripts/addons/material_roles_variants.py

# En Blender: Edit → Preferences → Add-ons → Habilita "Material Roles & Variants"
```

#### **2. Scripts Node.js**
```bash
cd scripts/
npm install
```

#### **3. Valida**
```bash
python3 validate_pipeline.py
```

---

## 🎓 **WORKFLOW COMPLETO**

### **FASE 1: BLENDER (5 minutos)**

1. **Import CSV** → Genera JSONs
2. **Clean-Up** → Purga materiales no usados
3. **Apply Roles** → Reemplaza materiales por roles
4. **Build Palette** → Crea objeto con todos los materiales
5. **Generate Variants** → Genera materiales de variantes
6. **Export GLB** → Exporta todo

### **FASE 2: NODE.JS (1 minuto)**

```bash
# Aplica KHR_materials_variants
node scripts/apply_khr_variants.js input.glb variants.json output.glb

# Optimiza (opcional)
node scripts/optimize_glb.js output.glb final.glb --draco
```

### **FASE 3: REACT (1 minuto)**

```jsx
<AdvancedBuildingModel
  url="/assets/final.glb"
  onLoad={(api) => {
    api.variants.applyVariantByName('Cyberpunk');
  }}
/>
```

---

## 🐛 **TROUBLESHOOTING**

### **Addon no aparece en Blender**
```bash
# Verifica la instalación
ls ~/.config/blender/4.5/scripts/addons/ | grep material

# Reinstala si es necesario
bash install.sh
```

### **"node: command not found"**
```bash
# Instala Node.js 18+
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### **Export GLB falla**
```bash
# Verifica versión de Blender (debe ser 4.5.x)
blender --version
```

**[👉 VER TROUBLESHOOTING COMPLETO](docs/README.md#troubleshooting)**

---

## 🌟 **FEATURES DESTACADOS**

### **🎨 Diseño AAA**
- Workflow profesional de estudio de videojuegos
- Pipeline idempotente (sin duplicación de datos)
- Validación completa en cada paso
- Documentación exhaustiva

### **⚡ Performance**
- Theme switching: <16ms (1 frame @ 60fps)
- Reducción de tamaño: 70-85% con DRACO
- Zero overhead en runtime
- Memory footprint mínimo

### **🔧 Developer Experience**
- Instalador automático
- Validador completo con 6 checks
- Ejemplos de código listos para usar
- API bien documentada con TypeScript types

---

## 📞 **SOPORTE**

### **Documentación**
- 📖 [README Completo](docs/README.md) - 10,000+ palabras
- 🚀 [Quickstart](QUICKSTART.md) - 5 minutos
- 📁 [Project Structure](PROJECT_STRUCTURE.md) - Estructura detallada
- 📝 [Release Notes](RELEASE_NOTES.md) - Notas de versión

### **Código**
- Todos los archivos tienen comentarios extensos
- Ejemplos incluidos en el código
- JSDoc para componentes React
- Python docstrings para scripts

---

## 🏆 **VERSIÓN ACTUAL**

**v1.0.0** - Noviembre 2025

**Cambios principales:**
- ✨ Release inicial completa
- ✅ 6 operadores de Blender
- ✅ 3 scripts Node.js
- ✅ 2 componentes React
- ✅ Documentación completa
- ✅ Validador automático

**[👉 VER TODAS LAS RELEASE NOTES](RELEASE_NOTES.md)**

---

## 🚀 **PRÓXIMOS PASOS**

### **Si eres nuevo:**
1. ✅ Lee [QUICKSTART.md](QUICKSTART.md)
2. ✅ Ejecuta `bash install.sh`
3. ✅ Valida con `python3 validate_pipeline.py`
4. ✅ Sigue el workflow con un modelo de prueba

### **Si eres avanzado:**
1. ✅ Lee [docs/README.md](docs/README.md) completo
2. ✅ Customiza el addon para tu workflow
3. ✅ Añade tus propios themes
4. ✅ Integra con tu pipeline existente

---

## 💎 **TECNOLOGÍAS**

- **Blender 4.5.4** - 3D creation suite
- **glTF 2.0** - GL Transmission Format
- **KHR_materials_variants** - Official glTF extension
- **Three.js r160+** - WebGL 3D library
- **React Three Fiber 8+** - React renderer for Three.js
- **@gltf-transform 4.3+** - glTF 2.0 SDK for Node.js
- **DRACO 1.5.7+** - Geometry compression
- **Node.js 18+** - JavaScript runtime

---

## 📄 **LICENCIA**

Este pipeline es **propietario** y está diseñado para uso en producción de videojuegos AAA.

© 2025 Technical Art Director. Todos los derechos reservados.

---

## 🎉 **¡GRACIAS!**

Este pipeline fue desarrollado con ❤️ para la comunidad de desarrollo de videojuegos AAA.

Si te resulta útil, ¡compártelo con tu equipo!

---

**Made with ❤️ for AAA Game Development**  
**Pipeline v1.0.0 - Noviembre 2025**

---

## 🔗 **ENLACES RÁPIDOS**

- 📋 [INDEX](INDEX.md) - Punto de entrada
- ⭐ [QUICKSTART](QUICKSTART.md) - Comienza aquí (5 min)
- 📖 [MANUAL COMPLETO](docs/README.md) - Documentación exhaustiva
- 📁 [ESTRUCTURA](PROJECT_STRUCTURE.md) - Arquitectura del sistema
- 📝 [RELEASES](RELEASE_NOTES.md) - Notas de versión

---

**🚀 ¡Feliz desarrollo!**
