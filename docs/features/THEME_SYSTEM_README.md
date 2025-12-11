# 🎨 Features Documentation

Documentation for HekTek City's key features and systems.

## 📚 Available Documentation

### [Theme System](./theme-system.md)
Complete guide to the multi-theme environment system.

**Topics covered:**
- 7 available themes (Original, Cyberpunk, Mars, Pandora, etc.)
- Theme architecture and configuration
- Cache optimization
- Usage examples
- Theme switching implementation

**Status:** ✅ Production-ready

---

### [Decorative Models Guide](./decoratives-guide.md)
Guide for implementing decorative models in themes.

**Topics covered:**
- Decorative model system overview
- Theme-specific decoratives
- Model specifications
- Implementation patterns

**Status:** ⚠️ Experimental (not production-enabled)

**Note:** This feature exists in the codebase but is not currently enabled in production due to performance considerations. It may be replaced with an animation-based approach using the Material Variants architecture.

---

## 🆕 New Features

### Runtime Materials System ⭐ NEW
Modify GLB materials in real-time without pipelines or KHR_materials_variants.

**Current Status:**
- ✅ Complete and production-ready
- ✅ Zero pipeline required
- ✅ Pure React/Three.js solution
- ✅ Perfect for story-driven games

**Documentation:**
- **[Quick Start Guide](./RUNTIME_MATERIALS_GUIDE.md)** ← Start here!
- [Implementation Summary](./IMPLEMENTATION_SUMMARY.md)
- [Technical Plan](./PLAN_REAL_SOLUTION.md)

**Use this instead of** the old KHR_materials_variants pipeline for dynamic material changes.

---

### Hybrid Quality Strategy (HQS)
Intelligent asset loading based on device capabilities.

**Status:** ✅ Production-ready

**Documentation:** See main [README.md](../../README.md#performance)

---

## 📋 Feature Status Reference

| Feature | Status | Production | Documentation | Version |
|---------|--------|------------|---------------|---------|
| **Runtime Materials** ⭐ | ✅ Complete | ✅ Ready | [RUNTIME_MATERIALS_GUIDE.md](./RUNTIME_MATERIALS_GUIDE.md) | v2.0 |
| **Navigable Buildings** ⭐ | ✅ Complete | ✅ Ready | Component docs in code | v2.0 |
| **3D Text Labels** ⭐ | ✅ Complete | ✅ Ready | Component docs in code | v2.0 |
| **Custom Hooks** ⭐ | ✅ Complete | ✅ Ready | [RUNTIME_MATERIALS_GUIDE.md](./RUNTIME_MATERIALS_GUIDE.md) | v2.0 |
| **Theme System** | ✅ Complete | ✅ Enabled | [theme-system.md](./theme-system.md) | v1.0 |
| **Decorative Models** | ⚠️ Experimental | ❌ Disabled | [decoratives-guide.md](./decoratives-guide.md) | v1.x |
| **HQS** | ✅ Complete | ✅ Enabled | [README.md](../../README.md) | v1.0 |
| **HDR Environments** | ✅ Complete | ✅ Enabled | [theme-system.md](./theme-system.md) | v1.0 |
| **Camera Navigation** | ✅ Complete | ✅ Enabled | [README.md](../../README.md) | v1.0 |
| **Mobile Support** | ✅ Complete | ✅ Enabled | [README.md](../../README.md) | v1.0 |

---

## 🎯 Feature Request Process

Have an idea for a new feature?

1. **Check existing documentation** to ensure it doesn't already exist
2. **Review roadmap** in main [README.md](../../README.md#roadmap)
3. **Consider technical feasibility** (performance, compatibility)
4. **Document the proposal** with use cases and examples
5. **Discuss with the team** before implementation

---

## 📝 Adding Feature Documentation

When documenting a new feature:

### Required Sections

1. **Overview** - What is this feature?
2. **Status** - Production-ready? Experimental? Deprecated?
3. **Configuration** - How to configure it
4. **Usage** - Code examples and patterns
5. **Performance** - Impact on performance
6. **Troubleshooting** - Common issues and fixes
7. **Related Documentation** - Links to related docs

### Example Structure

```markdown
# Feature Name

Brief description of the feature.

## Status

- **Development:** Complete / In Progress / Planned
- **Production:** Enabled / Disabled / Testing
- **Version:** 1.0.0

## Overview

Detailed explanation...

## Configuration

Configuration options...

## Usage

Code examples...

## Performance

Performance considerations...

## Troubleshooting

Common issues...

## Related Documentation

- [Link 1](...)
- [Link 2](...)
```

---

## 🔗 Related Documentation

### Development
- [Tools Overview](../../tools/README.md)
- [Pipeline Workflow](../pipeline/WORKFLOW_AUTOMATED.md)
- [Implementation Summary](../technical/implementation-summary.md)

### Guides
- [Quick Reference](../guides/quick-reference.md)
- [Testing Guide](../guides/testing-variants.md)

---

**Last Updated:** 2025-11-18
