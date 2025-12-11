# LIZA Fix - Implementation Plan

**Date**: 2025-12-02
**Status**: Awaiting User Approval  
**Approach**: Incremental fixes with testing after each step

---

## 🎯 Objective

Fix 3 critical issues:
1. ✅ Theme application (preprocessor → useAITheme)
2. ✅ Navigation execution (Gemini → camera movement)
3. ✅ Message display (show text, hide function calls)

---

## 📋 Implementation Strategy

**Approach**: Fix ONE component at a time, test, then move to next

**Order**:
1. Fix `useAITheme.js` (async import issue)
2. Fix `useLizaChat.js` + API response parsing
3. Verify navigation args
4. Test complete flow

---

## 🔧 Step 1: Fix useAITheme Async Import

### Problem
```javascript
// CURRENT (BROKEN):
import('../../config/theme-config.json').then(...) // Async!
return { success: true }; // Returns BEFORE theme applies
```

### Solution
```javascript
// NEW (FIXED):
import themeConfig from '../../config/theme-config.json' assert { type: 'json' };
// Synchronous import at module level
```

### File: `src/hooks/liza/useAITheme.js`

**Complete New Code**:
```javascript
import { useCallback } from 'react';
import { useAIThemeContext } from '../../contexts/AIThemeContext';
import themeConfig from '../../config/theme-config.json' assert { type: 'json' };

export function useAITheme() {
  const { applyAITheme: applyThemeToContext } = useAIThemeContext();

  const applyTheme = useCallback((themeData) => {
    console.log('[useAITheme] ===== APPLY THEME CALLED =====');
    console.log('[useAITheme] Received:', JSON.stringify(themeData, null, 2));
    
    // Case 1: Pre-established theme from preprocessor
    // Input: { themeName: "chernobyl" }
    if (themeData.themeName && typeof themeData.themeName === 'string') {
      console.log(`[useAITheme] 🎨 PRE-ESTABLISHED THEME: ${themeData.themeName}`);
      
      const theme = themeConfig.themes[themeData.themeName];
      
      if (!theme) {
        console.error(`[useAITheme] ❌ Theme "${themeData.themeName}" not found`);
        return {
          success: false,
          message: `❌ Tema "${themeData.themeName}" no encontrado`
        };
      }
      
      console.log(`[useAITheme] ✅ Found: ${theme.displayName}`);
      
      // Build full theme object for context
      const fullThemeData = {
        themeName: themeData.themeName,
        styleName: theme.displayName,
        primaryColor: theme.primaryColor,
        accentColor: theme.primaryColor,
        emissiveColor: theme.primaryColor,
        emissiveIntensity: 0.8,
        metalness: 0.6,
        roughness: 0.4
      };
      
      console.log('[useAITheme] Applying to context...');
      applyThemeToContext(fullThemeData);
      console.log('[useAITheme] ✅ Theme applied!');
      
      return {
        success: true,
        message: `✨ Tema ${theme.displayName} aplicado!`
      };
    }
    
    // Case 2: Custom AI-generated theme from Gemini
    // Input: { styleName, primaryColor, accentColor, ... }
    console.log('[useAITheme] 🤖 CUSTOM AI THEME');
    
    // Validate
    if (!themeData || !themeData.primaryColor || !themeData.accentColor) {
      console.error('[useAITheme] ❌ Invalid custom theme');
      return {
        success: false,
        message: '❌ Tema inválid'
      };
    }

    console.log('[useAITheme] Applying custom theme...');
    applyThemeToContext(themeData);
    console.log('[useAITheme] ✅ Custom theme applied!');
    
    return {
      success: true,
      message: `✨ Tema "${themeData.styleName}" aplicado!`
    };
  }, [applyThemeToContext]);

  return { applyTheme };
}
```

###Verification Test 1
```
Input: "change to chernobyl"
Expected console logs:
  [Pre-Processing] Keyword detected: "chernobyl" → theme: "chernobyl"
  [Pre-Processing] SKIPPING API
  [useAITheme] ===== APPLY THEME CALLED =====
  [useAITheme] PRE-ESTABLISHED THEME: chernobyl
  [useAITheme] ✅ Found: Chernobyl Zone
  [useAITheme] ✅ Theme applied!
Expected result:
  - City turns greenish (Chernobyl colors)
  - Chat shows: "✨ Tema Chernobyl Zone aplicado!"
```

---

## 🔧 Step 2: Fix API Response Parsing

### Problem
Need to see actual API response structure to know how to extract text

### Investigation Required
Review `api/liza/chat.js` to see what's returned:
- Is it `{ functionCalls: [...], textResponse: "..." }`?
- Or `{ functionCalls: [...], text: "..." }`?
- Or streaming response?

### File: `api/liza/chat.js`

**Need to examine Lines 109-125** to see exact response format

### Possible Fix (depending on response structure):

**Option A: If API returns JSON with textResponse**:
```javascript
// api/liza/chat.js
if (functionCalls && functionCalls.length > 0) {
  const responseData = {
    functionCalls: functionCalls.map(fc => ({
      name: fc.name,
      args: fc.args
    }))
  };
  
  // Also include text if Gemini provided it
  const textResponse = response.text();
  if (textResponse) {
    responseData.textResponse = textResponse;
  }
  
  return new Response(JSON.stringify(responseData), {
    headers: { 'Content-Type': 'application/json' }
  });
}
```

**Option B: If streaming response**:
Need to check current implementation

### Verification Test 2
```
Input: "navega a Experience"
Expected console logs:
  [LIZA API] Function calls detected: 1
  [LIZA API] Function 1: navigate_to_building
  [LIZA API] Also found text response: "¡Vamos a Experience!..."
Expected frontend:
  [useLizaChat] Function call response: { functionCalls: [...], textResponse: "..." }
  [useLizaTour] Executing navigate_to_building
  Chat shows: "¡Vamos a Experience!..." (ONLY text, NO code)
```

---

## 🔧 Step 3: Fix Message Display Logic

### Problem
Chat might show code/JSON instead of text

### File: `src/hooks/liza/useLizaChat.js`

**Current Code (lines 78-103)**:
```javascript
if (data.functionCalls) {
  // Execute each function call
  for (const fc of data.functionCalls) {
    console.log('[useLizaChat] Executing function:', fc.name);
    onToolCall(fc);
    // DON'T add system messages
  }

  // If there's also a text response, add it
  if (data.textResponse) {
    console.log('[useLizaChat] ✅ Also got text response:', data.textResponse);
    setMessages(prev => [...prev, {
      role: 'assistant',
      content: data.textResponse
    }]);
  }
  
  setIsLoading(false);
  return;
}
```

**Potential Issue**: `data.textResponse` might not exist or be empty

**Fix**: Add fallback and logging
```javascript
if (data.functionCalls) {
  console.log('[useLizaChat] ========== FUNCTION CALLS DETECTED ==========');
  console.log('[useLizaChat] Function calls:', data.functionCalls.length);
  console.log('[useLizaChat] Text response:', data.textResponse || 'NONE');
  
  // Execute each function call SILENTLY
  for (const fc of data.functionCalls) {
    console.log(`[useLizaChat] Executing: ${fc.name}`);
    onToolCall(fc);
  }

  // Show text response in chat (if exists)
  if (data.textResponse && data.textResponse.trim()) {
    setMessages(prev => [...prev, {
      role: 'assistant',
      content: data.textResponse
    }]);
  } else {
    console.warn('[useLizaChat] ⚠️ No text response from API');
  }
  
  setIsLoading(false);
  return;
}
```

### Verification Test 3
```
Input: "navega a Experience"
Expected:
  - Console: "Executing: navigate_to_building"
  - Console: "Text response: ¡Vamos a Experience!..."
  - Chat shows ONLY: "¡Vamos a Experience!..." (clean text)
  - NO code, NO JSON, NO function names
```

---

## 🔧 Step 4: Verify Navigation Args

### Problem
Navigation might not work if building name format is wrong

### Investigation
Check what `onSetSection` expects:
- "Experience" (capitalized)?
- "experience" (lowercase)?
- Or section ID from buildings-config.json?

### File: `src/hooks/liza/useLizaTour.js`

**Current Code (lines 23-27)**:
```javascript
if (onSetSection) {
  console.log(`[useLizaTour] Calling onSetSection("${building}")`);
  onSetSection(building, { skipCameraChange: true });
}
```

**Possible Fix**: Validate building name
```javascript
const validBuildings = ["Skills", "Experience", "Vision", "Projects", "About", "Docs"];

if (!validBuildings.includes(building)) {
  console.error(`[useLizaTour] ❌ Invalid building: "${building}"`);
  return {
    success: false,
    message: `❌ Building "${building}" not found`
  };
}
```

### Verification Test 4
```
Input: "show me experience"
Expected:
  - Gemini calls: navigate_to_building({ building: "Experience", mode: "FOCUS" })
  - Console: "Calling onSetSection("Experience")"
  - Camera MOVES to Experience building
  - Orbits around it (FOCUS mode)
```

---

## 📊 Complete Verification Plan

### Manual Tests (User Performs)

#### Test Suite 1: Theme Application
1. **Input**: "change to chernobyl"
   - ✅ Theme applies (green tones)
   - ✅ No API call (instant)
   - ✅ Chat shows: "✨ Tema Chernobyl Zone aplicado!"

2. **Input**: "make it pink"
   - ✅ Detects synonym → cyberpunk
   - ✅ Theme applies (pink/purple)
   - ✅ Chat shows: "✨ Tema Cyberpunk Neon aplicado!"

3. **Input**: "ocean theme"
   - ✅ Theme applies (blue)
   - ✅ Chat shows: "✨ Tema Deep Ocean aplicado!"

#### Test Suite 2: Navigation
1. **Input**: "navega a Experience"
   - ✅ Camera moves to Experience
   - ✅ Orbits around (FOCUS mode)
   - ✅ Chat shows Gemini's text response
   - ✅ NO code/JSON visible

2. **Input**: "show me skills"
   - ✅ Camera moves to Skills
   - ✅ Chat shows text response

3. **Input**: "enter vision building"
   - ✅ Camera enters Vision (INSIDE mode)
   - ✅ Shows interior content

#### Test Suite 3: Content Questions
1. **Input**: "cuéntame sobre React"
   - ✅ API called (no preprocessor)
   - ✅ Smart context loads skills.json
   - ✅ Chat shows detailed response
   - ✅ NO navigation, NO theme change

2. **Input**: "what's your experience with AI?"
   - ✅ Smart context loads exp.json
   - ✅ Chat shows AI experience details
   - ✅ NO theme change

### Automated Checks (Console Logs)

**After each user input, verify console shows**:
- `[Pre-Processing] Analyzing: <message>`
- `[Pre-Processing] Keyword detected: ...` OR `[Pre-Processing] No theme keyword`
- For themes: `[useAITheme] ✅ Theme applied!`
- For navigation: `[useLizaTour] Calling onSetSection(...)`
- For API: `[LIZA API] Got response from Gemini`

---

## ⚠️ Potential Blockers

### Blocker 1: API Response Format Unknown
**Issue**: Don't know exact structure of Gemini response
**Mitigation**: Add extensive logging in Step 2 first, then adjust code

### Blocker 2: Navigation Args Mismatch
**Issue**: Building names might need different format
**Mitigation**: Check logs in Step 4, compare with buildings-config.json

### Blocker 3: Theme Context Not Updating
**Issue**: applyThemeToContext() might not trigger re-render
**Mitigation**: Check AIThemeContext implementation if theme doesn't apply

---

## 📝 Implementation Order

1. ✅ **Step 1**: Fix useAITheme.js (30 mins)
   - Change to static import
   - Test theme application

2. ✅ **Step 2**: Investigate API response (15 mins)
   - Add logging to api/liza/chat.js
   - See exact response structure

3. ✅ **Step 3**: Fix message display (20 mins)
   - Update useLizaChat.js based on Step 2 findings
   - Test text extraction

4. ✅ **Step 4**: Verify navigation (15 mins)
   - Add validation to useLizaTour.js
   - Test camera movement

5. ✅ **Step 5**: Complete testing (30 mins)
   - Run all test suites
   - Fix any remaining issues

**Total Estimated Time**: ~2 hours

---

## ✅ Success Criteria

1. ✅ Theme application works for all 13 themes + synonyms
2. ✅ Navigation works for all 6 buildings (FOCUS and INSIDE modes)
3. ✅ Chat shows ONLY text responses (NO code/JSON)
4. ✅ Preprocessor correctly skips API for themes
5. ✅ Smart context loads correct JSONs
6. ✅ No console errors

---

## 📦 Git Deliverables (After User Approval)

**User will create commit when satisfied. AI provides suggestions**:

### Suggested Commit Message
```
fix(liza): Correct theme, navigation, and message flow

- Fix useAITheme async import issue (static import)
- Fix API response text extraction
- Clean chat UI (show text only, hide function calls)
- Verify navigation args format
- Add extensive logging for debugging

Closes #<issue number if applicable>
```

### Suggested PR Description
```
## Problem
LIZA's theme application and navigation were broken due to async timing issues and incorrect response parsing.

## Solution
1. Changed useAITheme to use static imports (synchronous)
2. Fixed API response parsing to extract text correctly
3. Cleaned up chat UI to hide function call code
4. Verified navigation args match expected format

## Testing
- ✅ All 13 themes apply correctly
- ✅ Navigation works to all 6 buildings
- ✅ Chat shows clean text responses
- ✅ No console errors

## Files Changed
- `src/hooks/liza/useAITheme.js`
- `src/hooks/liza/useLizaChat.js`
- `api/liza/chat.js`
- `src/hooks/liza/useLizaTour.js`
``

---

**Status**: AWAITING USER APPROVAL TO PROCEED WITH IMPLEMENTATION
