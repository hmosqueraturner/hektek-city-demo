# 🚀 Estrategia de Release v2.2.0

Estás en el branch `context-configuration`. El flujo correcto para disparar el release **v2.2.0** al hacer merge a `main` es el siguiente:

## 1. Commit & Push (En `context-configuration`)
Este commit guarda todo tu trabajo actual. No dispara el release aún, pero prepara el terreno.

**Comando:**
```bash
git add .
git commit -m "feat(config): SSOT refactor, dynamic UI & camera tuning"
git push origin context-configuration
```

## 2. Pull Request (GitHub)
Abre un PR de `context-configuration` hacia `main`.

**Título del PR:**
`feat(release): Prepare v2.2.0 - SSOT & Dynamic Architecture`

**Descripción del PR:**
Copia y pega esto para que quede registro claro de los cambios:

```markdown
## 🚀 Major Refactor & Features
- **SSOT Architecture:** Centralized all building/theme configs in `buildings-config.json`.
- **Dynamic Rendering:** `MapRPG` now renders buildings dynamically from JSON.
- **Billboard Text:** Implemented 3D text that rotates to face the camera (Y-axis constrained).

## 🐛 Fixes & Improvements
- **Viewer Logic:** Fixed double-click interactions and camera synchronization (`onRest`).
- **Camera Tuning:** Optimized focus/inside positions for Projects, Blog, Docs, and Vision.
- **Workflow:** Fixed GitHub Actions release pipeline for reliable versioning.

## 🔧 Configuration
- Standardized marker positions in JSON.
- Removed hardcoded styles in `BuildingLabel`.
```

## 3. Merge a Main (El Disparador)
**CRÍTICO:** Al hacer el merge en GitHub, asegúrate de usar la opción **"Squash and merge"** o editar el mensaje del merge commit.

El mensaje del **Merge Commit** es lo que el GitHub Action leerá para calcular la versión. Debe contener `feat` (para minor bump) o `BREAKING CHANGE` (para major). Como queremos la v2.2.0 (minor bump desde v2.1.0), usa este mensaje exacto:

**Título del Merge Commit:**
`feat(release): v2.2.0 - SSOT Architecture & Dynamic UI`

**Descripción del Merge Commit (Opcional pero recomendado):**
(Puedes dejar la lista de cambios detallada aquí también).

---

## ¿Por qué así?
1.  **Commit en branch:** Guarda el trabajo.
2.  **PR:** Revisa y documenta.
3.  **Merge a Main:** El evento `push` a `main` (que ocurre al hacer merge) es lo que activa tu workflow `release.yml`.
4.  **GitHub Action:**
    *   Detecta el push en `main`.
    *   Lee el mensaje `feat(release): ...`.
    *   Reconoce `feat` -> Incrementa MINOR (2.1.0 -> 2.2.0).
    *   Genera el tag `v2.2.0` y el Release en GitHub.
