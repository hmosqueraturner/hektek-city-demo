# 🤖 LIZA - Feature Specification (v6.5)

**LIZA** (Living Interactive Zone Assistant) is the conscious core of HekTek City. Unlike traditional chatbots, she is spatially aware and agentic, capable of manipulating the 3D world she inhabits.

![LIZA Awakening Icon](../assets/liza_concept.png) 
*(Placeholder: Conceptual visualization of LIZA's neural interface)*

## 1. The Interaction Model: `LizaCockpit`

Interaction happens through the **LizaCockpit**, a unified Heads-Up Display (HUD) that floats above the 3D world.

### Key Capabilities
*   **Unified Interface**: Merges Tour Controls, Navigation, and AI Chat into a single glassmorphic dock.
*   **Voice-First Design**: Integrated Web Speech API allows users to *speak* to the city.
*   **Contextual Awareness**: The cockpit sends the camera's current target (e.g., "Looking at Skills") to Gemini 2.0, allowing for context-aware responses.

## 2. Neuro-Architect: Generative Reality

LIZA does not just select from a list of themes. She **paints**.

### The "Free-Form" Engine
Using the `Neuro-Architect` module, LIZA translates semantic descriptions into raw material properties.

*   **User**: "Make it feel like a radioactive swamp in the morning."
*   **LIZA (Analysis)**:
    *   *Base*: Green/Brown sludge.
    *   *Highlight*: Neon Green (Radioactive).
    *   *Roughness*: High (Swampy/Wet).
    *   *Emissive*: Low pulse.
*   **Execution**: 200+ meshes are updated in <100ms via `useDynamicMaterials`.

### Technical Implementation

```javascript
// Pseudo-code of the painting logic
const palette = await liza.imagine(prompt);
// { primary: "#2a4b2a", accent: "#39ff14", roughness: 0.2, ... }

await scene.applyTheme({
  mode: 'GENERATIVE',
  palette: palette
});
```

## 3. Speech Recognition Stack

We utilize the browser-native **Web Speech API** for zero-latency, privacy-focused transcription.

*   **Engine**: `webkitSpeechRecognition` / `SpeechRecognition`.
*   **Language**: Auto-detects user locale (EN/ES supported).
*   **Privacy**: Audio is processed by the browser vendor (Google/Apple), not sent to our servers. Only the text transcript is sent to LIZA.

## 4. Contextual RAG (Retrieval Augmented Generation)

LIZA knows everything about Hector.

1.  **Static Knowledge**: Hardcoded "System Prompts" containing resume data.
2.  **Dynamic Context**: "Where is the user right now?" (Camera coordinates).
3.  **Active Tooling**: She knows which projects are currently interactive in the Arcade.

---

> **Note on "CockpitConsole" Legacy**:
> Early versions (v3.0-v5.0) used a sidebar called `CockpitConsole`. This has been superseded by the bottom-dock `LizaCockpit` in v6.0 to prioritize mobile ergonomics and the AI interface.
