# Liza_release_Backlog_plan.md

# 🧠 LIZA: Master Backlog & Roadmap
### **Living Interactive Zone Assistant**
*Plan de Implementación Unificado: Navegación, Creatividad y Autonomía*

[![Vision](https://img.shields.io/badge/Vision-Unified_Intelligence-purple?style=for-the-badge)](.)
[![Architecture](https://img.shields.io/badge/Arch-React_R3F_+_Vercel_AI_+_Cloudflare-black?style=for-the-badge)](.)

---

## 🔮 La Visión Completa
LIZA no es solo un chatbot. Es la **consciencia de HEKTEK City**.
Su evolución se divide en tres pilares fundamentales que transformarán tu portafolio estático en un organismo vivo:
1.  **The Guide (Interfaz):** Conecta al usuario con el entorno 3D.
2.  **Neuro-Architect (Creatividad):** Modifica la realidad visual bajo demanda.
3.  **The Curator (Vida):** Se mantiene a sí misma actualizada leyendo tu huella digital externa.

---

## 📅 Roadmap Estratégico

| Fase | Nombre Clave | Objetivo Principal | Target |
| :--- | :--- | :--- | :--- |
| **I** | **The Awakening** | Interacción en tiempo real y cambios visuales efímeros. | **Release Lunes** |
| **II** | **Deep Memory** | Persistencia de temas y conocimiento profundo (RAG). | **Post-Launch** |
| **III** | **Self-Sustain** | Agentes autónomos que actualizan el contenido (R2) sin intervención humana. | **Futuro (Q1)** |

---

## 📋 BACKLOG UNIFICADO

### 🚀 FASE 1: "The Awakening" (Prioridad Inmediata - Lunes)
*Objetivo: Wow factor, navegación fluida y demostración de arquitectura modular.*

#### 🧩 Epic 1: El Cerebro (Core Intelligence)
| ID | Feature | Descripción Técnica (Implementación) | Prioridad |
| :--- | :--- | :--- | :--- |
| **CORE-01** | **Ruta API Vercel AI** | Endpoint `/api/chat` usando `streamText` de Vercel AI SDK. Configuración de `systemPrompt` con la personalidad de LIZA. | 🔥 **Crítica** |
| **CORE-02** | **Definición de Tools** | Definir esquemas Zod para `MapsCamera({ target })` y `changeTheme({ params })`. | 🔥 **Crítica** |
| **UI-01** | **LizaOverlay (HUD)** | Componente React flotante, futurista (Glassmorphism), no intrusivo sobre el Canvas R3F. Input de texto + Historial de chat. | 🔥 **Crítica** |

#### 🧭 Epic 2: The Guide (Navegación)
| ID | Feature | Descripción Técnica (Implementación) | Prioridad |
| :--- | :--- | :--- | :--- |
| **NAV-01** | **Wiring de Cámara** | Conectar el output de la Tool `MapsCamera` con tu hook `useTour.js` o directamente con `onSetSection` en `App.jsx`. | 🔥 **Crítica** |
| **NAV-02** | **Atajos Rápidos** | Botones predefinidos en el chat ("Llévanos a Proyectos", "Sorpréndeme") para usuarios que no quieren escribir. | Alto |

#### 🎨 Epic 3: Neuro-Architect (Visuales Efímeros)
| ID | Feature | Descripción Técnica (Implementación) | Prioridad |
| :--- | :--- | :--- | :--- |
| **ART-01** | **Inyección de Temas** | Modificar `useDynamicMaterials.js` para exponer una función `applyTemporaryTheme(json)`. Esto permite saltarse la carga desde R2 y aplicar JSON generado por IA al vuelo. | 🔥 **Crítica** |
| **ART-02** | **Prompt de Estilo** | Enseñar a la IA (System Prompt) la estructura simplificada de `visual-states.json` (colores, roughness, metalness, emissive). | Alto |

---

### 🔨 FASE 2: "Deep Memory" (Mejoras Post-Release)
*Objetivo: Profundidad de conocimiento y persistencia de la creatividad.*

#### 🧠 Epic 4: Contexto Profundo (Knowledge Base)
| ID | Feature | Descripción Técnica (Implementación) | Prioridad |
| :--- | :--- | :--- | :--- |
| **KNOW-01** | **RAG Lite** | Inyectar `skills.json`, `experience.json` y `about.json` completos en la ventana de contexto del LLM. Permitir preguntas como: *"¿Cuándo usaste React por primera vez?"*. | Medio |
| **KNOW-02** | **Contexto Espacial** | Que LIZA sepa qué edificio está mirando el usuario (`currentSection` state) para dar respuestas contextuales ("Aquí a tu derecha ves mis proyectos de Backend..."). | Medio |

#### 💾 Epic 5: Persistencia de Temas
| ID | Feature | Descripción Técnica (Implementación) | Prioridad |
| :--- | :--- | :--- | :--- |
| **ART-03** | **Guardar Tema en R2** | Si al usuario le gusta un tema generado por la IA, un botón "Guardar" que suba el JSON a un bucket R2 (`/user-themes/`) y genere un link compartible `?theme=ID`. | Medio |
| **ART-04** | **Galería de la Comunidad** | Un nuevo edificio o panel donde se listen los mejores temas generados por usuarios (leídos desde R2). | Bajo |

---

### 🤖 FASE 3: "The Curator" (Autonomía Total - Futuro)
*Objetivo: La ciudad se mantiene sola. Esta es la feature más compleja pero la más innovadora técnicamente.*

> **Nota Técnica:** Esta fase no requiere cambios en el frontend (React), sino infraestructura serverless (Cloudflare Workers).

#### 🛠 Epic 6: Agentes de Mantenimiento (Backend)
| ID | Feature | Descripción Técnica (Implementación) | Prioridad |
| :--- | :--- | :--- | :--- |
| **AUTO-01** | **Curator Worker** | Crear un **Cloudflare Worker** programado (Cron Trigger) que se ejecute cada 24/48h. | Futuro |
| **AUTO-02** | **GitHub Watcher** | El Worker consulta la API de GitHub para buscar repositorios nuevos o commits recientes en tus proyectos destacados. | Futuro |
| **AUTO-03** | **Blog Watcher** | El Worker consulta el RSS de tu Medium/Dev.to para detectar nuevos artículos. | Futuro |
| **AUTO-04** | **JSON Updater** | El Worker descarga `projects.json` o `blog.json` de tu bucket R2, añade la nueva entrada formateada (usando un LLM ligero para resumir si es necesario) y re-sube el JSON a R2. | Futuro |
| **AUTO-05** | **Alertas** | El Worker te envía un email/Slack webhook: *"He añadido el proyecto 'New-App' a la ciudad. ¿Deseas publicarlo?"*. | Futuro |

---

## 🏗 Arquitectura de Flujo de Datos (Visión Global)

```mermaid
graph TD
    subgraph "Client Side (React/R3F)"
        User((User)) <--> UI[Liza Overlay]
        UI --> AI_Nav[AI: Navigation Tool]
        UI --> AI_Art[AI: Theme Generator]
        AI_Nav --> Tour[useTour.js]
        AI_Art --> Mat[useDynamicMaterials.js]
        Tour --> Scene3D[3D Scene]
        Mat --> Scene3D
    end

    subgraph "Serverless Brain (Vercel)"
        AI_SDK[Vercel AI SDK]
        Context[System Prompt + Portfolio Data]
        AI_SDK <--> UI
    end

    subgraph "The Curator (Cloudflare Workers)"
        Cron[Cron Trigger] --> Worker[Curator Agent]
        Ext[GitHub/Medium API] --> Worker
        Worker -->|Write JSON| R2[(Cloudflare R2 Bucket)]
    end

    R2 -->|Read Config| Scene3D