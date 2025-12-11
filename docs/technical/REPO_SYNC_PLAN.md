# Sync Plan: Hektek City -> Hektek City Demo

## Goal
Update the public `hektek-city-demo` repository with the overhauled documentation from the private `hektek-city` repository.

## Strategy
1.  **Direct Copy**: Use Powershell to recursively copy documentation folders (`architecture`, `features`, `blog`, `technical`).
2.  **Root Overwrite**:
    -   `hektek-city/README.md` -> `hektek-city-demo/README.md` (Main Landing Page).
    -   `hektek-city/docs/README.md` -> `hektek-city-demo/docs.md` (Technical Deep Dive Index).
3.  **Docsify Configuration**:
    -   Create `_sidebar.md` in `hektek-city-demo` to reflect the new structure.
    -   Ensure `index.html` has `loadSidebar: true`.

## Execution Steps

### 1. File Sync
```powershell
# Copy Directories
Copy-Item -Path "d:\code\portfolio\hektek-city\docs\architecture" -Destination "d:\code\portfolio\hektek-city-demo\architecture" -Recurse -Force
Copy-Item -Path "d:\code\portfolio\hektek-city\docs\features" -Destination "d:\code\portfolio\hektek-city-demo\features" -Recurse -Force
Copy-Item -Path "d:\code\portfolio\hektek-city\docs\blog" -Destination "d:\code\portfolio\hektek-city-demo\blog" -Recurse -Force
Copy-Item -Path "d:\code\portfolio\hektek-city\docs\technical" -Destination "d:\code\portfolio\hektek-city-demo\technical" -Recurse -Force

# Copy Files
Copy-Item -Path "d:\code\portfolio\hektek-city\README.md" -Destination "d:\code\portfolio\hektek-city-demo\README.md" -Force
Copy-Item -Path "d:\code\portfolio\hektek-city\docs\README.md" -Destination "d:\code\portfolio\hektek-city-demo\docs.md" -Force
```

### 2. Sidebar Creation
Create `d:\code\portfolio\hektek-city-demo\_sidebar.md`:
```markdown
* [Home](/)
* [Technical Deep Dive](docs.md)

* Architecture
  * [System Overview](architecture/ARCHITECTURE.md)
  * [System Diagrams](architecture/SYSTEM_DIAGRAMS.md)
  * [Deployment](architecture/DEPLOYMENT.md)

* Features
  * [LIZA Agent](features/Liza/LIZA-FEATURE.md)
  * [Neuro-Architect](features/NeuroArchitect/MATERIAL_ANALYSIS.md)
  * [Branding](features/BRANDING.md)

* Blog
  * [Latest Posts](blog/)
  * [Publishing Workflow](blog/PUBLISHING_WORKFLOW.md)

* Technical
  * [AI Protocol](technical/AI_WORKFLOW_PROTOCOL.md)
```

## Verification
-   Manual: Check if files exist in target.
-   I cannot start a server in the separate repo, but file existence confirms success.
