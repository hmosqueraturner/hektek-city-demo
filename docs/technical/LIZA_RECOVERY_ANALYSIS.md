# LIZA Recovery Analysis

## 1. Executive Summary
The "Production Degradation" and "Painting Failure" are likely caused by a combination of **Edge Runtime limitations** and **Redundant API Calls** in the server-side code, not by missing API keys (which are correctly handled). The "English Only" behavior is a result of weak prompting combined with English-only context injection.

## 2. Technical Findings

### 2.1 API Instability in Production
*   **File**: `api/liza/chat.js` (Serverless Function)
*   **Issue 1: Double Request Pattern**: When a tool (function call) is detected, the code attempts to make a **second** full round-trip to Gemini to get a "text response" (`const textResult = await textChat.sendMessage(enhancedPrompt)`).
    *   **Impact**: This doubles the latency. In Vercel Edge/Serverless, this increases the chance of timeouts (especially on free tiers or with slower 'Flash' responses). If the second call fails, the entire request fails (500 Error), causing the "Painting not working" issue.
*   **Issue 2: Edge Runtime**: `export const config = { runtime: 'edge' }`.
    *   **Impact**: While `@google/generative-ai` runs in Edge, the strict timeout and resource limits of Edge functions make complex, multi-step AI logic risky.
    *   **Recommendation**: Switch to `nodejs` (Standard Serverless) for better stability and longer timeouts.

### 2.2 Painting Failure (Root Cause)
The "Painting" feature relies on the `apply_visual_theme` function call. Because the API logic described in 2.1 is prone to crashing precisely when a function call *is* detected (triggering the second request), the painting command never reaches the client. The client logic (`useLizaTour.js`) is actually correct and ready to receive the command, but the server errors out before sending it.

### 2.3 Language Bias
*   **File**: `src/utils/liza/liza-smart-context.js` & `liza-prompts.js`
*   **Issue**: The Smart Context provides facts in English. The Prompt says "Respond in user's language" but the abundance of English context often sways the model.
*   **Fix**: Strengthen the System Prompt with a strict "MUST" directive for language matching.

### 2.4 Dead Code
*   **File**: `src/utils/liza/liza-preprocessor.js`
*   **Status**: User confirms this is failed/unwanted logic. It should be removed to prevent confusion.

## 3. Revised Plan
1.  **Safety**: Switch API to `nodejs` runtime.
2.  **Performance**: Remove the "Second Request" logic. Return the function call immediately.
3.  **Language**: Enforce Multilingual rules in System Prompt.
4.  **Cleanup**: Delete unused preprocessor.
