# 🚀 LIZA v5.0 - Setup Instructions para Hector

## ✅ Lo que ya está hecho

He creado automáticamente:

1. **Branch**: `v5.0-liza` (ya creado)
2. **Dependencias**: `@google/generative-ai` (ya instalado)
3. **Archivos creados** (15 archivos nuevos):
   - `src/components/LIZA/LizaChat.jsx` + `.css`
   - `src/hooks/liza/useLizaChat.js`
   - `src/hooks/liza/useLizaTour.js`
   - `src/utils/liza/liza-tools.js`
   - `src/utils/liza/liza-prompts.js`
   - `src/utils/liza/liza-knowledge.js`
   - `api/liza/chat.js`
   - `.env.local.example`
4. **Integraciones**:
   - `MapRPG.jsx` actualizado con LIZA

---

## 🎯 PASOS QUE DEBES HACER (10 minutos)

### Paso 1: Crear `.env.local` con tu API Key

```bash
# En la raíz del proyecto
cp .env.local.example .env.local
```

Luego edita `.env.local` y pon tu API key:

1. Ve a: https://aistudio.google.com/apikey
2. Haz clic en "Create API key"
3. Copia la key (formato: `AIzaSy...`)
4. Pégala en `.env.local`:

```env
VITE_GEMINI_API_KEY=AIzaSy_TU_KEY_AQUI
```

### Paso 2: Verifica que todo está bien

```bash
# Verifica que estás en el branch correcto
git branch

# Debería mostrar: * v5.0-liza

# Inicia el servidor
npm run dev
```

### Paso 3: Prueba LIZA

1. Abre http://localhost:5173
2. Deberías ver un botón flotante "LIZA" abajo a la derecha
3. Haz clic en él
4. Prueba estos mensajes:
   - "Hola LIZA"
   - "Muéstrame tus proyectos"
   - "Llévame a Skills"

---

## 🔍 Troubleshooting

### Si el botón LIZA no aparece:
```bash
# Verifica que no hay errores en consola del navegador (F12)
# Verifica que LizaChat.css se cargó correctamente
```

### Si LIZA no responde:
```bash
# Verifica tu .env.local
cat .env.local

# Verifica que no hay error 401 en la Network tab del navegador
# Si ves 404 en /api/liza/chat, reinicia el servidor
```

### Si la cámara no se mueve:
- LIZA está llamando la función correctamente pero MapRPG no la recibe
- Chequea la consola por errores de `useLizaTour`

---

## 📝 Próximos Pasos (Checkpoint 1.1)

Una vez que veas que LIZA responde, llámame y haremos un checkpoint rápido:

- ✅ UI funcional
- ✅ Chat funcional
- ✅ Navegación funcional

**Luego continuamos con** la integración completa de navegación y la optimización.

---

## 🎬 Demo Script para Checkpoint

Cuando esté listo, prueba estos 3 comandos:

1. **Chat básico**: "¿Qué hace Hector?"
2. **Navegación focus**: "Show me Skills"
3. **Navegación inside**: "Enter the Experience building"

Si los 3 funcionan, tenemos el CORE completo y pasamos a CREATIVE! 🎨

---

**Estado actual**: CORE ~80% completado
**Tiempo estimado hasta checkpoint**: 15 minutos
**Siguiente fase**: Theme Generation (Neuro-Architect)
