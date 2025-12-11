# Entregables de Código - LIZA API Redirect Loop Fix

Según el protocolo `AI_WORKFLOW_PROTOCOL.md`, aquí están los mensajes sugeridos para commit, PR y merge.

---

## 📝 Mensaje de Commit Sugerido

```bash
fix(api): exclude /api/* from SPA rewrites to prevent redirect loop

- Modified vercel.json rewrite pattern from "/(.*)" to "/((?!api).*)" 
  to exclude API routes from SPA routing
- Added CORS headers for /api/* endpoints
- Configured Edge Function with maxDuration and includeFiles
- Adjusted Permissions-Policy to allow camera/microphone for self

Fixes redirect loop causing ERR_TOO_MANY_REDIRECTS in production
Close issue with LIZA Chat API failing with 500+ redirect cycles

Related docs:
- docs/technical/incident_reports/INCIDENT_2025_LIZA_REDIRECT_LOOP.md
- docs/technical/incident_reports/LIZA_REDIRECT_LOOP_FIX_PLAN.md
- docs/technical/incident_reports/CLOUDFLARE_CONFIGURATION_GUIDE.md
```

---

## 📋 Descripción de Pull Request Sugerida

```markdown
## 🔧 Fix: LIZA API Redirect Loop in Production

### Problem Summary
LIZA Chat was completely broken in production with `ERR_TOO_MANY_REDIRECTS` error. The `/api/liza/chat` endpoint was caught in an infinite redirect loop, preventing any AI interaction.

**Error**:
```
POST https://www.hectortechno.com/api/liza/chat net::ERR_TOO_MANY_REDIRECTS
Failed to fetch
```

**Root Cause**: The SPA rewrite rule in `vercel.json` was capturing ALL routes including `/api/*`, causing Vercel to rewrite API requests to `/`, which then redirected back, creating an infinite loop.

---

### Changes Made

#### 🔹 vercel.json
-  **Modified SPA rewrite pattern** to exclude API routes
   - Before: `"source": "/(.*)"`  (captured everything)
   - After: `"source": "/((?!api).*)"`  (excludes /api/*)
   
- ✅ **Added CORS headers** for `/api/*` endpoints to prevent browser blocking

- ✅ **Configured Edge Function** for `/api/liza/chat.js`:
   - `includeFiles`: Ensures `src/**` files are available in Edge Runtime
   - `maxDuration`: Set to 60s to handle Gemini API latency
   
- ✅ **Adjusted Permissions-Policy**: Changed camera/microphone from `()` to `(self)` to support voice input

#### 🔹 Cloudflare Configuration (Manual)  
Following the guide in `docs/technical/incident_reports/CLOUDFLARE_CONFIGURATION_GUIDE.md`:

- ✅ **SSL/TLS Mode**: Set to "Full (strict)"  
- ✅ **Page Rule Created**: `*hectortechno.com/api/*` → Cache Level: Bypass
- ✅ **Cache Purged**: Cleared all cached responses

---

### Testing Plan

#### Local Verification
- [x] Build successful: `npm run build`
- [x] Local dev server working with LIZA Chat
- [ ] Preview deployment tested (Vercel preview URL)

#### Production Verification  
- [ ] Deploy to production
- [ ] Test LIZA Chat: Send message "Hola LIZA"
- [ ] Verify DevTools Network: `/api/liza/chat` returns 200 (not 30x)
- [ ] Verify LIZA responds correctly
- [ ] Monitor for 24h: Check Vercel Analytics for errors

#### Rollback Plan
If issues occur:
- Vercel Dashboard → Deployments → Promote previous v5.0.0
- Or `git revert` this commit

---

### Documentation Created

- 📄 `docs/technical/incident_reports/INCIDENT_2025_LIZA_REDIRECT_LOOP.md` - Technical analysis
- 📄 `docs/technical/incident_reports/LIZA_REDIRECT_LOOP_FIX_PLAN.md` - Implementation plan  
- 📄 `docs/technical/incident_reports/CLOUDFLARE_CONFIGURATION_GUIDE.md` - Cloudflare setup guide

---

### Related Issues

- Closes #XX (if you have a GitHub issue)
- Resolves production incident from 2025-11-30

---

### Breaking Changes

None. This is a pure bugfix.

---

### Checklist

- [x] Code follows project conventions
- [x] Documentation updated
- [x] Local testing passed
- [ ] Preview deployment tested
- [ ] Production smoke test passed (after merge)
- [x] Rollback plan documented

---

**Reviewers**: Please verify the regex pattern `/((?!api).*)` correctly excludes API routes while preserving SPA routing.

**Deployment**: Merge to `main` will auto-deploy to production (Vercel). Monitor closely for first 30 minutes.
```

---

## 🔀 Mensaje de Merge Sugerido

```bash
fix(api): exclude /api/* from SPA rewrites to prevent redirect loop (#XX)

Resolves ERR_TOO_MANY_REDIRECTS in production LIZA Chat API.
Modified vercel.json rewrite pattern to exclude API routes.

Deployed to production on 2025-11-30.
```

---

## 🚀 Comandos Git Sugeridos

```bash
# 1. Verificar estado
git status

# 2. Agregar cambios
git add vercel.json
git add docs/technical/incident_reports/

# 3. Commit con mensaje convencional
git commit -m "fix(api): exclude /api/* from SPA rewrites to prevent redirect loop

- Modified vercel.json rewrite pattern from \"/(.*) \" to \"/((?!api).*)\"
- Added CORS headers for /api/* endpoints
- Configured Edge Function with maxDuration and includeFiles
- Adjusted Permissions-Policy for camera/microphone

Fixes redirect loop causing ERR_TOO_MANY_REDIRECTS in production
Related: docs/technical/incident_reports/INCIDENT_2025_LIZA_REDIRECT_LOOP.md"

# 4. Push al branch actual
git push origin liza-vive

# 5. Crear PR en GitHub (opcional, o merge directo si prefieres)
# Ir a GitHub → Create Pull Request desde liza-vive → main

# 6. O merge directo a main (si no usas PRs)
git checkout main
git merge liza-vive --no-ff
git push origin main
```

---

## ⚡ Deploy Rápido (Sin PR)

Si quieres saltarte el PR y deployar directamente:

```bash
# Estando en branch liza-vive
git add .
git commit -m "fix(api): exclude /api/* from SPA rewrites"
git push origin liza-vive:main  # Push directo a main
```

Vercel auto-deployará en ~2 minutos.

---

## 📊 Post-Deploy Monitoring

**Primeros 30 minutos**:
- Verificar https://www.hectortechno.com → LIZA Chat funciona
- Monitor Vercel Dashboard → Functions → `/api/liza/chat` → Sin errores
- Cloudflare Analytics → Ver requests a `/api/*`

**Primeras 24 horas**:
- Vercel Analytics → Error rate debe ser <1%
- Si hay issues → Rollback inmediato a v5.0.0

---

**Nota**: Estos mensajes siguen las convenciones de Semantic Versioning y Conventional Commits para mantener un historial de git limpio y facilitar la generación automática de changelogs.
