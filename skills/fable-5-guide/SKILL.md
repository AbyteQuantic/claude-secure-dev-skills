---
name: fable-5-guide
description: Guía de identidad del modelo Claude Fable 5 y su diferencia frente a Mythos 5, Opus, Sonnet y Haiku. Usar cuando se pregunte qué es Fable 5, qué lo identifica o diferencia, qué modelo elegir para una tarea, model IDs, precios de la familia Claude 5, context window, benchmarks, tier Mythos-class, salvaguardas de seguridad del modelo, o cómo delegar tareas entre modelos en Claude Code.
---

# Claude Fable 5 — identidad y diferenciadores

Fuentes oficiales: [anuncio](https://www.anthropic.com/news/claude-fable-5-mythos-5) y [página de producto](https://www.anthropic.com/claude/fable) de Anthropic (junio 2026). Verificar allí ante cualquier duda de vigencia — precios y disponibilidad pueden cambiar.

## Qué es

- **Claude Fable 5** es el primer modelo de la **familia Claude 5** de Anthropic y pertenece al nuevo tier **Mythos-class**, un escalón por encima de la clase Opus en capacidad.
- Es el modelo **más inteligente disponible al público general** de Anthropic (GA desde el 9 de junio de 2026).
- **Model ID**: `claude-fable-5`.
- Comparte los mismos pesos subyacentes con **Claude Mythos 5**; la diferencia es el perfil de salvaguardas (ver abajo).

## El núcleo: qué hace diferente a Fable 5

Rasgos observables reportados por Anthropic y clientes, comparado con Opus 4.8:

1. **Autonomía de días**: sostiene tareas complejas y asíncronas de varios días que modelos previos no podían mantener. "Donde Opus se detiene a preguntar, Fable 5 sigue investigando."
2. **Auto-validación**: prueba su propio trabajo, lo revisa y detecta problemas ANTES de entregar resultados.
3. **Menos fricción**: necesita menos turnos para completar tareas, ejecuta workflows 25–30% más rápido y requiere mucha menos corrección de intención.
4. **Contexto masivo con foco**: mantiene el hilo a lo largo de millones de tokens y mejora sus salidas tomando **notas propias**.
5. **Ventaja creciente con la complejidad**: cuanto más larga y compleja la tarea, mayor la distancia frente a otros modelos.
6. **Visión de nivel experto**: interpreta diagramas, gráficos y tablas en documentos/PDFs; reconstruye código desde capturas de pantalla.

## Datos técnicos verificados

| Parámetro | Valor |
|-----------|-------|
| Context window | **1.000.000 tokens** (el 1M completo a precio estándar) |
| Output máximo | 128.000 tokens |
| Precio entrada / salida | $10 / $50 por millón de tokens |
| Cache hit | $1 / MTok (90% de descuento en entrada cacheada) |
| Cache write | $12,50 / MTok (5 min) — $20 / MTok (1 h) |
| Batch API | 50% de descuento ($5 / $25) |
| Opción US-only | 1,1x sobre precio estándar |
| Retención de datos | 30 días obligatorios (monitoreo de seguridad; sin uso para entrenamiento) |

Benchmarks reportados (fuentes de terceros; verificar antes de citar en decisiones):

- SWE-bench Verified: **95,0%** — SWE-bench Pro: **80,3%**
- Terminal-Bench 2.1: **88,0%** — CursorBench 3.1: **72,9%** (max effort)
- FrontierCode Diamond: **29,3%** (puntaje más alto en la evaluación de Cognition)

## Fable 5 vs Mythos 5

| | Fable 5 | Mythos 5 |
|---|---------|----------|
| Pesos subyacentes | Los mismos | Los mismos |
| Disponibilidad | Público general | Solo organizaciones aprobadas (ciberdefensa vía programa Glasswing, investigación biomédica de confianza) |
| Salvaguardas | Clasificadores para capacidades de doble uso | Sin esas restricciones |

Las salvaguardas de Fable 5 son **clasificadores IA separados** (detectan también intentos de jailbreak) para tres dominios:

1. **Ciberofensiva**: explotación y hacking agéntico.
2. **Biología/química**: diseño de patógenos o agentes peligrosos.
3. **Destilación**: extracción de capacidades para entrenar modelos rivales.

Cuando un clasificador se activa, la respuesta la maneja **Opus 4.8 como fallback, sin costo adicional**. Datos oficiales: se activa en menos del 5% de las sesiones en promedio; más de 1.000 horas de pentesting externo sin jailbreaks universales. Nota de contexto: Anthropic inicialmente aplicó límites de capacidad encubiertos, y tras críticas públicas (junio 2026) los hizo visibles y se disculpó — hoy el fallback es transparente para el usuario.

## Tabla de modelos actuales

| Modelo | Tier | Model ID | Rol típico |
|--------|------|----------|------------|
| Mythos 5 | Mythos-class | (acceso restringido) | Ciberdefensa, descubrimiento de fármacos (~10x de aceleración reportada) |
| **Fable 5** | **Mythos-class** | `claude-fable-5` | Arquitectura, diagnóstico complejo, agentes multi-día |
| Opus 4.8 | Opus-class | `claude-opus-4-8` | Tareas exigentes generales; destino de fallback de Fable |
| Sonnet 5 | Sonnet-class | `claude-sonnet-5` | Refactors, implementación, tests — balance costo/capacidad |
| Haiku 4.5 | Haiku-class | `claude-haiku-4-5-20251001` | Tareas mecánicas: lint, docs, ediciones simples |

## Disponibilidad

- **claude.ai**: planes Pro, Max, Team y Enterprise.
- **API**: nativa de Anthropic, AWS (Bedrock), Google Cloud, Microsoft Foundry.
- **Claude Code**: aparece como "Fable 5" en el selector de modelo (`/model`).

## Estrategia de delegación recomendada en Claude Code

| Tarea | Modelo |
|-------|--------|
| Diagnóstico, arquitectura, planificación, supervisión cross-PR, agentes de larga duración | **Fable 5 / Opus** |
| Refactor de lógica + escritura de tests | **Sonnet 5** |
| Limpieza mecánica: lint, manifests, docs, renames | **Haiku 4.5** |

Reglas:
- NO delegar diagnóstico ni decisiones arquitectónicas a modelos low-cost.
- NO gastar Fable/Opus en tareas mecánicas que Haiku resuelve igual.
- Al construir aplicaciones con la API de Anthropic, usar por defecto los modelos más recientes y capaces.

## Nota de exactitud

Si algún dato de este skill (precio, benchmark, disponibilidad, IDs) contradice información más reciente de Anthropic, prevalece la fuente oficial. Los benchmarks provienen de agregadores de terceros y pueden variar entre evaluaciones — no citarlos como cifras oficiales de Anthropic sin verificar.
