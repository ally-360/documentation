---
title: Multi-tenancy
---

## Objetivo

Garantizar que **un tenant no pueda ver ni modificar datos de otro tenant**.

Regla base:
- **Toda tabla de negocio incluye `tenant_id`.**
- **Toda query debe filtrar por `tenant_id`.**

Consecuencia práctica: si en algún punto se hace una query sin `tenant_id`, se asume un **riesgo de fuga de datos**.

## Resolución de tenant

Ally360 soporta dos formas principales:

1) **Token de contexto (`type=context`)** (recomendado)
- El token ya incluye `tenant_id`.
- Reduce ambigüedad y hace el request más explícito.

Patrón recomendado (cliente):
- `Authorization: Bearer <context-token>`

2) **Header `X-Company-ID` + token normal (`type=access`)**
- Se envía el tenant por header: `X-Company-ID: <uuid-empresa>`.
- El backend usa ese tenant para scoping.

Notas internas:
- El backend puede poblar `request.state.tenant_id` a partir de `X-Company-ID`.
- Aun así, la autorización debe validar pertenencia usuario↔empresa.

Patrón alternativo (cliente):
- `Authorization: Bearer <access-token>`
- `X-Company-ID: <uuid-empresa>`

## Validación de pertenencia (membership)

En ambos casos, debe existir relación **usuario ↔ empresa**.

Comportamiento esperado:
- Si el usuario no pertenece al tenant solicitado → **403 Forbidden**.
- Si falta `tenant_id` / header cuando la ruta lo requiere → error de validación/autorización según la dependencia.

Tip: para reducir fricción en frontend, el camino recomendado es **siempre usar context-token** después de seleccionar empresa.

## Checklist (para devs)

Antes de abrir PR, confirmar:

- [ ] Modelos de negocio con `tenant_id`.
- [ ] Índices/constraints compuestos con `tenant_id` cuando aplica (por ejemplo, unique por tenant).
- [ ] CRUD: `WHERE tenant_id = :tenant_id` en todas las queries.
- [ ] Services: validan ownership en operaciones que reciben IDs externos (ej. `customer_id`).
- [ ] Tests de integración multi-tenant: tenant A no ve datos de tenant B.

### Checklist extra (muy común de olvidar)

- [ ] En endpoints que aceptan IDs (por ejemplo `product_id`, `contact_id`), validar que el recurso pertenece al `tenant_id` del request.
- [ ] Evitar “joins” sin scoping. Si haces join entre tablas multi-tenant, ambas deben coincidir en `tenant_id`.
- [ ] En seeds/scripts: siempre setear `tenant_id` explícitamente.
