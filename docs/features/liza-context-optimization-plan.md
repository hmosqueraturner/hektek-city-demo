# Implementation Plan: LIZA Context Optimization

**Goal**: Reduce context from 58KB → ~5KB and skip API for simple navigation

---

## Change 1: Navigation Preprocessor (Skip API)

**File**: `useLizaChat.js`

Reactivate preprocessor for navigation ONLY:
```javascript
// Detect simple navigation commands
const NAVIGATION_PATTERNS = [
  /^(go\s*to|navigate\s*to|show\s*me|take\s*me\s*to)\s+(skills|experience|vision|projects|about|docs|blog)/i
];

function isSimpleNavigation(message) {
  return NAVIGATION_PATTERNS.some(p => p.test(message.trim()));
}
```

**Benefits**: Skip API call entirely for "go to Experience", "navigate to Skills"

---

## Change 2: Reduce System Prompt

**File**: `liza-prompts.js`

Current: 6KB with verbose theme examples
Proposed: 3KB - remove redundant examples, keep essential guidelines

---

## Change 3: Fix formatContext() Output

**File**: `liza-smart-context.js`

Current issue: formatContext returns JSON.stringify of full data
Fix: Return ultra-compact summary only

---

## Execution Order

1. [x] Analyze current architecture
2. [ ] Fix navigation preprocessor in useLizaChat.js
3. [ ] Trim LIZA_SYSTEM_PROMPT to 3KB
4. [ ] Verify formatContext generates compact output
5. [ ] Test: Context should be <10KB total
