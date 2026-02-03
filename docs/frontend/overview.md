---
title: Overview
---

## Objetivo

Esta sección documenta convenciones para integrar un frontend con Ally360 API, manteniendo multi-tenancy y seguridad.

## Recomendación: usar token de contexto

- Flujo recomendado: el frontend hace `POST /auth/select-company` y usa el **`context-token`** (incluye `tenant_id`).
- Esto evita errores por tenant “implícito” y reduce el riesgo de requests sin scoping.

Checklist recomendado en el cliente:
- Guardar `access-token` y `refresh-token` (si aplica) por sesión.
- Tras login, forzar selección de empresa (select-company) y operar con `context-token`.
- Cuando el usuario cambia de empresa, pedir un nuevo `context-token`.

## Alternativa: `X-Company-ID`

Si se usa `access-token`, el frontend debe enviar:

- `Authorization: Bearer <access-token>`
- `X-Company-ID: <uuid-empresa>`

Riesgo: es más fácil enviar requests sin `X-Company-ID` y terminar en 403 o (peor) queries mal scopeadas si existiera un bug.

## Errores típicos

- 401: token inválido/expirado.
- 403: usuario sin pertenencia a la empresa o sin rol.

Para depurar rápidamente:
- Si falla 401: revisar expiraciones y `APP_SECRET_STRING`.
- Si falla 403: revisar empresa activa y tipo de token.

Nota: esta sección es intencionalmente corta; se ampliará con convenciones de UI/estado/cache cuando el frontend esté definido.
