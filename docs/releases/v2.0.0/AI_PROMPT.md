# 🤖 AI Prompt for HEKTEK City v2.0 Final Optimizations

**Use this prompt with Claude, ChatGPT, or any capable AI model to complete the implementation.**

---

## 📋 Context Files to Share

Before using this prompt, upload/share these files with the AI:

### Required Files (Core Context)
1. `RELEASE_V2_FINAL_OPTIMIZATIONS.md` - Complete strategy document
2. `src/utils/assetsHelper.js` - Current asset management
3. `src/components/MapRPG.jsx` - Main scene component
4. `src/components/EnhancedModels.jsx` - Current model loading
5. `src/config/visual-states.json` - Current material definitions
6. `src/hooks/useDynamicMaterials.js` - Runtime materials hook
7. `package.json` - Dependencies and scripts

### Supporting Files (Helpful Context)
8. `src/components/MapControlsPanel.jsx` - UI controls
9. `src/components/DynamicBuildingModel.jsx` - Building with materials
10. `README.md` - Project overview
11. `CHANGELOG.md` - Version history

### Optional Files (If Available)
12. `HTLand.glb` metadata (size, poly count) - if you have it
13. Screenshots of current UI - for reference

---

## 🎯 The Prompt

```
I'm working on HEKTEK City, a 3D portfolio experience built with React Three Fiber. I need to implement final optimizations for Release v2.0.0 before launching.

## Project Context

HEKTEK City is an interactive 3D cityscape where buildings represent different portfolio sections. We've just implemented a Runtime Materials System that allows changing building appearances via JSON without rebuilding. Now we need to optimize the terrain (HTLand.glb - currently 300MB) and externalize all hardcoded configurations to JSON files.

## Current Architecture

- **Frontend:** React 18.3 + Vite 7.1
- **3D:** React Three Fiber 9.3 + Three.js 0.179
- **Assets:** Cloudflare R2 CDN
- **Features:** 7 themes, dynamic materials, navigable buildings

## Implementation Goals

Based on the detailed strategy in RELEASE_V2_FINAL_OPTIMIZATIONS.md, I need you to:

### Phase 1: JSON Configuration Files (PRIORITY 1)

Create these new JSON config files:

1. **src/config/theme-config.json**
   - Move THEME_CONFIGS from assetsHelper.js
   - All themes should use "HTLand" as terrain
   - Map HDR per theme (see RELEASE_V2_FINAL_OPTIMIZATIONS.md for exact structure)

2. **src/config/decoratives-config.json**
   - Keep only 1 decorative per theme as example
   - cyberpunk theme: robotHT (enabled)
   - Other themes: 1 example each (disabled)
   - Include position, rotation, scale, enabled flag

3. **src/config/buildings-config.json**
   - Extract positions, scales, camera markers from MapRPG.jsx
   - Include label configurations (text, position, neon effects)
   - For each building: Experience, Skills, Vision

4. **src/config/content-sources.json**
   - Define URLs for blog, about, projects content
   - Cloudflare R2 base URLs
   - Cache strategy configuration

### Phase 2: Custom Hook for Config Loading (PRIORITY 2)

Create **src/hooks/useConfigLoader.js**:

Requirements:
- Load configs from local JSON files in dev
- Support CDN loading in production (env var controlled)
- Implement localStorage caching with TTL
- Graceful fallback to local if CDN fails
- TypeScript-ready (JSDocs)

```javascript
// Usage example
const themeConfig = useConfigLoader('theme-config');
const buildings = useConfigLoader('buildings-config');
```

### Phase 3: Terrain LOD Component (PRIORITY 3)

Create **src/components/LODTerrain.jsx**:

Requirements:
- Progressive loading: 2K version first, then 4K in background
- Smooth crossfade transition when upgrading
- Proper memory cleanup (dispose old geometries)
- Support for quality selector (1k, 2k, 4k, 8k variants)
- Apply dynamic materials using DynamicBuildingModel pattern

```javascript
// Usage
<LODTerrain
  initialQuality="2k"
  targetQuality="4k"
  currentTheme={theme}
  onLoadProgress={(progress) => setLoadProgress(progress)}
/>
```

### Phase 4: Refactor assetsHelper.js (PRIORITY 4)

Update **src/utils/assetsHelper.js**:

Changes needed:
- Remove hardcoded THEME_CONFIGS object
- Remove hardcoded DECO_* objects
- Add functions to work with JSON configs
- Keep backward compatibility
- Add terrain quality URL builder

```javascript
// New functions to add
AssetManager.getTerrainUrl(quality = '2k') // Returns HTLand_2k.glb URL
AssetManager.loadThemeConfig() // Returns theme-config.json data
AssetManager.loadBuildingsConfig() // Returns buildings-config.json data
```

### Phase 5: Update MapRPG.jsx (PRIORITY 5)

Modify **src/components/MapRPG.jsx**:

Changes needed:
- Use useConfigLoader for all configs
- Load HTLand ONCE at mount (never reload)
- Remove terrain switching logic
- Apply dynamic materials to terrain using visual-states.json
- Only change HDR when theme changes
- Use buildings-config.json for positions/scales

Key behavior:
```javascript
// Pseudo-code
const themeConfig = useConfigLoader('theme-config');
const buildingsConfig = useConfigLoader('buildings-config');

useEffect(() => {
  // Load HTLand once, never unload
  loadTerrain('HTLand');
}, []);

useEffect(() => {
  // Only change HDR when theme changes
  const hdr = themeConfig.themes[currentTheme].hdr;
  setHdrEnvironment(hdr);
}, [currentTheme]);
```

### Phase 6: Add Terrain Quality Selector (PRIORITY 6)

Update **src/components/MapControlsPanel.jsx**:

Add a quality selector:
```jsx
<Select
  label="Terrain Quality"
  value={terrainQuality}
  onChange={handleQualityChange}
  options={[
    { value: '1k', label: '1K (15MB - Mobile)' },
    { value: '2k', label: '2K (30MB - Default)' },
    { value: '4k', label: '4K (60MB - Desktop)' },
    { value: '8k', label: '8K (150MB - High-End)' }
  ]}
/>
```

### Phase 7: Update visual-states.json (PRIORITY 7)

Add terrain materials to **src/config/visual-states.json**:

```json
{
  "buildings": {
    "HTLand": {
      "materials": {
        "MAT_Mat_Terrain": {
          "default": { "color": "#3a5a3a", "roughness": 0.9 },
          "cyberpunk": { "color": "#1a1a2e", "emissive": "#ff006e", "emissiveIntensity": 0.2 },
          "mars": { "color": "#8b4513", "roughness": 1.0 },
          "pandora": { "color": "#2d5a2d", "emissive": "#00ff7f", "emissiveIntensity": 0.3 }
        },
        "MAT_Mat_Water": {
          "default": { "color": "#1e90ff", "roughness": 0.1, "metalness": 0.5 },
          "cyberpunk": { "color": "#00ffff", "emissive": "#00ffff", "emissiveIntensity": 0.5 }
        }
      }
    }
  }
}
```

### Phase 8: Pipeline Script (BONUS - If time permits)

Create **tools/pipeline/optimize-htland.js**:

Purpose: Apply Draco compression and optimizations to terrain GLBs

```javascript
// Using @gltf-transform/cli
// Compress HTLand_1k.glb through HTLand_8k.glb
// Output optimized versions
// Compare before/after sizes
```

## Important Constraints

1. **HTLand Material Names:** Must be `MAT_Mat_Terrain` and `MAT_Mat_Water` (user will rename in Blender)
2. **No Breaking Changes:** All existing functionality must continue to work
3. **Backward Compatibility:** Code should work with or without CDN configs
4. **Performance:** Must not impact frame rate or memory usage negatively
5. **React Best Practices:** Use proper hooks, memoization, cleanup

## Environment Variables

Add to `.env.example`:
```bash
# Config Loading Strategy
VITE_USE_CDN_CONFIGS=false  # true for production, false for dev
VITE_R2_CONFIG_PATH=config  # Path in R2 bucket for configs

# Terrain Quality
VITE_DEFAULT_TERRAIN_QUALITY=2k
VITE_ENABLE_PROGRESSIVE_TERRAIN=true
```

## Deliverables

Please provide:

1. **All JSON config files** (theme-config.json, decoratives-config.json, buildings-config.json, content-sources.json)
2. **useConfigLoader.js hook** (complete implementation)
3. **LODTerrain.jsx component** (complete implementation)
4. **Updated assetsHelper.js** (refactored version)
5. **Updated MapRPG.jsx** (refactored version)
6. **Updated MapControlsPanel.jsx** (with quality selector)
7. **Updated visual-states.json** (with HTLand materials)
8. **Migration notes** (what changed, how to test)

## Code Style & Standards

- Use modern React patterns (hooks, functional components)
- Add comprehensive JSDoc comments
- Use meaningful variable names
- Handle errors gracefully
- Add loading states where appropriate
- Use optional chaining and nullish coalescing
- Follow existing code style in the project

## Testing Requirements

After implementation, I should be able to:

1. Switch themes → HDR changes, terrain materials change, HTLand stays loaded
2. Change terrain quality → Smooth transition to new quality level
3. See decoratives positioned correctly from JSON
4. Update JSON in R2 → Changes reflect after cache expiry
5. Toggle CDN on/off via env var → Works in both modes

## Questions to Ask Before Starting

If anything is unclear, please ask about:
- Exact structure/shape of existing data in current files
- Specific requirements for any feature
- Performance targets or constraints
- Browser compatibility requirements

## Additional Context

- This is a portfolio project, quality and performance are both important
- The user wants to minimize code changes for content updates going forward
- Progressive enhancement is preferred over "all or nothing" loading
- Mobile performance is critical (many visitors on mobile)

## Example Current Usage (for reference)

Current MapRPG.jsx pseudo-structure:
```javascript
// Current (before refactor)
const THEME_CONFIGS = { ... }; // Hardcoded

function MapRPG() {
  const [currentTheme, setCurrentTheme] = useState('default');
  const [currentTerrain, setCurrentTerrain] = useState('HTLand');

  useEffect(() => {
    const config = THEME_CONFIGS[currentTheme];
    setCurrentTerrain(config.terrain); // ← Remove this
    setHdrEnvironment(config.hdr);     // ← Keep this
  }, [currentTheme]);

  // Buildings positioned with hardcoded values
  return (
    <Canvas>
      <EnhancedTerrainModel name={currentTerrain} />
      <EnhancedBuildingModel
        name="Experience"
        position={[0, 0, 0]}  // ← Move to JSON
      />
    </Canvas>
  );
}
```

Target (after refactor):
```javascript
// Target (after refactor)
function MapRPG() {
  const [currentTheme, setCurrentTheme] = useState('default');
  const themeConfig = useConfigLoader('theme-config');
  const buildingsConfig = useConfigLoader('buildings-config');

  useEffect(() => {
    // Only change HDR
    const hdr = themeConfig.themes[currentTheme].hdr;
    setHdrEnvironment(hdr);
  }, [currentTheme]);

  return (
    <Canvas>
      <LODTerrain currentTheme={currentTheme} />
      {Object.entries(buildingsConfig.buildings).map(([name, config]) => (
        <DynamicBuildingModel
          key={name}
          buildingName={name}
          position={config.position}
          scale={config.scale}
          currentTheme={currentTheme}
        />
      ))}
    </Canvas>
  );
}
```

---

## Ready to Start?

I've provided all the files listed at the top. Please review them and implement the changes following the priorities and requirements above.

If you need clarification on any current code structure or have questions before proceeding, please ask!

Let's make HEKTEK City v2.0 amazing! 🚀
```

---

## 📁 How to Use This Prompt

### Option 1: Claude (Recommended)
1. Start a new Claude chat (Projects recommended)
2. Upload all required files listed above
3. Copy-paste the entire prompt
4. Review the output
5. Ask for clarifications or refinements

### Option 2: ChatGPT
1. Start a new chat with GPT-4 or GPT-4 Turbo
2. Use "Advanced Data Analysis" mode if available
3. Upload files (may need to combine into fewer files due to limits)
4. Copy-paste the prompt
5. Iterate on the results

### Option 3: Other AI Models
1. Use any model with good coding capabilities
2. Ensure it can handle large context (10k+ tokens)
3. Provide as many files as the model can accept
4. Use the prompt above
5. Validate outputs carefully

---

## 🎯 Expected Output

The AI should provide:

1. **7 Complete Files:**
   - theme-config.json
   - decoratives-config.json
   - buildings-config.json
   - content-sources.json
   - useConfigLoader.js
   - LODTerrain.jsx
   - (Optional) optimize-htland.js

2. **4 Refactored Files:**
   - assetsHelper.js (modified)
   - MapRPG.jsx (modified)
   - MapControlsPanel.jsx (modified)
   - visual-states.json (modified)

3. **Documentation:**
   - Migration guide
   - Testing instructions
   - What changed and why

4. **Additional Notes:**
   - Potential issues
   - Performance considerations
   - Next steps

---

## ✅ Validation Checklist

After receiving the AI's output, verify:

### Code Quality
- [ ] All files are syntactically correct
- [ ] JSDoc comments present
- [ ] Error handling implemented
- [ ] No obvious performance issues
- [ ] Follows React best practices

### Functionality
- [ ] JSON configs have correct structure
- [ ] useConfigLoader implements caching
- [ ] LODTerrain supports progressive loading
- [ ] assetsHelper backward compatible
- [ ] MapRPG uses configs correctly

### Integration
- [ ] Files reference each other correctly
- [ ] Import paths are correct
- [ ] No circular dependencies
- [ ] Env vars properly used

### Testing
- [ ] Can test locally (CDN off)
- [ ] Can test with CDN (CDN on)
- [ ] Quality selector functional
- [ ] Theme switching works
- [ ] Materials apply correctly

---

## 🐛 Common Issues & Fixes

### Issue: AI hallucinates file paths
**Fix:** Verify all import paths against actual project structure

### Issue: JSON structure doesn't match usage
**Fix:** Cross-reference with existing code that will consume the JSON

### Issue: Missing error handling
**Fix:** Add try-catch blocks and fallbacks

### Issue: Over-complicated solution
**Fix:** Ask AI to simplify while meeting requirements

### Issue: Doesn't match existing patterns
**Fix:** Provide more examples of current code style

---

## 🔄 Iteration Tips

If the first output isn't perfect:

1. **Be Specific:**
   - "The useConfigLoader hook doesn't handle cache expiry correctly. Please add TTL checking."

2. **Provide Examples:**
   - "Here's how we currently load configs: [paste code]. Please match this pattern."

3. **Ask for Refinements:**
   - "Can you optimize the LODTerrain component for better performance?"

4. **Request Explanations:**
   - "Why did you choose this approach for X?"

5. **Break Down Complex Parts:**
   - "Let's focus just on the LODTerrain component first, then we'll do the others."

---

## 🎓 Learning Opportunity

This implementation teaches:
- Progressive asset loading in Three.js
- JSON-driven architecture patterns
- CDN-based content management
- React performance optimization
- LOD (Level of Detail) techniques

Take time to understand the solutions, don't just copy-paste!

---

## 🚀 After Implementation

Once you have all the code:

1. **Test Locally:**
   ```bash
   npm run dev
   # Verify all features work
   ```

2. **Run Build:**
   ```bash
   npm run build
   # Check for errors
   ```

3. **Commit Changes:**
   ```bash
   git add .
   git commit -m "feat: Implement terrain optimization and JSON-driven configs for v2.0"
   ```

4. **Proceed with Release:**
   - Follow RELEASE_V2_PLAN.md
   - Create PR
   - Merge to main
   - GitHub Actions handles the rest

---

**Good luck with the implementation! 🎉**

*Remember: You can always come back to this prompt and adjust it based on your needs.*
