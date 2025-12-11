# LIZA Image Assets - Generation Guide

## Contexto del Proyecto

**LIZA** es la AI assistant del proyecto HEKTEK City con:
- **Estética**: Cyberpunk, holográfica, neural networks
- **Colores primarios**: Cyan (#00F0FF), Azul oscuro (#0a0e1a)
- **Colores secundarios**: Púrpura, toques de rojo (#BB1111)
- **Estilo**: Futurista, tecnológico, femenino, profesional

---

## 🎨 Assets Necesarios

### 1. Chat Icon (Avatar Circular)

**Uso**: Avatar de LIZA en el chat interface  
**Tamaño**: 512x512px (cuadrado)  
**Formato**: PNG con transparencia

**Prompt para generación**:
```
Circular icon of LIZA AI assistant, holographic female face made of glowing cyan 
neural network nodes and connections, cyberpunk style, dark background, neon cyan 
and purple accents, professional clean design, 512x512px avatar icon, centered 
composition, "LIZA" text subtly integrated in glowing cyan, sharp details, 
high tech aesthetic
```

**Especificaciones técnicas**:
- Composición centrada
- Background oscuro (puede ser transparente)
- Face holográfico con network pattern
- Texto "LIZA" integrado sutilmente
- Colores: Cyan dominante, púrpura como acento

---

### 2. Chat Background Pattern

**Uso**: Fondo sutil del chat interface  
**Tamaño**: 1920x1080px (o pattern repetible)  
**Formato**: PNG

**Prompt para generación**:
```
Subtle dark background pattern for chat interface, abstract neural network 
connections in dark blue and cyan, very low opacity geometric grid pattern, 
cyberpunk technology aesthetic, dark (#0a0e1a) background with faint cyan 
(#00F0FF) glowing lines and nodes, seamless pattern, professional UI design, 
minimalist, not distracting, safe for text overlay
```

**Especificaciones técnicas**:
- **MUY sutil** - no debe distraer del contenido
- Background #0a0e1a (casi negro)
- Líneas/nodos en cyan con 10-20% opacidad
- Seamless/repetible (opcional)
- Safe para sobreponer texto blanco

---

### 3. Compact Logo (UI Small)

**Uso**: Logo pequeño para UI elements, badges, etc.  
**Tamaño**: 256x256px  
**Formato**: PNG con transparencia

**Prompt para generación**:
```
Compact square logo for LIZA AI, stylized "L" letter made of glowing cyan neural 
networks and circuit patterns, dark background, cyberpunk minimalist design, 
256x256px, professional tech logo, neon cyan (#00F0FF) primary color with purple 
accents, clean geometric design suitable for small UI elements
```

**Especificaciones técnicas**:
- Letra "L" estilizada
- Neural network pattern integrado
- Funcional a tamaños pequeños (32x32px)
- Alto contraste para legibilidad

---

### 4. Wide Banner (Horizontal)

**Uso**: Headers, hero sections, presentaciones  
**Tamaño**: 1200x300px (ratio 4:1)  
**Formato**: PNG

**Prompt para generación**:
```
Wide horizontal banner featuring LIZA AI branding, holographic neural network 
visualization flowing from left to right, dark cyberpunk background, glowing 
cyan and purple data streams, "LIZA" text in neon cyan on right side, 
professional tech banner, 1200x300px aspect ratio, HEKTEK style aesthetic, 
clean modern design
```

**Especificaciones técnicas**:
- Flow horizontal (izquierda → derecha)
- Texto "LIZA" prominente en lado derecho
- Neural networks como elemento visual principal
- Background oscuro con gradiente sutil

**✅ GENERADO**: 
![LIZA Banner](C:/Users/hector/.gemini/antigravity/brain/b31bb821-f791-4b02-855e-0f84ab74ba82/liza_banner_wide_1764688515225.png)

---

### 5. Orb Icon (Loading/Status)

**Uso**: Indicador de estado, loading spinner, animated icon  
**Tamaño**: 512x512px  
**Formato**: PNG con transparencia

**Prompt para generación**:
```
Glowing cyan orb representing LIZA AI consciousness, sphere made of interconnected 
neural network nodes, holographic translucent effect, dark background, pulsing 
energy core, cyberpunk style, neon cyan (#00F0FF) and deep blue colors, suitable 
as animated icon or loading indicator, 512x512px, centered composition, mystical 
tech aesthetic
```

**Especificaciones técnicas**:
- Esfera perfecta, centrada
- Efecto holográfico/translúcido
- Bright core (podría animarse)
- Background transparente

---

## 🎯 Paleta de Colores LIZA

```css
/* Primary */
--liza-cyan: #00F0FF;
--liza-dark: #0a0e1a;

/* Secondary */
--liza-purple: #9333ea;
--liza-blue: #4299e1;

/* Accents */
--hektek-red: #BB1111;
```

---

## 📦 Estructura de Archivos Sugerida

```
/public/img/liza/
├── liza-chat-icon.png          (512x512)
├── liza-chat-background.png    (1920x1080)
├── liza-logo-compact.png       (256x256)
├── liza-banner-wide.png        (1200x300) ✅
├── liza-orb-icon.png           (512x512)
└── variants/
    ├── liza-icon-animated.gif
    ├── liza-banner-alt.png
    └── ...
```

---

## 🛠️ Herramientas Recomendadas

1. **Midjourney** - Mejor para estilo cyberpunk consistent
2. **DALL-E 3** - Buena versatilidad, fácil acceso
3. **Stable Diffusion** - Control total, local
4. **Leonardo AI** - Buen balance calidad/facilidad

---

## 💡 Tips de Generación

1. **Consistencia**: Usa el mismo prompt base para todas las variantes
2. **Iteración**: Genera múltiples versiones, elige la mejor
3. **Edición**: Usa Photoshop/GIMP para ajustes finales (opacidad, recortes)
4. **Formato**: Siempre PNG para transparencia
5. **Tamaño**: Genera en alta resolución, reduce después

---

## 🎬 Uso en el Proyecto

### Chat Icon
```jsx
<img src="/img/liza/liza-chat-icon.png" alt="LIZA" />
```

### Chat Background
```css
.chat-container {
  background-image: url('/img/liza/liza-chat-background.png');
  background-size: cover;
  background-position: center;
}
```

### Compact Logo
```jsx
<AssetViewer 
  mode="single" 
  imageUrl="liza/liza-logo-compact.png" 
/>
```

### Banner
```jsx
<AssetViewer 
  mode="single" 
  imageUrl="liza/liza-banner-wide.png"
  height="300px"
/>
```

---

## 📝 Notas

- Todas las imágenes deben mantener el estilo cyberpunk consistent
- Evitar saturación excesiva - mantener elegante y profesional
- El cyan debe ser el color dominante, no el púrpura
- Background siempre oscuro para mantener legibilidad
- Considerar versiones animadas (.gif o .webp) para efectos especiales

---

**¿Necesitas ayuda generando estas imágenes?** Puedo:
1. Refinar los prompts según tus necesidades
2. Sugerir variantes adicionales
3. Ayudar con la integración en el código
