---
title: Auth (JWT)
---

## Flujo (alto nivel)

1. `POST /auth/register`
2. `POST /auth/verify-email`
3. `POST /auth/login`
4. `POST /auth/select-company` → devuelve **token de contexto** con tenant

Objetivo del flujo: separar “quién eres” (login) de “en qué empresa estás operando” (contexto/tenant).

## Tipos de token

- `access`: token normal de autenticación.
- `context`: token que incluye `tenant_id` (selección de empresa), recomendado para frontend.

## Cómo se usan

- Recomendado: `Authorization: Bearer <context-token>` (tenant explícito dentro del token).
- Alternativa: `Authorization: Bearer <access-token>` + `X-Company-ID: <uuid-empresa>`.

Regla práctica:
- Si el cliente ya sabe la empresa activa, usar `context-token`.
- Si por alguna razón se usa `access-token`, **no olvidar** `X-Company-ID` en endpoints de negocio.

## Roles por empresa

Roles típicos:
- `owner`, `admin`, `seller`, `accountant`, `viewer`

La autorización se evalúa en el contexto de `(user_id, tenant_id)`.

Ejemplo de intención (conceptual):
- Un usuario puede ser `admin` en la empresa A y `viewer` en la empresa B.

## Errores esperados

- 401: token inválido/expirado.
- 403: el usuario no pertenece al tenant o no tiene permisos para la acción.

Checklist cuando veas 403:
- ¿El request usa `context-token`?
- Si usa `access-token`: ¿envías `X-Company-ID` correcto?
- ¿El usuario pertenece a esa empresa?
- ¿El rol requerido coincide con la acción?
