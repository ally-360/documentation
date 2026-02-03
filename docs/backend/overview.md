---
title: Overview
---

## Arquitectura por capas

Convención principal:

- **router.py**: define endpoints y dependencias (sin lógica compleja)
- **service.py**: reglas de negocio / casos de uso
- **crud.py**: acceso a base de datos y queries
- **models.py**: modelos SQLAlchemy
- **schemas.py**: validaciones Pydantic
- **dependencies.py**: inyección de dependencias (DB, auth, tenant)

Regla de oro: en `crud` y `service`, las operaciones que tocan DB deben recibir/usar `tenant_id`.

## Cómo agregar una feature sin romper el diseño

Checklist típico:

1. `schemas.py`: define input/output con validación estricta.
2. `models.py`: agrega/ajusta modelos (si aplica) **con `tenant_id`**.
3. `crud.py`: implementa queries con scoping `tenant_id`.
4. `service.py`: orquesta validaciones (ownership), reglas de negocio e idempotencia.
5. `router.py`: expone endpoints con dependencias de auth/tenant.
6. Migración Alembic: 1 cambio de modelos → 1 migración.
7. Tests: incluir caso multi-tenant (A no ve B).

En listas: respetar límites de paginación (`DEFAULT_PAGE_SIZE`, `MAX_PAGE_SIZE`).

## Módulos principales (referencia)

- auth
- contacts
- products
- invoices
- bills
- files
- pos/pdv
- inventory
- accounting
- treasury
- units

Para detalles por módulo, ver en el backend:
- `ally360-api/app/modules/<module>/README.md`
- `ally360-api/app/modules/<module>/CHANGELOG.md`

## Accounting ↔ Treasury (alto nivel)

- Los documentos generan asientos con `reference_type` (ej. `Invoice`, `Bill`, `POSSale`).
- Un `TreasuryMovement` intenta generar un asiento contable **idempotente** con `reference_type="TreasuryMovement"`.
- Transferencias: 2 movimientos (OUTFLOW/INFLOW) y **1 solo asiento** con `reference_type="TreasuryTransfer"` para evitar doble contabilización.
