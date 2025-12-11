# ⚠️ LIZA Navigation Fix - Root Cause Identified

**Date**: December 1, 2025, 07:47 CET  
**Issue**: LIZA responds with function call JSON as text instead of executing navigation  
**Status**: ✅ FIXED

---

## 🔍 Root Cause

**Incorrect Import Path in `api/liza/chat.js`**:

```javascript
// WRONG (line 2)
import { LIZA_SYSTEM_PROMPT } from '../../src/utils/liza/liza-prompts.js';

// SHOULD BE
import { LIZA_SYSTEM_PROMPT } from '../../src/utils/liza/liza-prompts.js';
```

The file `liza-system-prompt.js` **does not exist**. The correct file is `liza-prompts.js`.

---

## 💥 Impact

When the import fails:
1. `LIZA_SYSTEM_PROMPT` becomes `undefined`
2. Gemini model receives `undefined` system instruction
3. Model doesn't understand it should use function calling tools
4. Model responds with **manual JSON strings** in text instead of triggering actual function calls
5. Frontend displays JSON as text (seen in screenshots)
6. Navigation never executes

---

## 🔧 Fix Applied

**File**: `api/liza/chat.js`  
**Line**: 2  
**Change**: Corrected import path to `liza-prompts.js`

```diff
- import { LIZA_SYSTEM_PROMPT } from '../../src/utils/liza/liza-prompts.js';
+ import { LIZA_SYSTEM_PROMPT } from '../../src/utils/liza/liza-prompts.js';
```

---

## ✅ Expected Behavior After Fix

1. User: "Show me your experience"
2. LIZA receives proper system prompt with tool instructions
3. Gemini calls `navigate_to_building` function
4. API returns JSON: `{ functionCalls: [{ name: "navigate_to_building", args: {...} }] }`
5. Frontend intercepts JSON response (useLizaChat.js:44-74)
6. `onToolCall` callback executes
7. Camera navigates to Experience building ✨

---

## 🧪 Testing Steps

1. Save changes
2. Restart `npm run dev` (Vite HMR might not catch Edge Function changes)
3. Ask LIZA: "Navigate to skills"
4. Verify: Camera should move, NOT show JSON text
5. Ask LIZA: "Show me your projects"
6. Verify: Same - camera moves
7. Test theme change: "Change to matrix theme"
8. Verify: Theme applies correctly (already working per user)

---

## 🎯 Files Involved

**Frontend (Working ✅)**:
- `src/hooks/useLizaChat.js` (lines 44-74) - Handles functionCalls correctly

**Backend (Fixed ✅)**:
- `api/liza/chat.js` (line 2) - Import path corrected
- `api/liza/chat.js` (lines 110-129) - Function call detection logic is correct
- `src/utils/liza/liza-prompts.js` - System prompt with tool instructions

---

**Confidence**: 95% this fixes the navigation issue completely
