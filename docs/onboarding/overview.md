---
title: Overview
---

## Objetivo del producto

Ally360 busca ofrecer un ERP SaaS multi-empresa que cubra flujos de **ventas (ERP/POS)**, **compras**, **inventario**, **contabilidad** y **tesorería**, con trazabilidad y controles adecuados para operación diaria.

Como plataforma SaaS, el objetivo técnico principal es: **aislar completamente datos y permisos por empresa (tenant)**, manteniendo performance y una base escalable.

## Principios de diseño

- **Multi-tenant obligatorio:** todas las entidades de negocio incluyen `tenant_id` y todas las consultas filtran por `tenant_id`.
- **Seguridad por defecto:** validación estricta de entrada/salida, autorización por rol y verificación de pertenencia usuario↔empresa.
- **Escalabilidad:** arquitectura stateless, Redis para cache/rate limiting y Celery para tareas pesadas.
- **Arquitectura por capas:** `router → service → crud → models → schemas`.

## Qué deberías lograr en tu primer día

Al finalizar este onboarding, deberías poder:

1. Levantar el backend con Docker Compose.
2. Abrir la documentación OpenAPI en `http://localhost:8000/docs`.
3. Entender cómo se determina el tenant (context-token vs `X-Company-ID`).
4. Saber dónde agregar lógica nueva (router/service/crud) sin romper multi-tenancy.

## Qué necesitas para correrlo local

- Docker + Docker Compose
- Node.js (para estas docs): Node >= 20

Recomendado (para depurar): un cliente HTTP (curl, Insomnia, Postman).

Siguiente paso: [Dev Setup](./dev-setup.md) y luego [Variables de Entorno](./env-vars.md)
