# 🚀 HekTek City v2.0 - Commit & Release Strategy

## ✅ PASO 1: Commit Inicial (Reorganización)

### Mensaje de Commit
Usa el mensaje completo en `/tmp/commit_message.txt`

### Comandos para Commit
```bash
# 1. Verificar estado
git status

# 2. Agregar todos los cambios
git add .

# 3. Commit con mensaje detallado
git commit -F /tmp/commit_message.txt

# 4. Push a rama actual (30-subdomains)
git push origin 30-subdomains
```

**Resultado Esperado:**
- ✅ Commit con mensaje profesional detallado
- ✅ Visible en GitHub con toda la documentación
- ✅ Listo para PR hacia main

---

## 🎯 PASO 2: Merge a Main (Trigger v2.0.0)

### Opción A: Pull Request (Recomendado)
```bash
# 1. Crear PR en GitHub
gh pr create --title "feat!: Material Variants Pipeline & Professional Architecture (v2.0.0)" \
  --body "$(cat RELEASE_2.0_TEMPLATE.md)" \
  --base main \
  --head 30-subdomains

# 2. Review y Merge en GitHub UI
# 3. GitHub Actions automáticamente:
#    - Detecta "feat!" o "BREAKING CHANGE:" → MAJOR bump (v2.0.0)
#    - Genera changelog automático
#    - Crea GitHub Release
#    - Publica release notes
```

### Opción B: Merge Directo
```bash
# 1. Cambiar a main
git checkout main

# 2. Merge desde 30-subdomains
git merge 30-subdomains

# 3. Push a main (trigger GitHub Actions)
git push origin main
```

**Resultado Esperado:**
- ✅ GitHub Actions detecta "feat!" o "BREAKING CHANGE"
- ✅ Crea tag v2.0.0 automáticamente
- ✅ Genera Release con changelog
- ✅ Vercel auto-deploy a producción

---

## 📋 Mejoras en GitHub Actions

### 1. Changelog Config (✅ COMPLETADO)
Archivo: `.github/changelog-config.json`

**Mejoras aplicadas:**
- ✅ 10 categorías de commits (Features, Fixes, Improvements, Docs, etc.)
- ✅ Detección automática de tipo de commit (feat, fix, refactor, docs, etc.)
- ✅ Template mejorado con enlaces a Full Changelog
- ✅ Transformadores para limpiar mensajes
- ✅ Soporte para PRs y commits directos

### 2. Release Workflow (✅ COMPLETADO)
Archivo: `.github/workflows/release.yml`

**Mejoras aplicadas:**
- ✅ Mejor detección de versión semántica:
  - `BREAKING CHANGE:` o `feat!:` → MAJOR (v2.0.0)
  - `feat:` → MINOR (v1.1.0)
  - `fix:` → PATCH (v1.0.1)
- ✅ Fetch completo de historial para changelog
- ✅ Auto-generación de release notes por GitHub
- ✅ Configuración de tags inicial: v1.0.0

---

## 🎨 Conventional Commits Guide

Para que el versionado automático funcione, usa estos prefijos:

### Major Version (v2.0.0)
```bash
git commit -m "feat!: Add Material Variants Pipeline

BREAKING CHANGE: Script paths changed from /scripts to /tools"
```

### Minor Version (v1.1.0)
```bash
git commit -m "feat: Add new theme system with 4 variants"
```

### Patch Version (v1.0.1)
```bash
git commit -m "fix: Correct model loading path in MapVariations"
```

### Otros Tipos
```bash
# Refactoring (no cambia version por sí solo)
git commit -m "refactor: Reorganize project structure"

# Documentación
git commit -m "docs: Add complete Material Variants guide"

# Performance
git commit -m "perf: Optimize DRACO compression settings"

# Build/CI
git commit -m "ci: Update GitHub Actions workflow"

# Chores
git commit -m "chore: Update dependencies"
```

---

## 🔍 Verificación Pre-Commit

### Checklist
- [ ] ✅ Todos los archivos en `/tools` están copiados correctamente
- [ ] ✅ Todos los `.md` movidos a `/docs` (excepto README.md en root)
- [ ] ✅ 12 nuevos READMEs creados
- [ ] ✅ `package.json` actualizado con nuevas rutas
- [ ] ✅ `process-all-buildings.js` con rutas correctas
- [ ] ✅ `.github/changelog-config.json` mejorado
- [ ] ✅ `.github/workflows/release.yml` actualizado

### Comandos de Verificación
```bash
# Verificar estructura /tools
ls -la tools/

# Verificar solo README.md en root
ls *.md

# Verificar docs organizados
ls -la docs/

# Verificar package.json
cat package.json | grep "pipeline:"

# Verificar git status
git status
```

---

## 🚀 Estrategia de Release v2.0.0

### Timeline Sugerido

**AHORA - Commit Reorganización:**
```bash
git commit -F /tmp/commit_message.txt
git push origin 30-subdomains
```

**DESPUÉS - Completar Material Variants:**
1. Convertir scripts a ES modules
2. Procesar GLBs con variants
3. Integrar con MapRPG principal
4. Testing completo

**FINALMENTE - Release v2.0.0:**
```bash
# Crear PR con todo completo
gh pr create --title "feat!: Release v2.0.0 - Material Variants Pipeline" \
  --body "$(cat RELEASE_2.0_TEMPLATE.md)"

# Merge → GitHub Actions → v2.0.0 Release
```

---

## 📊 Qué Esperar del Workflow

### Cuando hagas Push a Main:

1. **GitHub Actions se activa** (`release.yml`)
2. **Analiza commits** desde último tag
3. **Detecta tipo de bump:**
   - `feat!:` o `BREAKING CHANGE:` → v2.0.0 (MAJOR)
   - `feat:` → v1.1.0 (MINOR)
   - `fix:` → v1.0.1 (PATCH)
4. **Crea tag:** `v2.0.0`
5. **Genera changelog** usando `.github/changelog-config.json`
6. **Crea GitHub Release** con:
   - Tag: v2.0.0
   - Title: Release v2.0.0
   - Body: Changelog auto-generado
   - Assets: Ninguno (solo código)
7. **Vercel detecta** push a main
8. **Auto-deploy** a producción

### Verificación Post-Release
```bash
# Ver último release
gh release view

# Ver tags
git tag -l

# Ver changelog
gh release view --json body
```

---

## 🐛 Troubleshooting

### "GitHub Actions no crea release"
**Causa:** Commit sin prefijo convencional
**Fix:** Usa `feat:`, `fix:`, o `feat!:`

### "Versión incorrecta (v1.0.x en vez de v2.0.0)"
**Causa:** Falta `BREAKING CHANGE:` o `!` en commit
**Fix:** 
```bash
# Amend commit
git commit --amend -m "feat!: Tu mensaje

BREAKING CHANGE: Descripción del cambio breaking"
git push --force
```

### "Changelog vacío"
**Causa:** No hay commits desde último tag
**Fix:** Asegúrate de tener commits nuevos con mensajes convencionales

---

## 📝 Resumen Ejecutivo

### Para ESTE Commit (Reorganización)
```bash
# Commit detallado con toda la info
git add .
git commit -F /tmp/commit_message.txt
git push origin 30-subdomains
```

### Para Release v2.0.0 (Posterior)
```bash
# Después de completar Material Variants
git commit -m "feat!: Complete Material Variants Pipeline integration

BREAKING CHANGE: Material variants now use *_final.glb files"

# Merge a main → Auto-release v2.0.0
git push origin main
```

---

**Made with ❤️ for HekTek City v2.0**
