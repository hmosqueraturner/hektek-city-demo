# LIZA Context Optimization & Intelligence Enhancement

**Feature Initiative**: Optimize LIZA's RAG context and implement intelligent knowledge loading  
**Date**: December 1, 2025  
**Status**: PLANNING - Awaiting User Approval  
**Related Docs**: AI_WORKFLOW_PROTOCOL.md

---

## 🎯 Problem Statement

### Current Issues

1. **Context Overload**: `buildPortfolioContext()` sends ~8-12KB of JSON stringify to Gemini on EVERY request
   - exp.json: 13 roles (520 lines)
   - skills.json: 90+ technologies (680 lines)
   - about.json: Enhanced background (91 lines)
   - **Total**: ~1,290 lines of JSON → Saturating Gemini's context window

2. **No R2 Integration for LIZA**: API uses bundled JSONs (build-time), not R2 (runtime)
   - Frontend uses `useRemoteConfig` → R2 runtime loading ✅
   - LIZA uses static imports → Bundled at build time ❌
   - **Result**: LIZA needs redeploy to see updated data

3. **Visual Theme Management Will Scale**: `visual-states.json` (1,334 lines, 37KB) will grow with more themes/buildings
   - Currently 7 themes, 5 buildings
   - Future: 15+ themes, 10+ buildings → File will exceed 100KB

4. **Model llamando API innecesariamente**: LIZA calls Gemini API even for pre-established themes
   - User says "cyberpunk" → Gemini generates theme config → Waste of tokens/latency
   - CockpitConsole just applies theme directly → No API needed

---

## 💡 Proposed Solutions

### Strategy 1: Lazy Loading de Knowledge

**Keyword Detection System** →  Only send relevant JSON slices based on query

#### How It Works
```
User: "What's your experience with AI?"
     ↓
Keyword Detector: ["experience", "AI"] 
     ↓
Knowledge Loader: exp.json (filtered: only AI-related roles)
     ↓
Context: ~1KB instead of 12KB
```

#### Implementation
1. **Create keyword map** in R2: `keywords-map.json`
```json
{
  "experience_keywords": ["experience", "work", "job", "company", "role"],
  "skills_keywords": ["technology", "skill", "tools", "framework"],  
  "ai_keywords": ["ai", "artificial intelligence", "ml", "mlops"],
  "theme_keywords": ["cyberpunk", "mars", "pandora", "desert", "scifi", "alien", "default"]
}
```

2. **Smart Context Builder** (`liza-smart-context.js`):
   - Detects keywords in user message
   - Fetches ONLY relevant JSON from R2
   - Builds minimal context (2-3KB max)

3. **RAG Contextual Loading**:
   - General query → Summary context (roles count, skills categories)
   - Specific query → Full detail for that domain
   - Theme query → NO context, direct theme application

---

### Strategy 2: Google AI Studio Pro Vector DB Integration

**Advantages con tu plan Pro**:
- ✅ **Embeddings API**: `gemini-embedding-001` (8K tokens, 3072 dimensions)
- ✅ **File Search Tool**: Managed RAG con auto-citations
- ✅ **Higher Limits**: Pro membership = más queries/día
- ✅ **Estado del arte**: Mejor calidad que soluciones custom

#### Architecture
```
[exp.json, skills.json, about.json]
           ↓
  Gemini Embedding API (batch)
           ↓
   Vector DB (Vertex AI Vector Search)
           ↓
  LIZA Query → Semantic Search → Top-K results
           ↓
   Context Window (solo relevante)
```

#### Trade-offs
**Pros**:
- Semantic search > Keyword matching
- Escalable (100+ JSON files)
- Auto-update (re-index R2 on change)

**Cons**:
- Complejidad adicional
- Costo (pero Pro membership lo mitiga)
- Latency extra (~100-200ms)

#### Recommendation
**FASE 1**: Lazy Loading (keyword)  
**FASE 2**: Vector DB (cuando tengas 20+ JSON sources)

---

### Strategy 3: Theme Pre-Establishment Detection

**Problem**: LIZA usa Gemini API para generar themes que ya existen

**Solution**: Detect pre-established themes BEFORE calling Gemini

#### Implementation in `liza-theme-generator.js`

**Current Flow**:
```javascript
User: "change to cyberpunk"
  → Gemini API call (genera theme config)
  → applyVisualTheme(generatedConfig)
```

**Optimized Flow**:
```javascript
User: "change to cyberpunk"
  → Keyword detector: "cyberpunk" in PRE_ESTABLISHED_THEMES
  → Load from theme-config.json (R2)
  → applyVisualTheme(existingTheme) 
  → NO API CALL ✅
```

#### Code Structure
```javascript
// liza-theme-generator.js
const PRE_ESTABLISHED_THEMES = ['cyberpunk', 'mars', 'pandora', 'scifi', 'alien', 'desert', 'default'];

export async function applyThemeIfPreEstablished(themeName) {
  const normalized = themeName.toLowerCase().trim();
  
  if (PRE_ESTABLISHED_THEMES.includes(normalized)) {
    // Load from R2 or use existing CockpitConsole logic
    return {
      isPreEstablished: true,
      themeConfig: await fetchThemeFromR2(normalized)
    };
  }
  
  // Not pre-established, need Gemini generation
  return { isPreEstablished: false };
}
```

#### Integration with Function Calling
```javascript
// Gemini function tool modificado
{
  name: "handle_visual_theme_change",
  description: "Apply visual theme. ONLY use Gemini generation for custom themes NOT in: cyberpunk, mars, pandora, scifi, alien, desert",
  parameters: {
    theme_name: { type: "string" },
    is_custom: { type: "boolean", description: "Set true only if theme is NOT pre-established" }
  }
}
```

**Benefits**:
- 🚀 **Instant theme application** (no API delay)
- 💰 **Token savings** (70% of theme requests are pre-established)
- 🎯 **Same logic** as CockpitConsole selector

---

### Strategy 4: JSON to Structured Data (No TSON)

**Research Findings**: 
- Gemini API **NO soporta TSON** (Typed JSON custom formats)
- Gemini usa **JSON Schema** para structured output
- TSON no aporta beneficio vs JSON Schema optimizado

**Recommendation**: **NO implementar TSON**

**Alternative**: Use Gemini's JSON Schema for structured responses
```javascript
const model = genAI.getGenerativeModel({
  model: 'gemini-2.0-flash-exp',
  generationConfig: {
    responseM imeType: "application/json",
    responseSchema: {
      type: "object",
      properties: {
        roles: { type: "array", items: { type: "object" } },
        relevance_score: { type: "number" }
      }
    }
  }
});
```

**Benefits**:
- Guaranteed valid JSON responses
- Type-safe parsing
- No custom serialization overhead

---

## 📂 Proposed Changes

### Component 1: Smart Context System

#### [NEW] `src/utils/liza/liza-smart-context.js`
```javascript
/**
 * Intelligent context builder with keyword detection and lazy loading
 */
import { fetchFromR2 } from '../r2-utils.js';

const KEYWORD_MAP_URL = 'https://assets.hectortechno.com/keywords-map.json';

export async function buildSmartContext(userMessage) {
  // 1. Detect keywords
  const keywords = await detectKeywords(userMessage);
  
  // 2. Determine which JSONs to load
  const requiredSources = determineDataSources(keywords);
  
  // 3. Fetch only needed data from R2
  const contextData = await fetchRelevantData(requiredSources, keywords);
  
  // 4. Build minimal context
  return formatContext(contextData);
}

async function detectKeywords(message) {
  const keywordMap = await fetchFromR2('keywords-map.json');
  const detected = [];
  
  for (const [category, words] of Object.entries(keywordMap)) {
    if (words.some(word => message.toLowerCase().includes(word))) {
      detected.push(category);
    }
  }
  
  return detected;
}

function determineDataSources(keywords) {
  const sources = [];
  
  if (keywords.includes('experience_keywords')) sources.push('exp');
  if (keywords.includes('skills_keywords')) sources.push('skills');
  if (keywords.includes('ai_keywords')) sources.push('skills', 'exp'); // Cross-reference
  
  return [...new Set(sources)]; // Deduplicate
}

async function fetchRelevantData(sources, keywords) {
  const data = {};
  
  for (const source of sources) {
    const json = await fetchFromR2(`${source}.json`);
    
    // Filter data based on keywords
    if (source === 'exp' && keywords.includes('ai_keywords')) {
      // Only return AI-related roles
      data.exp = json.exp.filter(role => 
        role.tags.some(tag => tag.toLowerCase().includes('ai') || tag.toLowerCase().includes('mlops'))
      );
    } else {
      data[source] = json;
    }
  }
  
  return data;
}

function formatContext(data) {
  let context = '# HEKTEK City Knowledge Base\n\n';
  
  if (data.exp) {
    context += `## Experience (${data.exp.length} relevant roles)\n`;
    data.exp.forEach(role => {
      context += `- ${role.title} at ${role.company_name} (${role.date})\n`;
      context += `  ${role.achievements[0]}\n`; // First achievement only
    });
  }
  
  if (data.skills) {
    context += `\n## Skills\n`;
    // Summarize instead of full JSON
    context += `- Programming: ${data.skills.programming?.length || 0} categories\n`;
    context += `- DevOps: ${data.skills.devOps?.length || 0} skills\n`;
  }
  
  return context.trim();
}
```

#### [MODIFY] `api/liza/chat.js`
```diff
- import { buildPortfolioContext } from '../../src/utils/liza/liza-knowledge.js';
+ import { buildSmartContext } from '../../src/utils/liza/liza-smart-context.js';

  // Build context with portfolio knowledge
- const contextPrompt = LIZA_SYSTEM_PROMPT + '\n\n' + buildPortfolioContext();
+ const baseContext = LIZA_SYSTEM_PROMPT;
+ const smartContext = await buildSmartContext(message);
+ const contextPrompt = baseContext + '\n\n' + smartContext;
```

**Size Reduction**:
- Before: 8-12KB context
- After: 1-3KB context (70-80% reduction)

---

#### [NEW] `src/utils/r2-utils.js`
```javascript
/**
 * R2 data fetching utilities
 */
const R2_BASE_URL = 'https://assets.hectortechno.com';

export async function fetchFromR2(filename) {
  const url = `${R2_BASE_URL}/${filename}`;
  
  try {
    const response = await fetch(url);
    if (!response.ok) throw new Error(`R2 fetch failed: ${response.status}`);
    return await response.json();
  } catch (error) {
    console.error(`[R2] Error fetching ${filename}:`, error);
    return null;
  }
}

export async function fetchMultipleFromR2(filenames) {
  return Promise.all(filenames.map(fetchFromR2));
}
```

---

#### [NEW] `R2 Config File`: `keywords-map.json`
```json
{
  "experience_keywords": [
    "experience", "work", "job", "company", "role", "position", 
    "career", "worked", "employment", "companies"
  ],
  "skills_keywords": [
    "technology", "skill", "tools", "framework", "programming",
    "language", "database", "cloud", "devops", "agile"
  ],
  "ai_keywords": [
    "ai", "artificial intelligence", "ml", "machine learning",
    "mlops", "aiops", "gemini", "gpt", "llm", "rag"
  ],
  "theme_keywords": [
    "cyberpunk", "mars", "pandora", "desert", "scifi", 
    "alien", "default", "original", "theme", "visual"
  ],
  "about_keywords": [
    "about", "personal", "background", "bio", "who are you",
    "tell me about", "personality", "lifestyle"
  ],
  "vision_keywords": [
    "vision", "goal", "mission", "philosophy", "future",
    "dreams", "aspirations"
  ]
}
```

**Upload to R2**: `https://assets.hectortechno.com/keywords-map.json`

---

### Component 2: Theme Pre-Establishment

#### [MODIFY] `src/utils/liza/liza-theme-generator.js`
```diff
+ import { fetchFromR2 } from '../r2-utils.js';
+ 
+ const PRE_ESTABLISHED_THEMES = [
+   'cyberpunk', 'mars', 'pandora', 'scifi', 'alien', 'desert', 'default', 'original'
+ ];

  export async function generateVisualTheme(themeName) {
+   // Check if pre-established theme
+   const normalized = themeName.toLowerCase().trim();
+   
+   if (PRE_ESTABLISHED_THEMES.includes(normalized)) {
+     console.log(`[LIZA Theme] Pre-established theme detected: ${normalized}`);
+     return await applyPreEstablishedTheme(normalized);
+   }
+   
+   // Custom theme - use Gemini generation
+   console.log(`[LIZA Theme] Custom theme, generating with Gemini: ${themeName}`);
    // ... existing Gemini generation logic
  }

+ async function applyPreEstablishedTheme(themeName) {
+   const themeConfig = await fetchFromR2('theme-config.json');
+   const visualStates = await fetchFromR2('visual-states.json');
+   
+   const theme = themeConfig.themes[themeName];
+   if (!theme) {
+     console.warn(`[LIZA Theme] Theme ${themeName} not found in config`);
+     return null;
+   }
+   
+   // Apply theme using same logic as CockpitConsole
+   return {
+     themeName: theme.name,
+     displayName: theme.displayName,
+     description: theme.description,
+     visualStates: visualStates,
+     skipGeminiCall: true // Flag to not use API
+   };
+ }
```

**Benefits**:
- 🚀 Instant theme switching (no 2-3s Gemini delay)
- 💰 70% token reduction on theme changes
- 🎯 Consistent with CockpitConsole UX

---

#### [MODIFY] `src/utils/liza/liza-tools.js` - Function Definition
```diff
  {
    name: "handle_visual_theme_change",
-   description: "Changes visual theme of the 3D environment",
+   description: "Changes visual theme. Use pre-established themes when possible: cyberpunk, mars, pandora, scifi, alien, desert. Only generate custom themes if user requests something unique.",
    parameters: {
      themeName: {
        type: "string",
-       description: "Name of the theme"
+       description: "Theme name. Use exact match for: cyberpunk, mars, pandora, scifi, alien, desert, original"
+     },
+     isCustom: {
+       type: "boolean",
+       description: "Set true ONLY if theme is NOT one of the pre-established options"
      }
    }
  }
```

---

### Component 3: Theme Data Generation

**Goal**: Create 3-5 more pre-established themes without touching code

#### [MODIFY] `theme-config.json` - Add New Themes
```json
{
  "themes": {
    // ... existing themes ...
    
    "ocean": {
      "name": "Ocean",
      "displayName": "Deep Ocean",
      "terrain": "HTLand",
      "hdr": "HekTek-magic-garden",
      "description": "Underwater tech civilization",
      "primaryColor": "#1e90ff",
      "enabled": true
    },
    "matrix": {
      "name": "Matrix",
      "displayName": "The Matrix",
      "terrain": "HTLand",
      "hdr": "HekTek-custom",
      "description": "Digital rain and green phosphor aesthetic",
      "primaryColor": "#00ff00",
      "enabled": true
    },
    "sunset": {
      "name": "Sunset",
      "displayName": "Golden Sunset",
      "terrain": "HTLand",
      "hdr": "HekTek-comet",
      "description": "Warm evening colors",
      "primaryColor": "#ff8c00",
      "enabled": true
    }
  }
}
```

#### [MODIFY] `visual-states.json` - Add Material Variants

For each new theme, add material definitions for each building. Example pattern:
```json
{
  "buildings": {
    "Experience": {
      "materials": {
        "MAT_Mat_mat_build_base_silver": {
          // ... existing variants ...
          "ocean": {
            "color": "#1e90ff",
            "metalness": 0.85,
            "roughness": 0.15,
            "emissive": "#00ced1",
            "emissiveIntensity": 0.5
          },
          "matrix": {
            "color": "#003300",
            "metalness": 0.7,
            "roughness": 0.3,
            "emissive": "#00ff00",
            "emissiveIntensity": 0.9
          }
        }
      }
    }
  }
}
```

**Generation Strategy**:
1. **Manual for first 2 themes** (ocean, matrix) - define look & feel
2. **Script-assisted** for remaining themes - interpolate colors/properties
3. **Test in CockpitConsole** selector - ensure visual quality

**Script Concept** (optional, for future):
```javascript
// generate-theme-variants.js
const baseTemplate = { /* existing cyberpunk */ };
const newTheme = {
  name: 'ocean',
  primaryColor: '#1e90ff',
  emissiveColor: '#00ced1',
  emissiveIntensity: 0.5
};

// Apply to all materials
const generated = applyThemeToAllMaterials(baseTemplate, newTheme);
```

---

### Component 4: Prompt Engineering Enhancement

#### [MODIFY] `src/utils/liza/liza-system-prompt.js`

```diff
  export const LIZA_SYSTEM_PROMPT = `
  You are LIZA, the AI assistant for HEKTEK City...
  
+ # Context Optimization
+ - You receive ONLY relevant knowledge based on user's query
+ - Don't ask for information not in your context - it's filtered intentionally
+ - If user asks about something not in context, acknowledge and offer related info
+ 
+ # Theme Handling
+ - Pre-established themes: cyberpunk, mars, pandora, scifi, alien, desert, original
+ - For these, use handle_visual_theme_change with isCustom=false
+ - For custom themes, set isCustom=true and I'll generate with Gemini
+ - ALWAYS prefer pre-established themes when user intent is close
+ 
  # Navigation Instructions
  - When user asks about SKILLS → navigate to "Skills" building
  ...
  `;
```

---

## 🧪 Verification Plan

### Phase 1: Lazy Loading Verification

#### Test 1: Context Size Reduction
**Command**: Manual test in local environment
```bash
npm run dev
# Open browser console
# Ask LIZA: "What's your AI experience?"
# Check Network tab for /api/liza/chat payload size
```

**Expected**:
- Before: ~12KB in request body (full JSON stringify)
- After: ~2KB in request body (filtered content)
- ✅ Success: 70-80% reduction

#### Test 2: Keyword Detection Accuracy
**Test Cases**:
| User Query | Expected Sources | Expected Context |
|------------|------------------|------------------|
| "What's your experience with DevOps?" | exp.json | Roles with DevOps tags only |
| "What technologies do you know?" | skills.json | Skills summary |
| "Tell me about yourself" | about.json | Personal background |
| "Change to cyberpunk" | none | No CV context sent |

**Validation**: Check console logs for detected keywords

#### Test 3: R2 Integration
**Command**:
```bash
# Verify R2 files accessible
curl https://assets.hectortechno.com/keywords-map.json
curl https://assets.hectortechno.com/exp.json
curl https://assets.hectortechno.com/skills.json
```

**Expected**: All return valid JSON (200 OK)

---

### Phase 2: Theme Pre-Establishment Verification

#### Test 4: Pre-Established Theme Application
**Manual Test**:
1. Start app in local
2. Ask LIZA: "Change to mars theme"
3. Check console logs for "Pre-established theme detected: mars"
4. Verify theme applied instantly (< 500ms)
5. Check Network tab: NO call to Gemini API

**Expected**:
- ✅ Theme applied via direct config load
- ✅ No Gemini API call
- ✅ Visual change matches CockpitConsole selector

#### Test 5: Custom Theme Generation (Fallback)
**Manual Test**:
1. Ask LIZA: "Change to a neon pink gothic theme"
2. Check console logs for "Custom theme, generating with Gemini"
3. Verify Gemini API called
4. Verify theme generated and applied

**Expected**:
- ✅ Keyword detector: NOT in PRE_ESTABLISHED_THEMES
- ✅ Gemini API called
- ✅ Custom theme applied

#### Test 6: CockpitConsole Consistency
**Manual Test**:
1. Use CockpitConsole selector → Select "Cyberpunk"
2. Ask LIZA: "Change back to original"
3. Ask LIZA: "Show me cyberpunk again"

**Expected**:
- ✅ Both methods produce identical visual results
- ✅ LIZA method is faster (no API delay)

---

### Phase 3: Integration Testing

#### Test 7: End-to-End LIZA Flow
**Scenario**: New user interaction
```
1. User: "Hello LIZA"
   → Light context (about.json summary only)
   
2. User: "What's your experience?"
   → Loads exp.json from R2
   → Responds with career summary
   
3. User: "Tell me about your AI work"
   → Filters exp.json (AI-tagged roles only)
   → Reduced context

4. User: "Make it look cyberpunk"
   → Detects "cyberpunk" keyword
   → NO Gemini call
   → Applies pre-established theme
```

**Expected**: All steps complete successfully with appropriate context sizes

#### Test 8: Fallback to Local (R2 Unavailable)
**Scenario**: R2 down or network error
```bash
# Block R2 domain in browser DevTools
# Ask LIZA questions
```

**Expected**:
- ✅ Falls back to bundled local JSON
- ✅ Warning in console: "R2 fetch failed, using local fallback"
- ✅ Functionality degraded but not broken

---

### Phase 4: Production Validation

#### Test 9: Vercel Deployment
**Command**:
```bash
# After deployment
vercel logs --follow
```

**Test in Production**:
1. Navigate to production URL
2. Interact with LIZA
3. Monitor Vercel logs for errors

**Expected**:
- ✅ No build errors
- ✅ R2 fetches successful (200 OK)
- ✅ Context sizes reduced in logs

#### Test 10: Performance Metrics
**Metrics to Track**:
| Metric | Before | Target After | Tool |
|--------|--------|--------------|------|
| Avg Response Time | 2-3s | 1-1.5s | Browser DevTools |
| Gemini API Calls (theme) | 100% | 30% | Vercel Analytics |
| Context Size | 8-12KB | 1-3KB | Network tab |
| Token Usage/Day | ~50K | ~20K | Google AI Studio |

---

## 🚧 User Review Required

> [!IMPORTANT]
> **Decision Point 1: Vector DB Integration**
> 
> Recommended approach: **Start with Lazy Loading (keyword-based)**
> 
> Reasons:
> - ✅ Simpler implementation
> - ✅ No additional infrastructure
> - ✅ 70-80% context reduction achievable
> - ✅ Can add Vector DB later if needed
> 
> **When to consider Vector DB**:
> - You have 20+ JSON sources
> - Keyword matching isn't accurate enough
> - You want semantic search ("What did you do in banking?" → finds all finance roles)

> [!WARNING]
> **Decision Point 2: Theme Data Generation**
> 
> Plan proposes **3 new themes** (ocean, matrix, sunset)
> 
> Questions:
> 1. Do you want me to generate all material variants for these?
> 2. Should I create a generation script or do manually?
> 3. Any specific visual preferences for these themes?

> [!NOTE]
> **Decision Point 3: R2 File Structure**
> 
> New R2 files needed:
> - `keywords-map.json` (keyword detection rules)
> - Updated versions of exp.json, skills.json, about.json
> 
> Current R2 files already exist for themes:
> - `theme-config.json` ✅
> - `visual-states.json` ✅ (needs expansion for new themes)

---

## 📊 Implementation Priority

### Phase 1: Foundation (Week 1)
- [ ] Create `r2-utils.js`
- [ ] Create `keywords-map.json` and upload to R2
- [ ] Implement `liza-smart-context.js`
- [ ] Modify `api/liza/chat.js` to use smart context

### Phase 2: Theme Optimization (Week 1-2)
- [ ] Modify `liza-theme-generator.js` for pre-establishment
- [ ] Update `liza-tools.js` function definitions
- [ ] Update LIZA system prompt

### Phase 3: Theme Expansion (Week 2)
- [ ] Design 3 new themes (ocean, matrix, sunset)
- [ ] Generate material variants in `visual-states.json`
- [ ] Update `theme-config.json`
- [ ] Test in CockpitConsole selector

### Phase 4: Testing & Deployment (Week 2-3)
- [ ] Complete all verification tests
- [ ] Deploy to production
- [ ] Monitor metrics
- [ ] Iterate based on results

---

## 💰 Cost-Benefit Analysis

### Token Savings (Estimated)
**Current**: ~500 tokens/request × 1000 requests/day = 500K tokens/day

**After Optimization**:
- Lazy loading: ~150 tokens/request (70% reduction)
- Theme pre-establishment: 30% requests avoid Gemini entirely
- **New total**: ~200K tokens/day

**Savings**: 60% token reduction → **300K tokens/day saved**

### Latency Improvement
| Operation | Before | After | Improvement |
|-----------|--------|-------|-------------|
| General Query | 2-3s | 1-1.5s | ~50% faster |
| Theme Change (pre-est) | 2-3s | <500ms | ~85% faster |
| Theme Change (custom) | 2-3s | 2-3s | No change |

### Development Effort
- **Phase 1-2**: ~8-12 hours (core functionality)
- **Phase 3**: ~6-8 hours (theme expansion)
- **Phase 4**: ~4-6 hours (testing)
- **Total**: ~18-26 hours

---

## 🎯 Success Criteria

✅ **Context size reduced by 70%+** (12KB → 3KB average)  
✅ **Response latency improved by 40%+** (2.5s → 1.5s average)  
✅ **Pre-established themes apply instantly** (<500ms)  
✅ **Token usage reduced by 60%+** (500K → 200K tokens/day)  
✅ **R2 integration working** (no redeploy needed for data updates)  
✅ **3+ new themes fully functional** (ocean, matrix, sunset)  
✅ **No degradation** in LIZA response quality or navigation accuracy

---

**Status**: ⏸️ AWAITING USER APPROVAL  
**Next Step**: User reviews plan → Approves phases → Implementation begins

**Questions for User**:
1. Approve lazy loading approach vs Vector DB for Phase 1?
2. Which themes to prioritize for Phase 3? (ocean, matrix, sunset, or others?)
3. Should I create theme generation script or manual definitions?
4. Any changes to proposed file structure or R2 organization?
