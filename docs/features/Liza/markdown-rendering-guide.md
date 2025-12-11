# Markdown Rendering System - User Guide

## Quick Start

### For Content Creators

**Want to add a new blog post with full markdown article?**

1. **Create your markdown file**:
   ```bash
   # Create in public/blog/
   touch public/blog/my-awesome-post.md
   ```

2. **Write your content**:
   ```markdown
   # My Awesome Post
   
   ## Introduction
   
   Your content here with **bold**, *italic*, `code`.
   
   ### Code Example
   
   \`\`\`javascript
   const example = () => {
     console.log("Hello!");
   };
   \`\`\`
   ```

3. **Add to blog.json**:
   ```json
   {
     "id": "my-awesome-post",
     "title": "My Awesome Post",
     "date": "2025-11-30",
     "category": "Tutorial",
     "tags": ["React", "Tutorial"],
     "summary": "Learn something awesome!",
     "contentUrl": "/blog/my-awesome-post.md"
   }
   ```

4. **Done!** Visit `/blog` and click "Read Full Article"

---

## Markdown Features

### Headings

```markdown
# H1 Heading (Red glow, bottom border)
## H2 Heading (Blue glow, left border)
### H3 Heading (Red glow)
#### H4 Heading (Blue)
```

**Renders as**: Large headings with cyberpunk glow effects

---

### Text Formatting

```markdown
**Bold text** - Renders in neon red
*Italic text* - Renders in neon blue
`inline code` - Blue background with cyan text
```

---

### Code Blocks

**Syntax highlighting supported**:

````markdown
```javascript
function greet(name) {
  return `Hello, ${name}!`;
}
```

```python
def greet(name):
    return f"Hello, {name}!"
```

```bash
npm install react-markdown
```
````

**Supported languages**: JavaScript, TypeScript, Python, Bash, JSON, HTML, CSS, and more

---

### Links

```markdown
[External Link](https://example.com)
[Internal Link](/about)
```

**Features**:
- Opens in new tab
- Neon blue color
- Glow on hover
- Underline effect

---

### Tables

```markdown
| Feature | Status | Priority |
|---------|--------|----------|
| Rendering | ✅ Done | High |
| Styling | ✅ Done | High |
| Testing | 🚧 In Progress | Medium |
```

**Styling**:
- Glassmorphism background
- Neon blue borders
- Header with cyan tint
- Responsive on mobile

---

### Lists

**Unordered**:
```markdown
- Item 1
- Item 2
  - Nested item
- Item 3
```

**Ordered**:
```markdown
1. First
2. Second
3. Third
```

**Task Lists** (GitHub Flavored):
```markdown
- [x] Completed task
- [ ] Pending task
- [ ] Another task
```

---

### Blockquotes

```markdown
> This is a blockquote
> with multiple lines
```

**Styling**: Left red neon border with dark background

---

### Images

```markdown
![Alt text](https://example.com/image.jpg)
![Local image](/assets/screenshot.png)
```

**Features**:
- Rounded borders
- Cyan glow effect
- Responsive sizing

---

### Horizontal Rules

```markdown
---
```

**Renders as**: Glowing red line

---

## Advanced Usage

### External GitHub Content

**Load README from GitHub**:

```json
{
  "contentUrl": "https://raw.githubusercontent.com/username/repo/main/README.md"
}
```

**Load docs from GitHub Pages**:

```json
{
  "contentUrl": "https://username.github.io/repo/docs/guide.md"
}
```

**Requirements**:
- URL must be publicly accessible
- CORS headers must allow cross-origin requests
- Use raw GitHub URLs (not web interface)

---

### Embedding HTML

```markdown
<div align="center">
  <img src="logo.png" width="200" />
  <h3>Custom HTML</h3>
</div>
```

**Note**: HTML is sanitized for security

---

### Mermaid Diagrams

**Not yet supported** - Coming in future version

Workaround: Use image of diagram:
```markdown
![Architecture](diagram.png)
```

---

## Best Practices

### 1. Structure

```markdown
# Main Title (One per document)

Brief introduction paragraph.

## Section 1

Content...

### Subsection

Detailed content...

## Section 2

More content...
```

### 2. Code Blocks

**Always specify language**:
```markdown
// ❌ Bad
\`\`\`
code here
\`\`\`

// ✅ Good
\`\`\`javascript
code here
\`\`\`
```

### 3. Links

**Use descriptive text**:
```markdown
// ❌ Bad
Click [here](url)

// ✅ Good
View the [implementation guide](url)
```

### 4. Tables

**Keep tables simple**:
- Max 5-6 columns
- Use abbreviations for headers
- Consider lists for complex data

### 5. Images

**Optimize before using**:
- Max width: 1200px
- WebP format preferred
- Descriptive alt text

---

## Examples

### Technical Tutorial

```markdown
# Building a React Component

## Overview

Learn how to build a reusable component.

## Installation

\`\`\`bash
npm install react
\`\`\`

## Implementation

\`\`\`javascript
import React from 'react';

export default function MyComponent() {
  return <div>Hello!</div>;
}
\`\`\`

## Usage

\`\`\`javascript
import MyComponent from './MyComponent';

function App() {
  return <MyComponent />;
}
\`\`\`
```

### Release Notes

```markdown
# v5.0.0 Release

## New Features

- **AI Chat**: Gemini 2.0 integration
- **Theme Generator**: AI-powered themes
- **Voice Input**: Web Speech API

## Technical Details

| Feature | Technology | Status |
|---------|-----------|--------|
| Chat | Gemini API | ✅ Live |
| Themes | Custom Engine | ✅ Live |
| Voice | Web Speech | ✅ Live |

## Migration Guide

\`\`\`javascript
// Old way
import { OldComponent } from 'old';

// New way
import { NewComponent } from 'new';
\`\`\`
```

---

## Tips & Tricks

### 1. Long Articles

**Add navigation**:
```markdown
## Table of Contents

- [Introduction](#introduction)
- [Features](#features)
- [Usage](#usage)
```

### 2. Syntax Highlighting

**Test languages**:
- `javascript`, `typescript`
- `python`, `ruby`, `go`
- `bash`, `shell`
- `json`, `yaml`
- `html`, `css`, `scss`
- `sql`, `graphql`

### 3. Special Characters

**Escape if needed**:
```markdown
Use \* for literal asterisk
Use \` for literal backtick
Use \\ for backslash
```

### 4. Line Breaks

**Two spaces at end of line**:
```markdown
Line 1  
Line 2  
Line 3
```

Or use `<br>`:
```markdown
Line 1<br>
Line 2
```

---

## Keyboard Shortcuts

**In modal viewer**:
- `Esc` - Close modal
- `Scroll` - Navigate content

---

## FAQ

**Q: Can I use emojis?**  
A: Yes! 🚀 ✅ 💻 All work

**Q: Max article length?**  
A: No limit, but 5000-7000 words recommended

**Q: Can I embed videos?**  
A: Use `videoUrl` in blog.json for YouTube videos

**Q: How to add images?**  
A: Place in `public/assets/` and reference: `![](assets/image.png)`

**Q: Can I link to other posts?**  
A: Yes, use: `[Post Title](/blog#post-id)`

---

## Support

**Issues**: Check [troubleshooting guide](./markdown-rendering-implementation.md#troubleshooting)

**Examples**: See `public/blog/v5-liza-awakening.md`

**Updates**: Follow changelog in implementation guide
