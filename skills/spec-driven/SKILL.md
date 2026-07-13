---
name: spec-driven
description: Desarrollo spec-driven (contrato primero). Usar SIEMPRE al crear o modificar APIs REST, eventos de mensajería (Pub/Sub, Kafka, SQS), servicios gRPC, webhooks, o al scaffoldear un microservicio nuevo. Triggers - spec driven, contract first, OpenAPI, AsyncAPI, swagger, protobuf, gRPC, endpoint nuevo, evento nuevo, topic nuevo, breaking change, versionado de API, wire shape, contrato de API, microservicio nuevo.
---

# Spec-Driven Development — la spec es la fuente de verdad

**El contrato vive en la spec, no en el código.** Antes de escribir handlers, services o consumidores, existe un archivo en `api/` que define wire shapes, status codes, headers y eventos. El código se genera o se valida CONTRA esa spec. Cero "el contrato vive en los structs/interfaces del código".

## Stack canónico por tipo de contrato

| Contrato | Formato | Linter | Generación de código | Mock |
|----------|---------|--------|----------------------|------|
| REST (público o interno) | OpenAPI 3.1 | Spectral | `oapi-codegen` (Go), `openapi-generator`, `openapi-typescript` | Prism |
| Eventos async (Pub/Sub, Kafka) | AsyncAPI 2.6+ | Spectral (ruleset asyncapi) | `asyncapi-codegen` por lenguaje | — |
| gRPC | Protobuf 3 | Buf | `protoc-gen-*` / Buf generate | — |
| Webhooks salientes | OpenAPI 3.1 (callbacks) | Spectral | manual + tests de contrato | Prism |

## Estructura obligatoria del repositorio

```
<servicio>/
├── api/
│   ├── openapi.yaml        ← contrato REST
│   ├── asyncapi.yaml       ← eventos producidos/consumidos
│   ├── .spectral.yaml      ← reglas de lint del contrato
│   └── generated/          ← stubs generados — NUNCA editar a mano
├── internal/ (o src/)
│   ├── domain/             ← entidades + invariantes, libres de wire shapes
│   ├── application/        ← casos de uso
│   ├── infrastructure/     ← DB, colas, clients
│   └── transport/          ← handlers que IMPLEMENTAN la interface generada
└── Makefile / tasks        ← targets: spec-lint, spec-gen, spec-diff
```

Si una spec mezcla superficie pública e interna, separarla en dos archivos (`openapi-public.yaml` + `openapi-internal.yaml`).

## Workflow obligatorio — 5 fases

1. **Specify** — escribir/editar `api/*.yaml` con el delta de la feature. El lint (Spectral/Buf) debe pasar en local antes de seguir.
2. **Plan** — PR con SOLO la spec + ejemplos de request/response. Cero implementación. Título: `spec(<dominio>): <feature>`. La revisión se enfoca en: ¿el contrato es correcto, idempotente, versionado y retrocompatible?
3. **Tasks** — descomponer la implementación en pasos pequeños; cada paso referencia una operación concreta de la spec. Delegar pasos mecánicos a modelos eficientes (Sonnet/Haiku) cuando aplique.
4. **Implement** — regenerar stubs (`make spec-gen`) e implementar la interface generada. Si el handler cambia el response, el código no compila — esa es la señal correcta, no un problema a "resolver" desactivando el codegen.
5. **Verify** — tests de contrato (mock Prism contra la implementación o validación de schema en E2E) + `spec-diff` contra main para detectar breaking changes.

## Gates de CI obligatorios

Todo PR que toque `api/*.yaml` o la capa de transporte debe pasar:

```yaml
- run: spectral lint api/openapi.yaml --fail-severity=warn
- run: spectral lint api/asyncapi.yaml --fail-severity=warn
- run: make spec-gen && git diff --exit-code api/generated/   # stubs sincronizados
- run: oasdiff breaking origin/main:api/openapi.yaml api/openapi.yaml   # o openapi-diff --fail-on=breaking
- run: <tests>   # incluye tests de contrato
```

Breaking change detectado → label `breaking-change` automático + bloqueo, salvo que el PR documente la migración de los consumers.

## Reglas de versionado

- **Breaking** (rename, cambio de tipo, required↔optional, status code): versión nueva en el path (`/v1` → `/v2`), PR separado con plan de migración y ventana de convivencia (shadow) de mínimo 7 días. NUNCA mutar el wire shape existente en silencio.
- **Additive non-breaking** (campo opcional nuevo, endpoint nuevo, evento nuevo): merge directo.
- **Parity con consumidores legacy** (BFFs, apps móviles ya publicadas): wire shape congelado byte a byte; cualquier cambio requiere label explícito + autorización del dueño del producto.

## Eventos async — requisitos mínimos

Cada topic/evento declarado en el `asyncapi.yaml` del **productor** con:

- Naming versionado: `<ENV>_<Dominio>.<EventName>.v<N>` (ej. `PROD_Order.OrderCreated.v1`).
- Schema del payload con versión explícita.
- Headers obligatorios de trazabilidad (`traceId`, `publishedAt`, identificadores de tenant/país si aplica).
- Retry policy + topic de DLQ.
- Lista informativa de consumers conocidos.

Los consumers declaran el MISMO evento en su propio `asyncapi.yaml` con `operation: subscribe`. Ideal: test de contrato cruzado en CI que valide productor.schema = consumer.schema.

## Adopción retroactiva en repos vivos

- La extracción de spec desde código existente va en PRs separados etiquetados `spec-backfill` — NO bloquea features en curso.
- Desde el momento en que `api/openapi.yaml` existe en main, TODO endpoint nuevo requiere spec primero.
- Si el repo no tiene spec, proponerla como Step 0 (`spec(bootstrap): initial OpenAPI from current endpoints`) y esperar autorización del usuario antes de scaffoldear.

## Excepción

Endpoints internos efímeros (debug, metrics) no requieren OpenAPI completa. `/health` y `/ready` SÍ deben estar en la spec: los agentes y orquestadores los consumen para health-checks.

## Anti-patrones que obligan a rechazar el PR

1. **Commit-and-forget de stubs**: `spec-gen` produce diff y se commitea sin actualizar la spec — contrato y código divergen en silencio.
2. **Editar `api/generated/` a mano**: el próximo regen lo pisa. Extender con wrappers en la capa de transporte.
3. **Cambiar el wire shape en el handler sin tocar la spec** — o "arreglarlo" desactivando el codegen.
4. **Publish de evento sin schema en AsyncAPI**: el consumer no tiene cómo suscribirse de forma segura.
5. **Romper parity con consumidores externos sin label + autorización explícita**.

**Why:** sin spec, el contrato se infiere del código y se erosiona en cada PR; los eventos publican payloads que los consumers no esperaban; los BFF rompen apps ya publicadas cuando alguien renombra un campo "porque era confuso". Spec-first hace el contrato auditable, generable, mockeable y consumible por agentes IA.
