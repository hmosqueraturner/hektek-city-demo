# Plan de Implementación: Fix LIZA API Redirect Loop

**Objetivo**: Resolver el error `ERR_TOO_MANY_REDIRECTS` en `/api/liza/chat` en producción.

**Referencia**: [INCIDENT_2025_LIZA_REDIRECT_LOOP.md](./INCIDENT_2025_LIZA_REDIRECT_LOOP.md)

---

## User Review Required

> [!WARNING]
> **Decisión crítica sobre rewrites en vercel.json**
> 
> Actualmente tienes un rewrite global `/(.*) → /` que probablemente está ahí para manejar SPA routing (React Router).
> 
> **Pregunta**: ¿Necesitas este rewrite para el routing de React/tu SPA? Si es así, debemos excluir `/api/*` del rewrite. Si no lo necesitas, podemos eliminarlo completamente.

> [!IMPORTANT]
> **Configuración de Cloudflare requerida**
> 
> Necesitarás acceso a tu dashboard de Cloudflare para verificar/ajustar:
> - Modo SSL/TLS (debe ser "Full (strict)")
> - Page Rules para `/api/*` (debe ser "Bypass Cache")
> - Origin Rules si existen

---

## Proposed Changes

### Solución 1: Corregir Rewrites en vercel.json (Prioridad ALTA)

#### [MODIFY] [vercel.json](file:///d:/code/portfolio/hektek-city/vercel.json)

**Cambio**: Excluir rutas de API del rewrite global

**Opción A - Si necesitas el rewrite para SPA**:
```json
{
  "rewrites": [
    {
      "source": "/((?!api).*)",
      "destination": "/"
    }
  ]
}
```

**Opción B - Si NO necesitas rewrites** (más simple):
```json
{
  "rewrites": []
}
```

**Justificación**:
- El rewrite actual `/(.*) → /` captura TODO, incluyendo `/api/*`
- Esto hace que Vercel nunca ejecute el API handler
- Excluir `/api/` permite que los endpoints funcionen correctamente

---

### Solución 2: Verificar Configuración de Cloudflare SSL/TLS (Prioridad ALTA)

#### Cloudflare Dashboard → SSL/TLS

**Pasos manuales**:

1. **Ir a**: Cloudflare Dashboard → hectortechno.com → SSL/TLS
2. **Verificar modo**: Debe ser **"Full (strict)"**
   - ❌ Si está en "Flexible": Cloudflare → Vercel usa HTTP, causa loops
   - ❌ Si está en "Full": Puede no validar certificado, puede causar issues
   - ✅ "Full (strict)": Cloudflare → Vercel usa HTTPS con validación
3. **Si no es "Full (strict)"**: Cambiarlo inmediatamente

**Por qué es importante**:
- Vercel siempre responde con HTTPS
- Si Cloudflare→Vercel usa HTTP, Vercel redirige a HTTPS → loop
- "Full (strict)" asegura HTTPS end-to-end

---

### Solución 3: Ajustar Page Rules de Cloudflare para `/api/*` (Prioridad MEDIA)

#### Cloudflare Dashboard → Rules

**Regla actual mencionada**:
```
*/hectortechno.com/*api/*
```

**Configuración correcta requerida**:

1. **Ir a**: Cloudflare Dashboard → hectortechno.com → Rules → Page Rules
2. **Verificar la regla para** `*hectortechno.com/api/*`
3. **Configuración debe ser**:
   - ✅ **Cache Level**: Bypass
   - ✅ **Browser Cache TTL**: Bypass
   - ❌ **NO tener**: Forwarding URL (redirect)
   - ❌ **NO tener**: Always Use HTTPS (redundante con SSL/TLS global)

**Si la regla tiene redirects o Always Use HTTPS**:
- Eliminar esas configuraciones
- Solo dejar "Bypass Cache"

**Alternativa - Configuration Rules (nuevo sistema Cloudflare)**:

Si usas el nuevo sistema de reglas:

1. **Ir a**: Rules → Configuration Rules
2. **Crear regla**: "API Passthrough"
3. **When incoming requests match**: `(http.request.uri.path matches "^/api/.*")`
4. **Then the settings are**:
   - Cache eligibility: Bypass cache
   - SSL: Full (strict)

---

### Solución 4: Añadir Headers CORS Explícitos (Prioridad BAJA)

#### [MODIFY] [api/liza/chat.js](file:///d:/code/portfolio/hektek-city/api/liza/chat.js)

**Cambio**: Añadir headers CORS para evitar conflictos con Cloudflare

```javascript
// Línea 20-22, reemplazar:
export default async function handler(req) {
  if (req.method !== 'POST') {
    return new Response('Method not allowed', { status: 405 });
  }

// Por:
export default async function handler(req) {
  // CORS headers
  const headers = {
    'Access-Control-Allow-Origin': '*', // O tu dominio específico
    'Access-Control-Allow-Methods': 'POST, OPTIONS',
    'Access-Control-Allow-Headers': 'Content-Type',
  };

  // Handle preflight
  if (req.method === 'OPTIONS') {
    return new Response(null, { status: 204, headers });
  }

  if (req.method !== 'POST') {
    return new Response('Method not allowed', { status: 405, headers });
  }
```

**Y actualizar todos los Response** para incluir estos headers:

```javascript
// Línea 90-96, añadir headers al spread:
return new Response(stream, {
  headers: {
    ...headers,
    'Content-Type': 'text/event-stream',
    'Cache-Control': 'no-cache',
    'Connection': 'keep-alive',
  },
});

// Línea 100-106, añadir headers:
return new Response(
  JSON.stringify({ error: 'Failed to process message', details: error.message }), 
  { 
    status: 500,
    headers: { ...headers, 'Content-Type': 'application/json' }
  }
);
```

**Justificación**:
- Cloudflare puede estar bloqueando por CORS
- Aunque el error es de redirects, esto previene issues secundarios

---

## Verification Plan

### Paso 1: Verificación Local

Antes de desplegar a producción:

```bash
# 1. Aplicar cambios a vercel.json
# 2. Build local para verificar
npm run build

# 3. Preview build localmente
npm run preview

# 4. Probar LIZA en preview
# Abrir http://localhost:4173
# Intentar usar LIZA Chat
```

### Paso 2: Deploy a Vercel (Preview)

```bash
# Deploy a preview (NO a producción aún)
vercel deploy

# Vercel dará una URL de preview: https://hektek-city-xxxxx.vercel.app
```

**Probar en preview**:
1. Abrir la URL de preview
2. Abrir DevTools → Network
3. Intentar usar LIZA Chat
4. Verificar que `/api/liza/chat` responde 200 (no 30x)

### Paso 3: Verificación de Cloudflare

**En Cloudflare Dashboard**:

1. **SSL/TLS**: Verificar "Full (strict)"
2. **Page Rules**: Verificar bypass cache para `/api/*`
3. **Analytics**: Ver si hay redirects en los últimos días

### Paso 4: Deploy a Producción

Solo si preview funciona:

```bash
# Deploy a producción
vercel --prod

# O hacer push a main si auto-deploy está configurado
git push origin liza-vive:main
```

### Paso 5: Smoke Test en Producción

1. **Abrir**: https://www.hectortechno.com
2. **DevTools**: Network tab
3. **Usar LIZA**: Enviar mensaje "Hola LIZA"
4. **Verificar**:
   - ✅ `POST /api/liza/chat` → Status 200
   - ✅ Response correcto de Gemini
   - ✅ No hay redirects (30x)
   - ✅ LIZA responde correctamente

### Paso 6: Monitoreo Post-Deploy

- **24h monitoring**: Verificar Vercel Analytics para errores
- **Cloudflare Analytics**: Verificar tráfico a `/api/liza/chat`
- **Sentry/Logging**: Si tienes, monitorear errores

---

## Rollback Plan

Si algo falla:

### Opción A: Revert Git
```bash
# Si hiciste push a main
git revert <commit-hash>
git push origin main
```

### Opción B: Revert en Vercel Dashboard
1. Ir a Vercel Dashboard → Deployments
2. Encontrar deployment previo funcional (v5.0.0)
3. Click "..." → "Promote to Production"

### Opción C: Revert vercel.json Solo
```bash
git checkout HEAD~1 -- vercel.json
git commit -m "revert: vercel.json to previous state"
git push origin main
```

---

## Orden de Ejecución Recomendado

1. ✅ **Primero**: Verificar/ajustar Cloudflare SSL/TLS a "Full (strict)"
2. ✅ **Segundo**: Ajustar Page Rules en Cloudflare (bypass cache para `/api/*`)
3. ✅ **Tercero**: Modificar `vercel.json` (excluir `/api/` del rewrite)
4. ⏸️ **Esperar aprobación del usuario**
5. ✅ **Cuarto**: Deploy a Vercel preview
6. ✅ **Quinto**: Si preview OK → Deploy a producción
7. 🔍 **Sexto**: Monitorear 24h

---

## Notas Adicionales

### Variables de Entorno en Vercel

Verificar que estén configuradas:
- ✅ `VITE_GEMINI_API_KEY`: Clave de API de Google Gemini

Si `VITE_GEMINI_API_KEY` no está disponible en Edge Runtime:

**Renombrar** a `GEMINI_API_KEY` (sin prefix `VITE_`):
- En Vercel Dashboard → Settings → Environment Variables
- Renombrar `VITE_GEMINI_API_KEY` → `GEMINI_API_KEY`
- En `api/liza/chat.js` línea 7, cambiar a `process.env.GEMINI_API_KEY`

> **Razón**: En Edge Runtime, las variables con prefix `VITE_` pueden no estar disponibles correctamente.

---

## Contacto y Referencias

- **Documentación Vercel Rewrites**: https://vercel.com/docs/projects/project-configuration#rewrites
- **Cloudflare SSL Modes**: https://developers.cloudflare.com/ssl/origin-configuration/ssl-modes
- **Vercel Edge Runtime**: https://vercel.com/docs/functions/edge-functions/edge-runtime

---

**Estado**: ⏸️ **ESPERANDO APROBACIÓN DEL USUARIO**

Por favor, confirma:
1. ¿Necesitas el rewrite `/(.*) → /` para tu SPA routing?
2. ¿Tienes acceso al dashboard de Cloudflare?
3. ¿Quieres que proceda con la implementación?
