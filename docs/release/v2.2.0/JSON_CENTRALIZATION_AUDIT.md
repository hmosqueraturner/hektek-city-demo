# JSON_CENTRALIZATION_AUDIT

## Diagnóstico Ejecutivo

**Estado: CRÍTICO**

La arquitectura de "Single Source of Truth" está actualmente **rota**. Aunque existen archivos de configuración robustos (`buildings-config.json` y `theme-config.json`), los componentes de React (especialmente `MapRPG.jsx` y `BuildingLabel.jsx`) los ignoran casi por completo.

El código actual utiliza una estrategia de "Hardcoding Paralelo", donde los valores se definen manualmente en el JSX, duplicando (y a menudo contradiciendo) lo que está en los JSON. Esto hace que cualquier cambio en los archivos de configuración sea inútil, ya que la UI no los lee.

## Tabla de Violaciones

| Archivo | Línea (aprox) | Tipo de Violación | Descripción |
| :--- | :--- | :--- | :--- |
| `src/components/MapRPG.jsx` | 424, 478, 532, 587, 639, 694, 746 | **Hardcoding Espacial** | `position`, `rotation`, y `scale` se pasan manualmente a `DynamicBuildingModel` en lugar de leerse de `buildings-config.json`. |
| `src/components/MapRPG.jsx` | 441, 495, 549, 604, 656, 711, 763 | **Hardcoding Visual** | `BuildingLabel` recibe `text`, `position` y `fontSize` fijos en el JSX. |
| `src/components/MapRPG.jsx` | 167-175 | **Estado Inicial** | `useState` para `markers` se inicializa con coordenadas fijas ("números mágicos") en lugar de `null` o valores del config. |
| `src/components/MapRPG.jsx` | 216-247 | **Lógica Duplicada** | `cameraSettings` define offsets y rotaciones de cámara hardcodeados por edificio. Esto debería ser parte de la configuración del edificio. |
| `src/components/MapRPG.jsx` | 569, 676 | **Lógica de Navegación** | Rutas como `/blog` y `/test-variants` están hardcodeadas en props `redirectPath`. |
| `src/components/BuildingLabel.jsx` | 135-181 | **Hardcoding Visual** | `getLabelStyleForTheme` contiene un `switch/map` manual de colores por tema, ignorando `theme-config.json`. |
| `src/components/MapRPG.jsx` | 412, 466, 520, 576, 627, 683, 734 | **Contenido Hardcodeado** | Textos descriptivos y títulos (`STRINGS.*` o strings directos) se pasan manualmente en `handleSelectSection`. |

## Plan de Refactorización

1.  **Centralización de Datos (Hooks):**
    *   Crear un hook `useBuildingConfig(buildingId)` que devuelva toda la config (transform, label, navigation, camera) para un edificio.
    *   Crear un hook `useThemeConfig()` que devuelva los colores y estilos del tema activo desde `theme-config.json`.

2.  **Refactorización de `MapRPG.jsx`:**
    *   Eliminar las declaraciones manuales de `<DynamicBuildingModel>` y `<BuildingLabel>`.
    *   Reemplazar con un `.map()` sobre `Object.entries(buildingsConfig.buildings)` para renderizar los edificios dinámicamente.
    *   Inyectar props directamente desde el objeto de configuración del iterador.

3.  **Limpieza de `BuildingLabel.jsx`:**
    *   Eliminar `getLabelStyleForTheme`.
    *   Hacer que acepte `style` o `themeConfig` como prop, pasados desde el padre (que ya los leyó del JSON).

4.  **Inyección de Navegación:**
    *   Hacer que `NavigableBuilding` lea `redirectPath` de la prop de configuración inyectada.

## Prompt de Corrección

Copia y pega este prompt para ejecutar la corrección:

```text
@antigravity Por favor, ejecuta el "Plan de Refactorización" definido en el reporte JSON_CENTRALIZATION_AUDIT.md.

Pasos específicos:
1. Modifica `src/components/BuildingLabel.jsx` para eliminar el hardcoding de temas y aceptar props de estilo dinámicas.
2. Refactoriza `src/components/MapRPG.jsx` para que deje de renderizar edificios manualmente. En su lugar, debe iterar sobre `src/config/buildings-config.json` y renderizar los componentes `DynamicBuildingModel`, `BuildingLabel` y `NavigableBuilding` dinámicamente usando SÓLO los datos del JSON.
3. Asegúrate de que la navegación y los textos también vengan del JSON.
4. Verifica que no quede ningún "número mágico" de posición o color en estos componentes.
```
