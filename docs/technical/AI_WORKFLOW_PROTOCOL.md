# AI Interaction Protocol & Development Standards

Este documento define las reglas ESTRICTAS e INVIOLABLES para cualquier Asistente de IA (Gemini, Claude, GPT, etc.) que interactúe con este repositorio.

## 1. Integridad del Repositorio (GIT)
* **PROHIBIDO:** Nunca realizar `git commit` ni `git push` automáticamente.
* **PROHIBIDO:** Nunca cambiar de rama (`checkout`), crear ramas o tags, ni modificar el historial de git (rebase, reset) por iniciativa propia.
* **SOLO LECTURA:** Tu interacción con Git es de solo lectura y análisis.
* **ACCIÓN HUMANA:** Solo el desarrollador humano ejecuta comandos que alteran el estado del repositorio o el control de versiones.

## 2. Flujo de Trabajo (Workflow)
El ciclo de desarrollo debe seguir estrictamente este orden:

1.  **Análisis:** Comprender el problema.
2.  **Documentación (Output):** Generar un archivo Markdown en `/docs/features/` o `/docs/technical/` con el análisis, roadmap o plan.
3.  **Plan de Implementación:** Generar un archivo Markdown en `/docs/...` detallando paso a paso qué archivos se tocarán y cómo.
4.  **Aprobación:** **DETENERSE.** Esperar aprobación explícita del humano en el chat: "Proceder con la implementación".
5.  **Implementación:** Solo tras la aprobación, proveer el código.

## 3. Entregables de Código
* No aplicar cambios ciegamente.
* Al finalizar una tarea, proveer los siguientes mensajes de texto para que el humano los utilice:
    * Mensaje sugerido para `commit`.
    * Descripción sugerida para el `Pull Request` (PR).
    * Mensaje sugerido para el `merge` (basado en `.github/workflows/release.yml` y convenciones de changelog).

## 4. Comunicación y Contexto
* **Preguntar Primero:** Antes de asumir, haz las preguntas necesarias para completar la tarea con precisión (Regla #8).
* **Documentación Persistente:** No dejar ideas volátiles en el chat. Todo análisis o decisión técnica debe quedar plasmada en un archivo `.md` dentro de `/docs`.

---
*Este protocolo es mandatorio para mantener la seguridad del entorno de producción.*
