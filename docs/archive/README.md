# 🗄️ Documentation Archive

Historical and outdated documentation kept for reference.

## ⚠️ Important Notice

**The documentation in this directory is OUTDATED or DEPRECATED.**

Do not use this documentation for:
- Current development
- Learning the current system
- Implementation guidance

Use this documentation for:
- Historical reference
- Understanding past decisions
- Migration context

---

## 📂 Archived Documentation

### [Decoratives Migration Guide v1](./decoratives-migration-v1.md)
**Original:** `DECORATIVES_MIGRATION_GUIDE.md`
**Date Archived:** 2025-11-18
**Reason:** Feature not enabled in production, may be replaced

**Status:** ⚠️ Experimental feature documentation

**Current Alternative:**
- Feature exists but disabled due to performance
- May be replaced with animation-based approach
- See current docs: [features/decoratives-guide.md](../features/decoratives-guide.md)

---

## 📋 Document Status Reference

| Document | Original Name | Archived Date | Reason | Current Doc |
|----------|--------------|---------------|---------|-------------|
| decoratives-migration-v1.md | DECORATIVES_MIGRATION_GUIDE.md | 2025-11-18 | Feature experimental | [features/decoratives-guide.md](../features/decoratives-guide.md) |

---

## 🔍 Finding Current Documentation

If you landed here looking for current documentation:

### Material Variants Pipeline
**Old location:** Various scattered files
**Current location:** [docs/pipeline/](../pipeline/)

**Key docs:**
- [Quick Start](../pipeline/QUICK_START_AUTOMATED.md)
- [Workflow](../pipeline/WORKFLOW_AUTOMATED.md)

---

### Theme System
**Old location:** `THEME_SYSTEM_CHANGES.md` (root)
**Current location:** [docs/features/theme-system.md](../features/theme-system.md)

---

### Testing
**Old location:** `TEST_VARIANTS.md` (root)
**Current location:** [docs/guides/testing-variants.md](../guides/testing-variants.md)

---

### Pipeline Overview
**Old location:** `PIPELINE.md` (root)
**Current location:** [docs/technical/pipeline-overview.md](../technical/pipeline-overview.md)

---

### Implementation Details
**Old location:** `IMPLEMENTATION_SUMMARY.md` (root)
**Current location:** [docs/technical/implementation-summary.md](../technical/implementation-summary.md)

---

## 📚 Complete Documentation Index

For current, up-to-date documentation, see:

**Main Documentation:**
- [Root README](../../README.md)
- [Documentation Index](../README.md)

**By Category:**
- **Features:** [docs/features/](../features/)
- **Guides:** [docs/guides/](../guides/)
- **Technical:** [docs/technical/](../technical/)
- **Pipeline:** [docs/pipeline/](../pipeline/)
- **Tools:** [tools/](../../tools/)

---

## 🔄 Archive Management

### When to Archive

Document should be archived when:
- Feature is deprecated or significantly changed
- Information is outdated but may have historical value
- Workflow has been completely replaced
- Document no longer reflects current implementation

### When to Delete

Archived documents may be deleted when:
- No historical value remains
- Information is completely irrelevant
- Document has been archived for > 1 year with no references

### Archive Process

1. **Move document** to `docs/archive/`
2. **Add version suffix** if applicable (e.g., `-v1.md`)
3. **Update this README** with entry in table
4. **Create redirect** in original location (if applicable)
5. **Update related docs** to point to current version

---

## 📝 Archive Log

### 2025-11-18 - Initial Archive Setup

**Actions:**
- Created archive directory
- Moved `DECORATIVES_MIGRATION_GUIDE.md` → `decoratives-migration-v1.md`
- Established archive process
- Created this README

**Reason:**
- Part of documentation reorganization
- Consolidating all docs under `/docs`
- Separating active from historical documentation

---

## 🔗 Related Documentation

### Active Documentation
- [Documentation Index](../README.md)
- [Features](../features/)
- [Guides](../guides/)
- [Technical](../technical/)
- [Pipeline](../pipeline/)

### Legacy Code
- [tools/legacy/](../../tools/legacy/) - Archived code

---

**Last Updated:** 2025-11-18
**Maintained by:** HekTek City Team

**Note:** This archive is reviewed periodically. Documents with no historical value may be removed.
