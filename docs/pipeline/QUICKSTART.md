# ⚡ QUICKSTART - Material Roles & Variants Pipeline

**Guía de inicio rápido de 5 minutos** para empezar a usar el pipeline inmediatamente.

---

## 🚀 **INSTALACIÓN RÁPIDA**

```bash
# 1. Descomprime el pipeline
cd blender_material_pipeline/

# 2. Instala dependencias Node.js
cd scripts/
npm install
cd ..

# 3. Ejecuta el validador
python3 validate_pipeline.py
```

✅ Si ves "TODAS LAS VALIDACIONES PASARON", ¡estás listo!

---

## 🎨 **WORKFLOW EN 6 PASOS**

### **PASO 1: Instalar Addon en Blender**

```bash
# Opción A: Desde Blender
# Edit → Preferences → Add-ons → Install → selecciona addon/__init__.py

# Opción B: Copia manual
cp addon/__init__.py ~/.config/blender/4.5/scripts/addons/material_roles_variants.py
```

Luego en Blender:
1. Edit → Preferences → Add-ons
2. Busca "Material Roles"
3. ✅ Habilita el addon

---

### **PASO 2: En Blender - Preparar Materiales**

1. Abre tu archivo `.blend`
2. Presiona `N` para abrir el N-Panel
3. Ve a la pestaña **"Material Roles"**

#### **2.1: Import CSV**
- Haz clic en "CSV File" y selecciona tu CSV
- Haz clic en **"1️⃣ Import CSV & Generate JSONs"**
- ✅ Verás un preview con el conteo

#### **2.2: Clean-Up**
- Haz clic en **"2️⃣ Clean-Up Complete"**
- ✅ Purga materiales no usados

#### **2.3: Apply Roles**
- Haz clic en **"3️⃣ Apply Material Roles"**
- ✅ Reemplaza materiales por roles

#### **2.4: Build Palette**
- Haz clic en **"4️⃣ Build MATERIAL_PALETTE"**
- ✅ Crea el objeto palette

#### **2.5: Generate Variants**
- Haz clic en **"5️⃣ Generate Variant Materials"**
- ✅ Genera materiales de variantes

#### **2.6: Export GLB**
- Haz clic en **"6️⃣ Export Clean GLB"**
- Guarda como: `my_buildings.glb`
- ✅ Exporta todo

---

### **PASO 3: Aplicar KHR_materials_variants**

```bash
cd scripts/

node apply_khr_variants.js \
  ../my_buildings.glb \
  ../addon/materials_variants.json \
  ../my_buildings_variants.glb
```

✅ Genera GLB con soporte para theme switching

---

### **PASO 4: Optimizar (Opcional)**

```bash
# Compresión DRACO (recomendado)
node optimize_glb.js \
  ../my_buildings_variants.glb \
  ../my_buildings_final.glb \
  --draco

# O con DRACO + KTX2 (máxima compresión)
node optimize_glb.js \
  ../my_buildings_variants.glb \
  ../my_buildings_final.glb \
  --draco \
  --ktx2
```

✅ Reduce el tamaño ~80%

---

### **PASO 5: Copiar a tu proyecto React**

```bash
# Copia el componente
cp scripts/AdvancedBuildingModel.jsx /tu/proyecto/src/components/

# Copia el GLB final
cp my_buildings_final.glb /tu/proyecto/public/assets/
```

---

### **PASO 6: Integrar en React**

```jsx
// App.jsx
import React, { useRef, useState } from 'react';
import { Canvas } from '@react-three/fiber';
import { OrbitControls } from '@react-three/drei';
import AdvancedBuildingModel from './components/AdvancedBuildingModel';

function App() {
  const [theme, setTheme] = useState('Original');
  const buildingAPI = useRef(null);
  
  const handleLoad = (api) => {
    buildingAPI.current = api;
    console.log('✅ Edificio cargado');
  };
  
  const changeTheme = (themeName) => {
    buildingAPI.current?.variants.applyVariantByName(themeName);
    setTheme(themeName);
  };
  
  return (
    <>
      {/* UI de Themes */}
      <div style={{ position: 'absolute', top: 20, right: 20, zIndex: 1000 }}>
        {['Original', 'Cyberpunk', 'Mars', 'Pandora'].map(t => (
          <button 
            key={t} 
            onClick={() => changeTheme(t)}
            style={{
              display: 'block',
              margin: '5px 0',
              padding: '10px',
              background: theme === t ? '#00ff00' : '#333',
              color: 'white',
              border: 'none',
              cursor: 'pointer'
            }}
          >
            {t}
          </button>
        ))}
      </div>
      
      {/* Canvas 3D */}
      <Canvas camera={{ position: [0, 5, 10] }}>
        <ambientLight intensity={0.5} />
        <directionalLight position={[10, 10, 5]} />
        
        <AdvancedBuildingModel
          url="/assets/my_buildings_final.glb"
          onLoad={handleLoad}
        />
        
        <OrbitControls />
      </Canvas>
    </>
  );
}

export default App;
```

---

## 🎉 **¡LISTO!**

Ahora puedes cambiar themes en tiempo real sin reload:

```jsx
// Cambio por nombre
buildingAPI.current.variants.applyVariantByName('Cyberpunk');

// Cambio por índice
buildingAPI.current.variants.applyVariantByIndex(1);

// Obtener variante actual
console.log(buildingAPI.current.variants.currentVariantName);
```

---

## 🔥 **EJEMPLOS AVANZADOS**

### **Con Keyboard Shortcuts**

```jsx
useEffect(() => {
  const handleKeyPress = (e) => {
    switch(e.key) {
      case '1': buildingAPI.current?.variants.applyVariantByIndex(0); break;
      case '2': buildingAPI.current?.variants.applyVariantByIndex(1); break;
      case '3': buildingAPI.current?.variants.applyVariantByIndex(2); break;
      case '4': buildingAPI.current?.variants.applyVariantByIndex(3); break;
    }
  };
  
  window.addEventListener('keydown', handleKeyPress);
  return () => window.removeEventListener('keydown', handleKeyPress);
}, []);
```

### **Con Múltiples Edificios**

```jsx
const buildings = [
  { id: 'docs', url: '/assets/docs.glb', position: [0, 0, 0] },
  { id: 'blog', url: '/assets/blog.glb', position: [10, 0, 0] },
];

const buildingsAPI = useRef({});

const changeAllThemes = (themeName) => {
  Object.values(buildingsAPI.current).forEach(api => {
    api?.variants.applyVariantByName(themeName);
  });
};

return (
  <>
    {buildings.map(b => (
      <AdvancedBuildingModel
        key={b.id}
        url={b.url}
        position={b.position}
        onLoad={(api) => { buildingsAPI.current[b.id] = api; }}
      />
    ))}
  </>
);
```

---

## 📚 **PRÓXIMOS PASOS**

1. Lee la **documentación completa**: `docs/README.md`
2. Mira los **ejemplos de integración**: `scripts/MapRPG_integration_examples.jsx`
3. Ajusta tus **materiales en Blender** según tus necesidades
4. Itera y experimenta con **themes personalizados**

---

## 🆘 **¿PROBLEMAS?**

### **El addon no aparece en Blender**
```bash
# Verifica que esté instalado
ls ~/.config/blender/4.5/scripts/addons/ | grep material

# Reinicia Blender
```

### **"materials_roles.json no encontrado"**
```bash
# Asegúrate de ejecutar "Import CSV" primero en el N-Panel
```

### **"node: command not found"**
```bash
# Instala Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### **Errores al exportar GLB**
```bash
# Verifica versión de Blender
blender --version  # Debe ser 4.5.x

# Si es otra versión, descarga Blender 4.5.4
```

---

## 💎 **TIPS PRO**

✅ **SIEMPRE** ejecuta `Clean-Up` antes de exportar  
✅ Usa `--draco` en optimize_glb.js para reducir tamaño  
✅ Preload tus GLBs con `AdvancedBuildingModel.preload()`  
✅ Usa `useRef` para el API, NO `useState` (evita re-renders)  
✅ El pipeline es **idempotente**: puedes ejecutar pasos múltiples veces  

---

## 🎮 **¡FELIZ DESARROLLO!**

Si todo funciona, deberías ver:
- ✅ Edificios cargados en 3D
- ✅ Themes cambian al hacer clic
- ✅ Zero lag en theme switching
- ✅ Tamaño de archivo reducido ~80%

**Para más información:** `docs/README.md`

---

**Made with ❤️ by Technical Art Director**  
**Pipeline v1.0.0 - 2025**
