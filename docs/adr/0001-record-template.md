---
title: "0001 - ADR Template"
---

# ADR 0001: Título

## Context

- ¿Qué problema estamos resolviendo?
- ¿Qué restricciones tenemos? (multi-tenancy, performance, compatibilidad)
- ¿Qué opciones se evaluaron?

Checklist de contexto (Ally360):
- Impacto en aislamiento por `tenant_id`
- Impacto en migraciones (Alembic)
- Impacto en presigned URLs / MinIO (si aplica)
- Impacto en performance (paginación, índices, N+1)
- Impacto en jobs (Celery/Redis) (si aplica)

## Decision

- ¿Qué se decidió?
- ¿Qué componentes se afectan?

## Consequences

- Pros
- Contras
- Riesgos / mitigaciones

## Alternatives considered

- Alternativa A (por qué no)
- Alternativa B (por qué no)
