---
name: solid-development
description: Desarrollo sólido y mantenible. Usar al diseñar arquitectura, crear módulos, clases, servicios o refactorizar código existente. Triggers - SOLID, clean code, clean architecture, refactor, diseño de módulos, testing, TDD, cobertura de tests, manejo de errores, code review, deuda técnica, mantenibilidad.
---

# Solid Development — código que dura

Estándar de calidad estructural para todo código nuevo o refactorizado. Complementa a `secure-coding` (seguridad) con solidez de diseño, testing y mantenibilidad.

## Antes de tocar código: entender el flujo completo

Regla previa a cualquier fix o refactor:

1. Mapear la cadena de llamadas afectada con evidencia directa (`file:line`), no con inferencia.
2. Identificar TODOS los callers de la función/endpoint que se va a tocar.
3. Inventariar consumidores que dependen del comportamiento actual (otros servicios, eventos, reportes).
4. Validar la hipótesis con datos reales (logs, tests, base de datos) antes de proponer el cambio.
5. Reportar separado: "lo que sé con evidencia" vs "lo que estoy infiriendo".

## Principios SOLID aplicados

| Principio | Regla práctica | Señal de violación |
|-----------|----------------|--------------------|
| **S** — Single Responsibility | Un módulo, una razón de cambio | Clase/archivo que mezcla transporte + lógica de negocio + persistencia |
| **O** — Open/Closed | Extender por composición/estrategia, no editando switch gigantes | Agregar un caso nuevo obliga a tocar 5 archivos |
| **L** — Liskov Substitution | Subtipos honran el contrato del padre | Override que lanza `NotImplemented` o cambia semántica |
| **I** — Interface Segregation | Interfaces pequeñas por consumidor | Interface de 15 métodos donde cada cliente usa 2 |
| **D** — Dependency Inversion | Dominio depende de abstracciones; infraestructura las implementa | `import mysql` dentro de la lógica de negocio |

Estructura de referencia (servicios backend):

```
internal/
├── domain/          # entidades + invariantes — sin dependencias externas
├── application/     # casos de uso — orquesta dominio vía interfaces
├── infrastructure/  # implementaciones concretas (DB, colas, HTTP clients)
└── transport/       # handlers HTTP/gRPC — solo traducen wire ↔ casos de uso
```

## Disciplina de testing

- **Todo cambio de comportamiento lleva test** que falla sin el cambio y pasa con él.
- Pirámide: mayoría unit (rápidos, aislados), integración para bordes de infraestructura, E2E mínimos para flujos críticos.
- Tests deterministas: sin `sleep` arbitrarios, sin dependencia de red real, sin orden implícito entre tests.
- Nombres que describen comportamiento: `rechaza_orden_sin_stock`, no `test_case_3`.
- Cobertura: 100% sobre el cambio crítico (la línea nueva de lógica); no perseguir 100% global como métrica vanidosa.
- Antes de declarar "listo": lint + tests en verde con salida real, nunca "debería pasar".

## Manejo de errores

- Errores propagados con contexto (`fmt.Errorf("cargando orden %s: %w", id, err)`), no tragados.
- Nunca `catch {}` vacío ni `except: pass` — como mínimo, log con nivel adecuado y decisión explícita.
- Distinguir errores esperables (validación, 404) de bugs (invariante rota) — los segundos deben ser ruidosos.
- Operaciones externas (HTTP, DB, colas) con timeout explícito y estrategia de retry idempotente cuando aplique.
- Fallar rápido en arranque si falta configuración crítica; no en el primer request.

## Contratos primero

- APIs REST/eventos: la spec (OpenAPI/AsyncAPI/proto) es la fuente de verdad del contrato, no los structs.
- Cambios breaking de contrato: versión nueva (`/v2`), nunca mutación silenciosa del wire shape.
- Eventos publicados sin schema documentado = PR rechazado.

## Criterios de code review (aplicar también al propio código)

1. ¿El cambio hace UNA cosa? (commits/PRs atómicos)
2. ¿Los nombres revelan intención sin necesitar comentarios?
3. ¿Hay lógica duplicada que ya existe en el codebase? (buscar antes de escribir)
4. ¿Los comentarios explican el *por qué* de restricciones no obvias — no el *qué* de cada línea?
5. ¿Se puede borrar código en vez de agregarlo? (la mejor línea es la que no existe)
6. ¿El código nuevo sigue las convenciones del código circundante (idioma, estilo, patrones)?

## Anti-patrones que obligan a rehacer

- Fix "intuitivo" sin haber mapeado callers y consumidores.
- Copiar/pegar bloques con variaciones mínimas en vez de extraer función.
- Abstracción prematura: crear interfaces/factories para un único uso concreto.
- Editar código generado a mano (se pierde en el próximo regen).
- Mezclar refactor + cambio de comportamiento en el mismo commit (imposible de revisar y revertir).
