# LIZA Recovery Implementation Plan

# Goal
Restore LIZA's production stability, ensure AI-driven painting works reliable, and enforce Spanish/Multilingual support.

## User Review Required
> [!IMPORTANT]
> **Runtime Change**: We are switching the API from `edge` to `nodejs` runtime. This is generally more stable for AI workloads on Vercel.

## Proposed Changes

### 1. Optimize Field API Config
**File**: `api/liza/chat.js`
- **Change**: Switch `runtime` to `nodejs`.
- **Change**: **Remove the "Second Request" logic**. We will rely on Gemini's ability to return `text` **AND** `functionCalls` in a single response.
    - *Note*: Gemini models (especially Flash) are trained to provide a conversational response alongside tool use if the system prompt encourages it.
    - *Fallback*: If no text is returned (rare), the client will show a generic "Processing..." message, but the action (Painting) will still succeed.

```javascript
// Remove expensive double-call logic
// Return the single response immediately:
const textResponse = response.text(); // Gemini usually provides this with the tool call
return new Response(JSON.stringify({ 
    functionCalls, 
    textResponse 
}), ...);
```

### 2. Strengthen Language Rules (Minimal Impact)
**File**: `src/utils/liza/liza-prompts.js`
- **Change**: Add a **single, high-priority line** to the top of the system prompt. We will NOT add bulk.

```javascript
// Add only this line at the top:
"ALWAYS respond in the user's language (English or Spanish)."
```

### 3. Cleanup
**File**: `src/utils/liza/liza-preprocessor.js`
- **Change**: DELETE file. (User confirmed this is dead code).

## Verification Plan

### Manual Verification
1.  **Production Stability**:
    *   Deploy changes.
    *   Test standard chat ("Hello", "Hola"). Ensure no 500 errors.
2.  **Painting Check**:
    *   Command: "Change theme to Cyberpunk".
    *   **Success Criteria**: The API returns the `apply_visual_theme` function call immediately (without timeout). The client executes it. The scene changes.
3.  **Language Check**:
    *   Command: "¿Puedes explicar tu experiencia?"
    *   **Success Criteria**: LIZA responds in Spanish.
