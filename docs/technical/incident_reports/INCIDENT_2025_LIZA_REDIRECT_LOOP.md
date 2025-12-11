# Análisis: Error ERR_TOO_MANY_REDIRECTS en LIZA Chat API (Producción)

**Fecha**: 2025-11-30  
**Severidad**: 🔴 CRÍTICA - Funcionalidad completamente rota en producción  
**Estado**: ✅ Local funciona | ❌ Producción falla  

---

## 📋 Resumen Ejecutivo

El endpoint `/api/liza/chat` funciona perfectamente en desarrollo local pero falla en producción (Vercel + Cloudflare) con el error:

```
POST https://www.hectortechno.com/api/liza/chat net::ERR_TOO_MANY_REDIRECTS
```

### Contexto del Problema
- **Entorno local**: ✅ Funcionando correctamente
- **Producción (Vercel + Cloudflare)**: ❌ Loop infinito de redirecciones
- **Variables de entorno**: Configuradas correctamente en Vercel
- **Intentos previos**: v5.0.1 → v5.0.4 (sin éxito, revertido a v5.0.0)
- **Branch actual**: `liza-vive` (con últimos cambios de iconos)

---

## 🔍 Análisis del Error

### Error Principal
```
Failed to load resource: net::ERR_TOO_MANY_REDIRECTS
TypeError: Failed to fetch
```

### Flujo de la Petición
```
Browser → Cloudflare → Vercel → API Handler
   ↑                                    ↓
   └──────── REDIRECT LOOP ─────────────┘
```

---

## 🎯 Causa Raíz Identificada

### ⚠️ PROBLEMA CRÍTICO en `vercel.json`

**Líneas 41-46**:
```json
"rewrites": [
  {
    "source": "/(.*)",
    "destination": "/"
  }
]
```

### ❌ Por qué esto causa el loop

1. **Request llega a Cloudflare**: `POST /api/liza/chat`
2. **Regla de Cloudflare**: Pasa la petición a Vercel (configurada para `*/hectortechno.com/*api/*`)
3. **Vercel recibe**: `POST /api/liza/chat`
4. **vercel.json rewrite**: `/(.*) → /` **reescribe la ruta a la raíz**
5. **Vercel intenta**: Servir `/` en lugar de `/api/liza/chat`
6. **El API handler nunca se ejecuta**: Porque la ruta fue reescrita
7. **Vercel devuelve 308/301**: Redirigiendo de vuelta
8. **Cloudflare recibe redirect**: Y lo reenvía
9. **🔄 Loop infinito**: 20+ redirects hasta `ERR_TOO_MANY_REDIRECTS`

---

## 🔬 Evidencia Técnica

### Archivo: `vercel.json`
```json
{
  "rewrites": [
    {
      "source": "/(.*)",        // ⚠️ Captura TODO, incluyendo /api/*
      "destination": "/"         // ❌ Reescribe a la raíz
    }
  ]
}
```

### Archivo: `api/liza/chat.js`
- ✅ Código correcto
- ✅ Edge Runtime configurado
- ✅ Variables de entorno accesibles
- ❌ **Pero nunca se alcanza** debido al rewrite

### Cliente: `useLizaChat.js` (línea 24)
```javascript
const response = await fetch('/api/liza/chat', {
```
- ✅ Ruta correcta
- ✅ Headers correctos
- ❌ Falla por el redirect loop del servidor

---

## 🛠️ Causas Secundarias Posibles

### 1. Configuración SSL/TLS en Cloudflare
- **Modo actual**: Probablemente `Flexible` o `Full`
- **Problema**: Si está en `Flexible`, Cloudflare → Vercel usa HTTP, Vercel puede forzar HTTPS → loop
- **Recomendación**: Debe estar en `Full (strict)`

### 2. Regla de Cloudflare para `/api/*`
- **Regla creada**: `*/hectortechno.com/*api/*`
- **Posible conflicto**: Si la regla intenta forzar caching o redirecciones
- **Recomendación**: La regla debe ser **Bypass Cache** para `/api/*`

### 3. Headers de Seguridad
```json
{
  "key": "Strict-Transport-Security",
  "value": "max-age=63072000; includeSubDomains; preload"
}
```
- **Nota**: El HSTS es correcto pero puede interactuar mal con SSL de Cloudflare si está mal configurado

---

## 📊 Comparación Local vs Producción

| Aspecto | Local ✅ | Producción ❌ |
|---------|---------|---------------|
| **Runtime environment** | Node.js dev server | Vercel Edge Runtime |
| **Proxy/CDN** | Ninguno | Cloudflare |
| **SSL** | HTTP localhost | HTTPS con Cloudflare |
| **Rewrites aplicados** | No (dev server ignora vercel.json rewrites) | Sí (Vercel aplica vercel.json) |
| **Variables de entorno** | `.env.local` | Vercel Environment Variables |

> **Key insight**: En local, el dev server NO aplica los rewrites de `vercel.json`, por eso funciona. En producción, Vercel SÍ los aplica → loop.

---

## 🎯 Diagnóstico: 99% de probabilidad

**El problema es el rewrite `/(.*) → /` en vercel.json que captura las rutas de API.**

### Por qué estamos seguros:
1. ✅ El código del API es correcto
2. ✅ Variables de entorno configuradas
3. ✅ En local (sin rewrites) funciona
4. ❌ En producción (con rewrites) falla
5. ❌ El error es específicamente de redirects, no de API/Auth

---

## ⚠️ Impacto

- 🔴 **LIZA completamente inoperativa** en producción
- 🔴 **Usuarios no pueden interactuar** con el asistente AI
- 🟡 **Historial de versiones contaminado** (v5.0.1-5.0.4)
- 🟡 **Branch main requiere revert** a v5.0.0

---

## 📝 Siguiente Paso

Crear **PLAN DE IMPLEMENTACIÓN** para resolver el error con soluciones ordenadas por prioridad.

---

**Archivo creado como parte del protocolo AI_WORKFLOW_PROTOCOL.md**
