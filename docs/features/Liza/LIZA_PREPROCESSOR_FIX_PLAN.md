# LIZA Preprocessor Fix - Implementation Plan

**Date**: 2025-12-02  
**Status**: Awaiting User Approval  
**Scope**: Fix preprocessor to use real theme architecture

---

## 🎯 Objective

Fix `liza-preprocessor.js` to:
1. Use **theme-config.json** (13 real themes)
2. Call **useAITheme.applyTheme()** correctly
3. Generate automatic synonyms
4. NOT touch CockpitConsole

---

## 📋 User Decisions

1. ✅ **Sinónimos**: Automáticos (AI genera basándose en theme names)
2. ✅ **Ubicación**: `src/config/theme-synonyms.json` (escalable)
3. ✅ **Strategy**: Fix preprocessor only, keep smart-context
4. ✅ **Git**: NO tocar - trabajo en local hasta aprobación final

---

## 🏗️ Architecture Analysis

### Existing (DO NOT MODIFY)

#### `src/config/theme-config.json`
- **13 themes**: default, scifi, cyberpunk, alien, pandora, mars, desert, ocean, matrix, sunset, industrial, tokyo, chernobyl
- Each has: `name`, `displayName`, `primaryColor`, `terrain`, `hdr`

#### `src/config/visual-states.json`
- **1334 lines** of material configurations
- Per building: HTLand, Experience, Skills, etc.
- Per theme: material properties (color, roughness, metalness, emissive)

#### `src/hooks/liza/useAITheme.js`
- Function: `applyTheme(themeData)`
- Expects: `{ themeName: "chernobyl" }`
- Does: Reads configs, applies materials, fires event

#### `src/components/Cockpit/CockpitConsole.jsx`
- ⛔ **DO NOT TOUCH** - Works perfectly
- Uses: `applyTheme({ themeName })` directly

### To Fix

#### `src/utils/liza/liza-preprocessor.js`
- ❌ Currently: Hardcoded 3 themes
- ✅ Should: Read from theme-config.json (13 themes)
- ❌ Currently: Returns invented object
- ✅ Should: Return `{ themeName: "key" }`

---

## 📝 Implementation Steps

### Step 1: Create `theme-synonyms.json`

**File**: `src/config/theme-synonyms.json`

**Content**: Auto-generated synonyms for each theme

```json
{
  "cyberpunk": ["neon", "pink", "rosa", "purple", "dystopia", "futuristic", "ciberpunk"],
  "mars": ["red", "rojo", "planet", "colony", "marte", "desert", "desierto"],
  "pandora": ["jungle", "selva", "green", "verde", "forest", "bosque", "bioluminescent"],
  "ocean": ["water", "blue", "azul", "sea", "underwater", "deep"],
  "matrix": ["green", "code", "digital", "rain", "cyber"],
  "sunset": ["gold", "golden", "orange", "warm", "evening"],
  "alien": ["extraterrestrial", "purple", "space", "ufo"],
  "scifi": ["futuristic", "tech", "space", "future"],
  "desert": ["sand", "wasteland", "arid", "dry"],
  "industrial": ["metal", "factory", "rust", "steel"],
  "tokyo": ["neon", "japan", "japanese", "night", "street"],
  "chernobyl": ["nuclear", "radioactive", "wasteland", "post-apocalyptic", "radiation"],
  "default": ["original", "normal", "reset", "base"]
}
```

**Strategy**: 
- Theme name itself is primary keyword
- displayName also works
- Synonyms based on theme description/concept
- English + Spanish where applicable

---

### Step 2: Rewrite `liza-preprocessor.js`

**File**: `src/utils/liza/liza-preprocessor.js`

**Changes**:

#### Import Real Configs
```javascript
import themeConfig from '../../config/theme-config.json' assert { type: 'json' };
import themeSynonyms from '../../config/theme-synonyms.json' assert { type: 'json' };
```

#### Build Keyword Map Dynamically
```javascript
function buildKeywordMap() {
  const keywordMap = {};
  
  // For each theme in theme-config.json
  Object.keys(themeConfig.themes).forEach(themeKey => {
    const theme = themeConfig.themes[themeKey];
    
    // Primary: theme key itself (e.g., "cyberpunk")
    keywordMap[themeKey.toLowerCase()] = themeKey;
    
    // Secondary: displayName (e.g., "cyberpunk neon")
    if (theme.displayName) {
      keywordMap[theme.displayName.toLowerCase()] = themeKey;
    }
    
    // Tertiary: synonyms from theme-synonyms.json
    if (themeSynonyms[themeKey]) {
      themeSynonyms[themeKey].forEach(synonym => {
        keywordMap[synonym.toLowerCase()] = themeKey;
      });
    }
  });
  
  return keywordMap;
}

const KEYWORD_MAP = buildKeywordMap();
// Result: ~60+ keywords → 13 theme keys
```

#### Detect Theme in Message
```javascript
export function preprocessMessage(message) {
  const lowerMessage = message.toLowerCase();
  
  console.log('[Pre-Processing] Analyzing:', message);
  
  // Check each keyword
  for (const [keyword, themeKey] of Object.entries(KEYWORD_MAP)) {
    if (lowerMessage.includes(keyword)) {
      console.log(`[Pre-Processing] ✅ Keyword "${keyword}" → theme "${themeKey}"`);
      
      // Verify theme exists in config (safety check)
      if (!themeConfig.themes[themeKey]) {
        console.error(`[Pre-Processing] Theme "${themeKey}" not in theme-config.json`);
        return null;
      }
      
      console.log('[Pre-Processing] ⚡ SKIP API - Direct theme application');
      
      return {
        type: 'apply_visual_theme',
        args: {
          themeName: themeKey  // ✅ CORRECT - "chernobyl", not hardcoded object
        },
        skipAPI: true,
        message: `✨ Tema ${themeConfig.themes[themeKey].displayName} aplicado!`
      };
    }
  }
  
  // No theme keyword found
  console.log('[Pre-Processing] No theme keyword - calling API');
  return null;
}
```

**Key Changes**:
1. ✅ Reads `theme-config.json` (all 13 themes)
2. ✅ Reads `theme-synonyms.json` (60+ keywords)
3. ✅ Returns `{ themeName: "key" }` not hardcoded object
4. ✅ Automatic - no hardcoded themes

---

### Step 3: Verify Integration with `useAITheme`

**No changes needed** - `useLizaTour.js` already calls:

```javascript
const result = applyTheme(toolCall.args);
// toolCall.args = { themeName: "chernobyl" } ✅
```

`useAITheme.applyTheme` receives this and:
1. Reads `theme-config.json` → gets theme config
2. Reads `visual-states.json` → gets materials
3. Applies to all buildings

---

### Step 4: Cleanup

**Files to DELETE**:
- ❌ `config/theme-keywords.json` (old, hardcoded)

**Files to KEEP**:
- ✅ `src/utils/liza/liza-preprocessor.js` (rewritten)
- ✅ `src/utils/liza/liza-smart-context.js` (unchanged)
- ✅ `src/utils/r2-utils.js` (unchanged)
- ✅ `config/keywords-map.json` (unchanged)

---

## 🧪 Verification Plan

### Test Cases

#### Test 1: Official Theme Names
```
Input: "change to chernobyl"
Expected:
  - Keyword detected: "chernobyl" → "chernobyl"
  - Skip API: Yes
  - Apply theme: Chernobyl Zone
  - Materials: visual-states.json "chernobyl" variant
Result: ✅ Theme applied instantly
```

#### Test 2: Display Names
```
Input: "switch to cyberpunk neon"
Expected:
  - Keyword detected: "cyberpunk neon" → "cyberpunk"
  - Skip API: Yes
  - Apply theme: Cyberpunk Neon
Result: ✅ Theme applied instantly
```

#### Test 3: Synonyms (English)
```
Input: "make it pink"
Expected:
  - Keyword detected: "pink" → "cyberpunk"
  - Skip API: Yes
  - Apply theme: Cyberpunk Neon
Result: ✅ Theme applied instantly
```

#### Test 4: Synonyms (Spanish)
```
Input: "cambia a verde"
Expected:
  - Keyword detected: "verde" → "pandora"
  - Skip API: Yes
  - Apply theme: Pandora Forest
Result: ✅ Theme applied instantly
```

#### Test 5: Complex Synonym
```
Input: "nuclear wasteland theme"
Expected:
  - Keyword detected: "nuclear" → "chernobyl"
  - Skip API: Yes
  - Apply theme: Chernobyl Zone
Result: ✅ Theme applied instantly
```

#### Test 6: No Theme Keyword
```
Input: "tell me about your experience"
Expected:
  - No keyword detected
  - Skip API: No
  - Call Gemini API
  - Gemini responds with text (no theme change)
Result: ✅ API called, normal response
```

#### Test 7: Navigation (Should NOT Trigger Theme)
```
Input: "go to experience"
Expected:
  - No theme keyword detected
  - Skip API: No
  - Call Gemini API
  - Gemini: navigate_to_building("Experience")
Result: ✅ Navigation works
```

#### Test 8: All 13 Themes
Test each official name:
- "default" → Original HekTek ✅
- "scifi" → SciFi Future ✅
- "cyberpunk" → Cyberpunk Neon ✅
- "alien" → Alien World ✅
- "pandora" → Pandora Forest ✅
- "mars" → Mars Colony ✅
- "desert" → Desert Wasteland ✅
- "ocean" → Deep Ocean ✅
- "matrix" → The Matrix ✅
- "sunset" → Golden Sunset ✅
- "industrial" → Industrial Complex ✅
- "tokyo" → Tokyo Nights ✅
- "chernobyl" → Chernobyl Zone ✅

---

## 📊 Coverage

### Keyword Coverage
- **13 official names**: chernobyl, mars, etc.
- **13 display names**: "Chernobyl Zone", "Mars Colony", etc.
- **~60 synonyms**: pink, nuclear, green, etc.
- **Total**: ~86 keywords → 13 themes

### Language Coverage
- English: primary
- Spanish: secondary (common words)
- Expandable: add more to `theme-synonyms.json`

---

## 🚫 What We Will NOT Touch

1. ⛔ `CockpitConsole.jsx` - DO NOT MODIFY
2. ⛔ `useAITheme.js` - Already works
3. ⛔ `theme-config.json` - Use as-is
4. ⛔ `visual-states.json` - Use as-is
5. ⛔ Smart context system - Keep unchanged
6. ⛔ Git history - No commits until approved

---

## 📁 Files Modified

### New Files
1. `src/config/theme-synonyms.json` - Synonym mappings

### Modified Files
1. `src/utils/liza/liza-preprocessor.js` - Rewritten to use real architecture

### Deleted Files
1. `config/theme-keywords.json` - Old hardcoded file (basura)

---

## ⏱️ Timeline

1. **Create theme-synonyms.json**: 5 mins
2. **Rewrite liza-preprocessor.js**: 10 mins
3. **Delete old files**: 1 min
4. **Test all 13 themes**: 15 mins
5. **Test synonyms**: 10 mins

**Total**: ~40 minutes

---

## ✅ Success Criteria

1. All 13 themes work with official names
2. Synonyms work (pink → cyberpunk, nuclear → chernobyl)
3. No API call for theme changes
4. API call for navigation and questions
5. CockpitConsole still works (not touched)
6. Logging shows keyword detection

---

## 🔴 Risks

### Low Risk
- Synonym conflicts (e.g., "green" could be pandora or matrix)
  - Mitigation: First match wins, order matters
  
### No Risk
- Breaking CockpitConsole (we don't touch it)
- Breaking smart-context (not modified)

---

## 📝 Next Steps

**WAITING FOR USER APPROVAL**

After approval:
1. Create `theme-synonyms.json`
2. Rewrite `liza-preprocessor.js`
3. Delete old files
4. Test locally
5. User tests
6. (Later) Update docs, blog, etc.
7. (Later) Git commit when user approves

---

**Questions Before Implementation?**

- Are the synonym choices reasonable?
- Should I prioritize certain keywords over others?
- Any specific synonyms you want added/changed?
