# LIZA Implementation - Critical Questions

**Date**: 2025-12-02  
**Purpose**: Understand existing architecture before fixing LIZA

---

## 🔍 Questions for User

### 1. Theme Application Architecture

**Question**: En `CockpitConsole.jsx` veo que hay un selector de temas (línea ~155-180) que usa `setAssetTheme`. 

- ¿Este `setAssetTheme` es el método correcto que LIZA debería usar para aplicar temas?
- ¿O LIZA debe usar `useAITheme.applyTheme()` que es diferente?
- ¿Cuál es la diferencia entre `setAssetTheme` (del selector) y `applyTheme` (de useAITheme)?

**Por qué pregunto**: Necesito saber si debo usar el MISMO mecanismo que el selector manual o si LIZA tiene su propio sistema.

---

### 2. Navegación en Tours

**Question**: Vi que existe `useTour.js` con `startTour()` que navega por buildings.

- ¿LIZA debe usar este sistema de Tours para navegar?
- ¿O LIZA usa `useLizaTour.executeToolCall` → `onSetSection`?
- ¿Cuál es la diferencia entre Tours y la navegación de LIZA?

**Por qué pregunto**: No quiero duplicar funcionalidad que ya existe - quiero usar la arquitectura correcta.

---

### 3. Gemini API Response Structure

**Question**: Cuando Gemini retorna una respuesta, necesito entender EXACTAMENTE qué puede devolver:

- ¿Gemini puede retornar SOLO function calls SIN texto?
- ¿Gemini puede retornar function calls + texto al mismo tiempo?
- ¿Gemini puede retornar SOLO texto SIN function calls?

**Casos específicos**:
```
User: "navega a Experience"
→ Gemini retorna: function call navigate_to_building + texto explicativo?
→ O solo: function call navigate_to_building?

User: "cuéntame sobre React"
→ Gemini retorna: SOLO texto?
→ O texto + function call?
```

**Por qué pregunto**: Necesito saber cómo manejar la respuesta en el chat UI - si debo mostrar el texto, ocultar function calls, o ambos.

---

### 4. Preprocessor Scope

**Question**: El preprocessor debe detectar keywords de themes y NO llamar API.

**Pero necesito confirmar**:
- ¿El preprocessor SOLO maneja themes?
- ¿O también debe manejar navegación sin API?
  - Ejemplo: "go to Experience" → ¿preprocessor navega directamente SIN API?
  - O: "go to Experience" → ¿preprocessor deja que API maneje?

**Por qué pregunto**: No sé si el preprocessor debe ser ULTRA simple (solo themes) o más complejo (themes + navegación).

---

### 5. Message Flow en Chat UI

**Question**: Cuando LIZA ejecuta una acción (tema o navegación):

- ¿Debe aparecer un mensaje en el chat confirmando la acción?
  - Ejemplo: "✨ Tema Cyberpunk aplicado!" 
  - O: Ejecución silenciosa (solo el efecto visual)?

- Si Gemini retorna function call + texto:
  - ¿Mostrar ambos en el chat?
  - ¿Mostrar solo el texto, ocultar function call?

**Por qué pregunto**: Los logs muestran código/JSON en el chat - necesito saber qué es correcto mostrar.

---

### 6. Componentes a Revisar

**Question**: ¿Qué otros componentes o funcionalidades debería revisar para tener mejor visión de este tema?

**Ya revisé**:
- `CockpitConsole.jsx` (selector de temas)
- `useTour.js` (navegación por tours)
- `useLizaTour.js` (tool execution)
- `useAITheme.js` (aplicación de temas AI)
- `useLizaChat.js` (manejo de mensajes)

**¿Debería revisar**:
- ¿Cómo funciona exactamente `setAssetTheme` vs `applyTheme`?
- ¿Hay algún componente que maneje la respuesta de Gemini que no he visto?
- ¿Hay algún ejemplo funcionando del sábado que pueda estudiar?

---

## 📋 Next Steps (AFTER answers)

1. Crear análisis técnico detallado
2. Crear plan de implementación
3. Esperar aprobación
4. Implementar cambios uno por uno
5. Probar cada cambio antes del siguiente

---

**Esperando respuestas del usuario antes de continuar.**
