# 📖 User Guides

Step-by-step guides and tutorials for using HekTek City's tools and features.

## 📚 Available Guides

### [Testing Material Variants](./testing-variants.md)
Complete guide for testing the Material Variants system.

**What you'll learn:**
- How to access the testing interface
- Testing building loading
- Testing theme switching
- Performance testing
- Troubleshooting common issues

**Prerequisites:**
- Dev server running (`npm run dev`)
- Buildings exported from Blender
- Basic understanding of 3D navigation

**Time:** ~15-20 minutes

---

### [Quick Reference](./quick-reference.md)
Quick command reference for common operations.

**What you'll find:**
- NPM scripts and their usage
- File paths and locations
- Common commands
- Keyboard shortcuts
- Troubleshooting quick fixes

**Use case:** Quick lookup when you know what you want to do

---

## 🚀 Getting Started Guides

### New to HekTek City?

Follow these guides in order:

1. **Installation**
   - See main [README.md](../../README.md#quick-start)

2. **Configuration**
   - See main [README.md](../../README.md#configuration)

3. **First Run**
   ```bash
   npm run dev
   # Visit http://localhost:5173
   ```

4. **Explore Features**
   - [Theme System](../features/theme-system.md)
   - [Material Variants](../pipeline/QUICK_START_AUTOMATED.md)

---

## 🔧 Development Guides

### Working with the Pipeline

1. **[Material Variants Pipeline](../pipeline/QUICK_START_AUTOMATED.md)**
   - Fastest way to process all buildings
   - 5-minute quick start

2. **[Automated Workflow](../pipeline/WORKFLOW_AUTOMATED.md)**
   - Complete workflow documentation
   - Step-by-step instructions

3. **[Testing Variants](./testing-variants.md)**
   - How to test your changes
   - Expected results
   - Troubleshooting

---

## 🎨 Blender Guides

### Using the Material Roles Addon

1. **Installation**
   - See [Blender Addon README](../../tools/blender/addons/README.md)

2. **Usage**
   - See [Blender Tools Guide](../../tools/blender/README.md)

3. **Exporting Buildings**
   - See [Automated Workflow](../pipeline/WORKFLOW_AUTOMATED.md#paso-2)

---

## 🐛 Troubleshooting Guides

### Common Issues

**Models not loading:**
→ [Testing Guide - Problem 1](./testing-variants.md#problema-1-edificio-no-carga--pantalla-negra)

**Themes not changing:**
→ [Testing Guide - Problem 3](./testing-variants.md#problema-3-themes-no-cambian)

**Export errors in Blender:**
→ [Blender Addon - Common Issues](../../tools/blender/addons/README.md#common-issues)

**Pipeline script errors:**
→ [GLTF Pipeline - Troubleshooting](../../tools/gltf/README.md#troubleshooting)

---

## 📋 Guide Templates

### Creating a New Guide

Use this structure for new user guides:

```markdown
# Guide Title

Brief description (1-2 sentences).

## Prerequisites

- Requirement 1
- Requirement 2

## Estimated Time

X minutes / hours

## Step 1: [Action]

Instructions...

### Expected Result

What should happen...

## Step 2: [Action]

Instructions...

### Expected Result

What should happen...

## Troubleshooting

### Issue 1
**Problem:** Description
**Solution:** Steps to fix

## Next Steps

- What to do after completing this guide
- Related guides to check out

## Related Documentation

- [Link 1](...)
- [Link 2](...)
```

---

## 🎯 Guide Types

### Quick Start Guides
- Goal: Get user productive in < 10 minutes
- Focus: Essential steps only
- Example: [QUICK_START_AUTOMATED.md](../pipeline/QUICK_START_AUTOMATED.md)

### Tutorial Guides
- Goal: Teach a specific skill or feature
- Focus: Step-by-step with explanations
- Example: [testing-variants.md](./testing-variants.md)

### Reference Guides
- Goal: Quick lookup of commands/options
- Focus: Comprehensive, organized lists
- Example: [quick-reference.md](./quick-reference.md)

### Troubleshooting Guides
- Goal: Solve specific problems
- Focus: Problem → Solution format
- Example: Troubleshooting sections in various docs

---

## 📚 Learning Paths

### Path 1: Basic User

1. Read [README.md](../../README.md)
2. Follow [Quick Start](../../README.md#quick-start)
3. Review [Quick Reference](./quick-reference.md)
4. Explore features from UI

**Goal:** Use the application effectively

---

### Path 2: Content Creator

1. Complete Path 1
2. Learn [Theme System](../features/theme-system.md)
3. Understand [Asset Management](../../README.md#3d-models--asset-pipeline)

**Goal:** Create and manage custom content

---

### Path 3: Pipeline Developer

1. Complete Path 1
2. Study [Pipeline Overview](../technical/pipeline-overview.md)
3. Follow [Blender Tools Guide](../../tools/blender/README.md)
4. Learn [GLTF Pipeline](../../tools/gltf/README.md)
5. Practice [Automated Workflow](../pipeline/WORKFLOW_AUTOMATED.md)

**Goal:** Contribute to the material variants pipeline

---

### Path 4: Application Developer

1. Complete Path 1
2. Review [Implementation Summary](../technical/implementation-summary.md)
3. Study [Architecture](../../README.md#architecture)
4. Read [Technical Documentation](../technical/)

**Goal:** Contribute to the React application

---

## 🔗 External Learning Resources

### Blender
- [Blender Fundamentals](https://www.blender.org/support/tutorials/)
- [Blender Python API Quickstart](https://docs.blender.org/api/current/info_quickstart.html)

### glTF
- [glTF Tutorial Series](https://github.com/KhronosGroup/glTF-Tutorials)
- [glTF Viewer](https://gltf-viewer.donmccurdy.com/) - Test your GLBs

### React Three Fiber
- [R3F Getting Started](https://docs.pmnd.rs/react-three-fiber/getting-started/introduction)
- [drei Documentation](https://github.com/pmndrs/drei)

### Three.js
- [Three.js Journey](https://threejs-journey.com/)
- [Three.js Fundamentals](https://threejs.org/manual/)

---

## 🤝 Contributing Guides

Help improve the documentation:

1. **Found a mistake?** Submit a fix
2. **Something unclear?** Ask for clarification
3. **Missing a guide?** Propose a new one
4. **Have tips?** Share them in discussions

---

## 📝 Documentation Standards

All guides should:
- ✅ Be accurate and tested
- ✅ Include clear prerequisites
- ✅ Have step-by-step instructions
- ✅ Show expected results
- ✅ Include troubleshooting
- ✅ Link to related documentation

---

**Last Updated:** 2025-11-18
