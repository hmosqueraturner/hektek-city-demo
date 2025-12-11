# Incident Report: Liza Chat API 500 Error
**Fecha:** 30 Nov 2025
**Componente:** `/api/liza/chat`
**Severidad:** Alta (Bloqueante en Producción)

## 1. Descripción del Problema
La funcionalidad de chat "Liza" funciona correctamente en entorno local (`localhost`), pero devuelve un error `500 Internal Server Error` al desplegarse en Vercel Production.

**Log del Cliente:**
```text
/api/liza/chat:1 Failed to load resource: the server responded with a status of 500 ()
hook.js:608 [useLizaChat] Error: Error: API Error: 500