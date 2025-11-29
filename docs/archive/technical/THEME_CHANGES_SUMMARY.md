# Resumen de Cambios en Themes - visual-states.json

## Directrices Aplicadas

### 1. **Original**
- **Mapa**: Full resolución, natural, luz de día
- **Materiales estructurales**: Colores originales (sin cambios)
- **Materiales de detalle**: Colores originales (sin cambios)

### 2. **Cyberpunk**
- **Mapa**:
  - Terreno: `#1a1a2e` (azul oscuro casi negro - nocturno)
  - Emisión: `#ff006e` (rosa/rojo sutil, intensity 0.2)
  - Water: `#0a3a5a` (azul oscuro) con emisión cyan `#00ffff` (intensity 0.5)

- **Materiales estructurales** (metales/base/build):
  - Color: `#ff0033` (ROJO RGB NEON)
  - Emisión: `#ff0000` (rojo puro, intensity 0.7)
  - Metalness: 0.95, Roughness: 0.1 (muy brillante)

- **Materiales de detalle** (glass/glaze/crystal):
  - Color: `#0099ff` (AZUL RGB NEON)
  - Emisión: `#00ffff` (cyan neon, intensity 0.75)
  - Roughness: 0.05 (muy liso)
  - Opacity: 0.65 (semi-transparente)

### 3. **Mars**
- **Mapa**:
  - Terreno: `#8a5a3a` (naranja-marrón árido)
  - Emisión: `#ff4400` (rojizo - luz marciana, intensity 0.15)
  - Water: `#6a4a3a` (marrón rojizo - agua contaminada) con emisión `#ff4400`

- **Materiales estructurales** (metales/base/build):
  - Color: `#0a0a0a` (NEGRO CASI PURO)
  - Emisión: `#0066ff` (AZUL NEON, intensity 0.4)
  - Metalness: 0.95, Roughness: 0.1 (MUY BRILLANTE Y REFLECTANTE)

- **Materiales de detalle** (glass/glaze/crystal):
  - Color: `#0088ff` (AZUL NEON)
  - Emisión: `#00ccff` (cyan brillante, intensity 0.7)
  - Roughness: 0.08 (muy liso)
  - Opacity: 0.55 (semi-transparente)

### 4. **Pandora**
- **Mapa**: (Ya estaba correcto, sin cambios)
  - Terreno: `#2a4a4a` (verde-azul oscuro natural)
  - Emisión: `#1a3a5a` (azul muy sutil, intensity 0.2)
  - Water: `#3a6a8a` (azul natural) con emisión `#2a5a9a`

- **Materiales estructurales** (metales/base/build):
  - Colores: `#2d1b4e`, `#3d2858`, `#4a1f6f` (VIOLETA OSCURO)
  - Emisión: `#8b00ff`, `#9d00ff`, `#a020f0` (VIOLETA/FUCSIA NEON)
  - Intensity: 0.88-0.95 (ALTA EMISIÓN)
  - Metalness: 0.9-0.94, Roughness: 0.1-0.15

- **Materiales de detalle** (glass/glaze/crystal):
  - Color: `#8b00ff` (VIOLETA NEON)
  - Emisión: `#ff00ff` (FUCSIA NEON, intensity 0.85)
  - Roughness: 0.05 (muy liso)
  - Opacity: 0.75 (semi-transparente)
  - **CORREGIDO**: 9 materiales que tenían verde/cyan ahora violeta/fucsia

## Materiales Actualizados

### Estructurales (12 tipos)
- MAT_Mat_build_base_silver
- MAT_Mat_build_sup
- MAT_Mat_mat_build_base_met
- MAT_Mat_mat_steel
- MAT_Mat_steel003
- MAT_Mat_docs_base
- MAT_Mat_ctrl_core
- MAT_Mat_ctrl_button_bola
- MAT_Mat_ctrl_button_triangle
- MAT_Mat_ctrl_cruz
- MAT_Mat_ctrl_antena
- MAT_Mat_ctrl_skull

### Detalles (7 tipos)
- MAT_Mat_glaze2006
- MAT_Mat_glaze_i
- MAT_Mat_glaze_ii
- MAT_Mat_facade_glass
- MAT_Mat_green_glass
- MAT_Mat_water_vis
- MAT_Mat_Water

## Edificios Afectados
- HTLand (terreno y agua)
- Experience
- Skills
- Vision
- Docs
- About
- Projects
- Blog

## Archivos Modificados
- `src/config/visual-states.json` - Configuración principal de materiales por theme

## Scripts Utilizados
- `update-themes.py` - Script principal para actualizar todos los themes
- `fix-pandora-details.py` - Script de corrección para materiales de detalle Pandora

## Testing
Para ver los cambios:
```bash
npm run dev
```

Luego cambia entre themes usando el selector de themes en la UI.

## Notas
- **Original theme**: No modificado - mantiene todos los colores por defecto
- **Terrenos**: Cambios sutiles para realismo (no radicales)
- **Cyberpunk**: Estética nocturna con metales rojos y cristales azules
- **Mars**: Estética árida con metales negros brillantes y acentos azules
- **Pandora**: Estética bioluminiscente con violetas/fucsias intensos
