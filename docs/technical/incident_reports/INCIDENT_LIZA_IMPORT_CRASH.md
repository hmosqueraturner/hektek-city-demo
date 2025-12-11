# Incident Report: Liza 500 "Silent Crash"
**Causa Identificada:** Fallo de resolución de Imports en Serverless (Vercel).

## Diagnóstico Definitivo
El error 500 persiste a pesar del bloque `try/catch` implementado en el parche v5.0.2.
* **Evidencia:** Si el error ocurriera dentro de la lógica (ej. API Key inválida), el `catch` lo capturaría y devolvería un JSON. Al devolver un 500 genérico instantáneo, indica que el entorno de Node.js no pudo ni siquiera cargar el archivo script.
* **Causa Raíz:** Las funciones en `/api` se ejecutan en un entorno aislado. Los imports relativos a `../../src/utils/liza/` fallan porque Vercel, por defecto, no incluye la carpeta `src` en el bundle de la API si no se configura explícitamente.

## Solución Requerida
1. **Configuración de Vercel:** Forzar la inclusión de los archivos de `src/utils/liza` en la compilación de la función serverless.
2. **Validación de SDK:** Confirmar que `package.json` tiene la versión correcta para soportar `gemini-2.0-flash-exp`.