# 🧪 Testing Material Variants - Guía Paso a Paso

**Fecha:** 2025-11-17
**Estado:** ✅ Archivos GLB exportados - Listo para testing en React

---

## 📋 Estado Actual

✅ **Completado:**
1. Addon de Blender instalado y funcionando
2. StandardBuildings.blend procesado (roles + variants aplicados)
3. **7 edificios exportados** como `*_clean.glb`:
   - Experience_clean.glb
   - Skills_clean.glb
   - Vision_clean.glb
   - Docs_clean.glb
   - About_clean.glb
   - Projects_clean.glb
   - Blog_clean.glb

⚠️ **Pendiente:**
- Procesar GLBs con `add-khr-variants.js` (hay issue con ES modules)
- Por ahora, vamos a testear directamente los `*_clean.glb` en React

---

## 🚀 Cómo Probar (Paso a Paso)

### **PASO 1: Iniciar el Servidor de Desarrollo**

```bash
cd D:\code\portfolio\hektek-city
npm run dev
```

Deberías ver algo como:
```
VITE v5.x.x ready in XXX ms

➜  Local:   http://localhost:5173/
➜  Network: use --host to expose
```

---

### **PASO 2: Acceder a la Página de Testing**

Abre tu navegador y ve a:
```
http://localhost:5173/test-variants
```

---

### **PASO 3: Interfaz de Testing**

Verás una interfaz con:

#### **Panel de Control (Izquierda Superior):**
- **Select Building:** Dropdown para elegir entre los 7 edificios
- **Current Theme:** Indica el tema actual
- **Botones de Temas:** 4 botones para cambiar themes:
  - 🏙️ **Original** (Verde)
  - 🌃 **Cyberpunk** (Rosa)
  - 🔴 **Mars** (Rojo)
  - 🌿 **Pandora** (Cyan)

#### **Viewport 3D (Centro):**
- El edificio seleccionado renderizado en 3D
- Controles de cámara:
  - Click izquierdo + arrastrar: Rotar
  - Scroll: Zoom
  - Click derecho + arrastrar: Pan

#### **Instrucciones (Derecha Inferior):**
- Guía rápida de controles y testing

---

### **PASO 4: Pruebas a Realizar**

#### ✅ **Test 1: Carga de Edificios**
1. Selecciona "Experience" en el dropdown
2. Espera a que cargue el modelo
3. **Esperado:** El edificio debe aparecer en 3D
4. **Verificar:** Consola debe mostrar: `🏢 Building loaded: {...}`

5. Cambia a "Skills", "Vision", etc.
6. **Esperado:** Cada edificio debe cargar correctamente

#### ✅ **Test 2: Theme Switching (SI aplica variants)**
**NOTA:** Este test solo funcionará si los GLBs tienen KHR_materials_variants.
Como estamos usando `*_clean.glb` sin procesar, es posible que los themes NO cambien todavía.

1. Con un edificio cargado, haz click en **🌃 Cyberpunk**
2. **Esperado (si variants existen):** Los materiales deben cambiar de color/textura
3. **Esperado (si NO variants):** No pasa nada / console warning

4. Prueba los otros themes: Mars, Pandora, Original
5. **Verificar:** Consola debe mostrar: `🎨 Theme changed to: Cyberpunk`

#### ✅ **Test 3: Performance**
1. Abre DevTools (F12)
2. Ve a la pestaña "Performance" o usa Stats (visible en el viewport)
3. Cambia de theme varias veces
4. **Esperado:** FPS debe mantenerse >30 (idealmente 60)

#### ✅ **Test 4: Console Logs**
Revisa la consola de Chrome DevTools para ver:
- ✅ Mensajes de carga: `🏢 Building loaded: {...}`
- ✅ Cambios de theme: `🎨 Theme changed to: ...`
- ⚠️ Warnings si faltan variants: `KHR_materials_variants extension not found`
- ❌ Errores si algo falla

---

## 🔍 Qué Esperar (Resultados)

### **Escenario A: GLBs con Variants (Procesados)**
Si ya procesamos los GLBs con `add-khr-variants.js`:
- ✅ Edificios cargan correctamente
- ✅ Themes cambian de color/textura instantáneamente (<16ms)
- ✅ Console muestra info de variants disponibles

### **Escenario B: GLBs sin Variants (Clean)**
Si estamos usando `*_clean.glb` sin procesar:
- ✅ Edificios cargan correctamente
- ⚠️ Themes NO cambian (porque no hay variants en el GLB todavía)
- ⚠️ Console muestra: `Warning: KHR_materials_variants extension not found`

**Esto es NORMAL.** El componente `AdvancedBuildingModel` está diseñado para funcionar con o sin variants.

---

## 🐛 Troubleshooting

### **Problema 1: Edificio no carga / pantalla negra**
**Posible causa:** GLB no encontrado o ruta incorrecta

**Solución:**
1. Verifica que el archivo existe:
   ```bash
   ls assets/models/buildings/Experience_clean.glb
   ```
2. Asegúrate que Vite está sirviendo los assets correctamente
3. Revisa la consola para ver el error específico

### **Problema 2: "404 Not Found" para el GLB**
**Posible causa:** La ruta del asset no es correcta

**Solución:**
1. Los archivos deben estar en `public/assets/models/buildings/` O en `assets/models/buildings/`
2. Vite sirve archivos desde `public/` automáticamente
3. Si los GLBs están en `assets/`, muévelos a `public/assets/`

### **Problema 3: Themes no cambian**
**Causa:** Los GLBs no tienen la extensión KHR_materials_variants

**Solución:**
Esto es esperado si usamos `*_clean.glb`. Para añadir variants:
```bash
# Necesitamos convertir add-khr-variants.js a ES modules primero
# O usar el script alternativo
```

### **Problema 4: Error "Cannot read property 'switchTheme' of undefined"**
**Causa:** El ref no está inicializado todavía

**Solución:**
- Esto puede pasar al cargar. Es normal.
- El componente maneja esto automáticamente con optional chaining (`?.`)

---

## 📊 Métricas de Éxito

### ✅ **Test Exitoso:**
- [ ] Los 7 edificios cargan sin errores
- [ ] FPS >30 en todo momento
- [ ] No hay errores en console (solo warnings esperados)
- [ ] Los controles de cámara funcionan suavemente
- [ ] (Opcional) Themes cambian si hay variants

### ⚠️ **Test Parcial:**
- [ ] Algunos edificios no cargan
- [ ] FPS <30 pero >15
- [ ] Themes no cambian (esperado sin variants)

### ❌ **Test Fallido:**
- [ ] Ningún edificio carga
- [ ] Errores críticos en console
- [ ] FPS <15 / lag severo
- [ ] App crashea al cambiar edificios

---

## 🎯 Próximos Pasos

### **Si Test Exitoso:**
1. ✅ Confirmar que los edificios se ven correctamente
2. ✅ Tomar screenshots para documentación
3. ✅ Convertir scripts a ES modules
4. ✅ Procesar GLBs con `add-khr-variants.js`
5. ✅ Re-testear con variants activos
6. ✅ Integrar con MapRPG principal si todo funciona

### **Si Test Parcial:**
1. ⚠️ Identificar qué edificios fallan y por qué
2. ⚠️ Revisar logs de console
3. ⚠️ Ajustar configuración según sea necesario

### **Si Test Fallido:**
1. ❌ Revisar estructura de assets
2. ❌ Verificar que Vite sirve los archivos correctamente
3. ❌ Debuggear componente AdvancedBuildingModel

---

## 📝 Reportar Resultados

Por favor, comparte en Discord/Slack/Etc:

```markdown
## 🧪 Test Results - Material Variants

**Fecha:** YYYY-MM-DD
**Tester:** [Tu nombre]

### Status
- [ ] ✅ Exitoso
- [ ] ⚠️ Parcial
- [ ] ❌ Fallido

### Edificios Testeados
- Experience: ✅ / ⚠️ / ❌
- Skills: ✅ / ⚠️ / ❌
- Vision: ✅ / ⚠️ / ❌
... (etc)

### Themes Testeados
- Original: ✅ / ⚠️ / ❌
- Cyberpunk: ✅ / ⚠️ / ❌
- Mars: ✅ / ⚠️ / ❌
- Pandora: ✅ / ⚠️ / ❌

### Performance
- FPS promedio: XX fps
- Tiempo de carga: XX segundos
- Theme switch time: XX ms

### Screenshots
[Adjuntar screenshots]

### Console Logs
[Adjuntar logs relevantes]

### Notas
[Cualquier observación adicional]
```

---

**¡Buena suerte con el testing! 🚀**
