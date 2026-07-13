---
name: fable-5-guide
description: Guía de identidad del modelo Claude Fable 5 y su diferencia frente a Mythos 5, Opus, Sonnet y Haiku. Usar cuando se pregunte qué es Fable 5, qué lo identifica o diferencia, qué modelo elegir para una tarea, model IDs, precios de la familia Claude 5, tier Mythos-class, salvaguardas de seguridad del modelo, o cómo delegar tareas entre modelos en Claude Code.
---

# Claude Fable 5 — identidad y diferenciadores

Fuente principal: [anuncio oficial de Anthropic](https://www.anthropic.com/news/claude-fable-5-mythos-5) (junio 2026). Verificar allí ante cualquier duda de vigencia — precios y disponibilidad pueden cambiar.

## Qué es

- **Claude Fable 5** es el primer modelo de la **familia Claude 5** de Anthropic y pertenece al nuevo tier **Mythos-class**, un escalón por encima de la clase Opus en capacidad.
- Es el modelo **más inteligente disponible al público general** de Anthropic (GA desde el 9 de junio de 2026).
- **Model ID**: `claude-fable-5`.
- Comparte el mismo modelo subyacente con **Claude Mythos 5**; la diferencia es el perfil de salvaguardas (ver abajo).

## Fable 5 vs Mythos 5

| | Fable 5 | Mythos 5 |
|---|---------|----------|
| Modelo subyacente | El mismo | El mismo |
| Disponibilidad | Público general | Solo organizaciones aprobadas (defensores ciber, investigación biomédica de confianza) |
| Salvaguardas | Clasificadores adicionales para capacidades de doble uso | Sin esas restricciones |

Las salvaguardas de Fable 5 son **clasificadores IA** que detectan y bloquean:

1. **Ciberofensiva**: explotación y hacking agéntico → la sesión hace fallback a Opus 4.8.
2. **Biología/química**: diseño de patógenos o agentes peligrosos.
3. **Destilación**: intentos de extraer capacidades para entrenar modelos rivales.

Datos del anuncio: más del 95% de las sesiones de Fable no involucran ningún fallback; más de 1.000 horas de pentesting externo sin jailbreaks universales encontrados.

## Qué lo diferencia de los demás modelos

| Modelo | Tier | Model ID | Rol típico |
|--------|------|----------|------------|
| Mythos 5 | Mythos-class | (acceso restringido) | Ciberdefensa, descubrimiento de fármacos (~10x de aceleración reportada) |
| **Fable 5** | **Mythos-class** | `claude-fable-5` | Arquitectura, diagnóstico complejo, trabajo autónomo largo |
| Opus 4.8 | Opus-class | `claude-opus-4-8` | Tareas exigentes generales; destino de fallback de Fable |
| Sonnet 5 | Sonnet-class | `claude-sonnet-5` | Refactors, implementación, tests — balance costo/capacidad |
| Haiku 4.5 | Haiku-class | `claude-haiku-4-5-20251001` | Tareas mecánicas: lint, docs, ediciones simples |

Capacidades diferenciales de Fable 5 (reportadas en el anuncio):

- **Ingeniería de software**: puntaje más alto en FrontierCode (Cognition); Stripe reportó comprimir meses de migraciones de código en días.
- **Autonomía extendida**: trabaja de forma autónoma por más tiempo que cualquier Claude previo.
- **Contexto largo**: mantiene el foco a lo largo de millones de tokens y mejora sus salidas tomando notas propias.
- **Visión**: extrae cifras precisas de figuras científicas; reconstruye código desde capturas de pantalla.
- **Análisis complejo**: mejora sustancial en razonamiento financiero y tareas analíticas prolongadas.

## Precios y datos operativos

- **API**: $10 / millón de tokens de entrada, $50 / millón de tokens de salida.
- **Retención de datos**: 30 días para modelos Mythos-class, sin uso para entrenamiento de modelos futuros.
- En Claude Code, Fable 5 aparece como "Fable 5" en el selector de modelo (`/model` en CLI o el selector de la app).

## Estrategia de delegación recomendada en Claude Code

Usar el modelo más eficiente por tipo de tarea (vía subagentes o `/model`):

| Tarea | Modelo |
|-------|--------|
| Diagnóstico, arquitectura, planificación, supervisión cross-PR, trabajo autónomo largo | **Fable 5 / Opus** |
| Refactor de lógica + escritura de tests | **Sonnet 5** |
| Limpieza mecánica: lint, manifests, docs, renames | **Haiku 4.5** |

Reglas:
- NO delegar diagnóstico ni decisiones arquitectónicas a modelos low-cost.
- NO gastar Fable/Opus en tareas mecánicas que Haiku resuelve igual.
- Al construir aplicaciones con la API de Anthropic, usar por defecto los modelos más recientes y capaces de la lista de arriba.

## Nota de exactitud

Si algún dato de este skill (precio, disponibilidad, IDs) contradice información más reciente de Anthropic, prevalece la fuente oficial. No inventar benchmarks ni cifras que no estén en el anuncio.
