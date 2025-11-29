# 📚 HekTek City - Documentation

Complete documentation for the HekTek City project.

## 🗂️ Documentation Structure

```
docs/
├── features/      # Feature-specific documentation
├── guides/        # Usage guides and tutorials
├── technical/     # Technical architecture and implementation
├── pipeline/      # Material Variants Pipeline docs
├── archive/       # Outdated/historical documentation
└── assets/        # Screenshots, diagrams, etc.
```

---

## 🚀 Quick Links

### Getting Started
- **Main README:** [../README.md](../README.md)
- **Quick Start:** [pipeline/QUICK_START_AUTOMATED.md](pipeline/QUICK_START_AUTOMATED.md)
- **Quick Reference:** [guides/quick-reference.md](guides/quick-reference.md)

### Features
- **Theme System:** [features/theme-system.md](features/theme-system.md)
- **Decorative Models:** [features/decoratives-guide.md](features/decoratives-guide.md)

### Development
- **Pipeline Workflow:** [pipeline/WORKFLOW_AUTOMATED.md](pipeline/WORKFLOW_AUTOMATED.md)
- **Testing Variants:** [guides/testing-variants.md](guides/testing-variants.md)
- **Implementation Details:** [technical/implementation-summary.md](technical/implementation-summary.md)

### Technical
- **Architecture Overview:** [technical/pipeline-overview.md](technical/pipeline-overview.md)
- **Executive Summary:** [technical/executive-summary.md](technical/executive-summary.md)

---

## 📂 Directory Guides

### 🎨 [features/](./features/)
Documentation for specific features and systems:
- Theme switching system
- Material variants
- Decorative models (experimental)
- HDR environments

### 📖 [guides/](./guides/)
Step-by-step guides and tutorials:
- Testing procedures
- Quick reference commands
- Troubleshooting
- Best practices

### 🏗️ [technical/](./technical/)
Technical architecture and implementation:
- System architecture
- Implementation details
- Performance optimization
- API documentation

### 🔧 [pipeline/](./pipeline/)
Material Variants Pipeline documentation:
- Automated workflow
- Manual workflow
- Configuration
- Changelog

### 🗄️ [archive/](./archive/)
Historical/outdated documentation:
- Previous versions
- Deprecated features
- Migration guides (old)

---

## 📋 Documentation by Topic

### Material Variants System

**Overview:**
- [Pipeline Overview](technical/pipeline-overview.md)
- [Quick Start](pipeline/QUICK_START_AUTOMATED.md)

**Workflow:**
- [Automated Workflow](pipeline/WORKFLOW_AUTOMATED.md)
- [Testing Guide](guides/testing-variants.md)

**Reference:**
- [Project Structure](pipeline/PROJECT_STRUCTURE.md)
- [Changelog](pipeline/CHANGELOG_AUTOMATION.md)

---

### Theme System

**Main Guide:**
- [Theme System Documentation](features/theme-system.md)

**Related:**
- [Quick Reference](guides/quick-reference.md)
- [Implementation Summary](technical/implementation-summary.md)

---

### Decorative Models

**Current Status:** ⚠️ Experimental (not production-ready)

**Documentation:**
- [Decoratives Guide](features/decoratives-guide.md)
- [Migration Guide (Archived)](archive/decoratives-migration-v1.md)

---

### Development & Tools

**Tools Documentation:**
- [Tools Overview](../tools/README.md)
- [Blender Tools](../tools/blender/README.md)
- [GLTF Pipeline](../tools/gltf/README.md)
- [Automated Pipeline](../tools/pipeline/README.md)

---

## 🔍 Finding What You Need

### I want to...

**...get started quickly**
→ [Quick Start Guide](pipeline/QUICK_START_AUTOMATED.md)

**...understand the theme system**
→ [Theme System Documentation](features/theme-system.md)

**...process buildings with material variants**
→ [Automated Workflow](pipeline/WORKFLOW_AUTOMATED.md)

**...test material variants in React**
→ [Testing Guide](guides/testing-variants.md)

**...understand the architecture**
→ [Pipeline Overview](technical/pipeline-overview.md)

**...find specific commands**
→ [Quick Reference](guides/quick-reference.md)

**...troubleshoot an issue**
→ [Testing Guide - Troubleshooting](guides/testing-variants.md#troubleshooting)

**...learn about implementation details**
→ [Implementation Summary](technical/implementation-summary.md)

---

## 📝 Documentation Standards

### File Naming
- Use lowercase with hyphens: `theme-system.md`
- Be descriptive: `testing-variants.md` not `test.md`
- Include version if archived: `decoratives-migration-v1.md`

### Structure
All documentation should include:
- Clear title and purpose
- Table of contents (for long docs)
- Code examples (where applicable)
- Related documentation links
- Last updated date

### Links
- Use relative paths: `[link](../guides/quick-reference.md)`
- Always verify links work
- Update links when moving files

---

## 🔄 Keeping Documentation Updated

### When to Update

**Add new documentation when:**
- Adding a new feature
- Implementing a new workflow
- Creating new tools or scripts

**Update existing documentation when:**
- Changing functionality
- Fixing bugs that affect usage
- Improving processes
- Adding new examples

**Archive documentation when:**
- Feature is deprecated
- Workflow is replaced
- Information is outdated but valuable for reference

---

## 🤝 Contributing to Documentation

### Documentation Checklist

- [ ] Clear and concise writing
- [ ] Code examples tested and working
- [ ] Screenshots/diagrams where helpful
- [ ] Related docs linked
- [ ] Spelling and grammar checked
- [ ] Follows project structure

### Where to Add Documentation

| Type | Location | Example |
|------|----------|---------|
| Feature docs | `docs/features/` | `new-feature.md` |
| User guides | `docs/guides/` | `how-to-use-x.md` |
| Technical specs | `docs/technical/` | `api-reference.md` |
| Pipeline docs | `docs/pipeline/` | `new-workflow.md` |
| Tool docs | `tools/*/README.md` | Tool-specific READMEs |

---

## 📚 External Resources

### Blender
- [Blender 4.5 Manual](https://docs.blender.org/manual/en/4.5/)
- [Blender Python API](https://docs.blender.org/api/current/)

### glTF
- [glTF 2.0 Specification](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html)
- [KHR_materials_variants](https://github.com/KhronosGroup/glTF/tree/main/extensions/2.0/Khronos/KHR_materials_variants)

### React Three Fiber
- [R3F Documentation](https://docs.pmnd.rs/react-three-fiber)
- [drei Helpers](https://github.com/pmndrs/drei)

### Three.js
- [Three.js Documentation](https://threejs.org/docs/)
- [Three.js Examples](https://threejs.org/examples/)

---

**Last Updated:** 2025-11-18
**Maintained by:** HekTek City Team
