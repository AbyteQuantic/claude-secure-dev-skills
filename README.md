# claude-secure-dev-skills

Skills de Claude Code para que **cualquier modelo de Claude** (Fable 5, Opus, Sonnet, Haiku) valide y desarrolle siempre con seguridad en el código, aplique prácticas de desarrollo sólido, y conozca qué identifica a **Claude Fable 5** frente al resto de la familia.

Mantenido por [AbyteQuantic](https://github.com/AbyteQuantic).

## Skills incluidos

| Skill | Propósito |
|-------|-----------|
| [`secure-coding`](skills/secure-coding/SKILL.md) | Desarrollo seguro proactivo: validación de entradas, manejo de secretos, OWASP Top 10, criptografía, autorización. Se activa al escribir o modificar código. |
| [`security-gate`](skills/security-gate/SKILL.md) | Compuerta de validación de seguridad obligatoria antes de commit/PR: escaneo de secretos, auditoría de dependencias, análisis estático, verificación binaria. |
| [`solid-development`](skills/solid-development/SKILL.md) | Desarrollo sólido: principios SOLID, clean code, disciplina de testing, manejo de errores, criterios de revisión. |
| [`fable-5-guide`](skills/fable-5-guide/SKILL.md) | Identidad y diferenciadores de Claude Fable 5 (tier Mythos-class): capacidades, salvaguardas, precios, y cómo aprovecharlo en Claude Code. |
| [`fable-5-mode`](skills/fable-5-mode/SKILL.md) | Modo de trabajo Fable 5 para cualquier modelo (Sonnet, Haiku, Opus): autonomía sostenida, evidencia sobre inferencia, notas propias en tareas largas, escalamiento honesto. |
| [`spec-driven`](skills/spec-driven/SKILL.md) | Desarrollo contract-first: OpenAPI/AsyncAPI/proto como fuente de verdad, workflow de 5 fases, gates de CI, versionado y anti-patrones. Se activa al crear o modificar APIs, eventos o microservicios. |

## Instalación

### Opción A — Como plugin (recomendada)

Desde una sesión interactiva de Claude Code:

```
/plugin marketplace add AbyteQuantic/claude-secure-dev-skills
/plugin install secure-dev@abyte-quantic-skills
```

### Opción B — Copia directa de skills

Copia las carpetas de `skills/` al directorio de skills personal o del proyecto:

```bash
# Global (todas las sesiones)
cp -r skills/* ~/.claude/skills/

# Por proyecto
cp -r skills/* <repo>/.claude/skills/
```

Salida esperada: los skills aparecen listados al ejecutar `/skills` (o en el listado de skills disponibles de la sesión).

## Modo Fable 5 siempre activo (cualquier modelo)

Los skills se activan por coincidencia con su descripción; para que **cualquier modelo de Anthropic trabaje como Fable 5 en TODAS las sesiones**, copia el bloque de [templates/CLAUDE.md-snippet.md](templates/CLAUDE.md-snippet.md) en el `CLAUDE.md` del proyecto (o en `~/.claude/CLAUDE.md` para todos tus proyectos). Eso ancla `fable-5-mode` + `secure-coding` + `security-gate` como comportamiento obligatorio de sesión.

**Límite honesto**: un skill impone la *metodología* de Fable 5 (autonomía, verificación con evidencia, notas en tareas largas, disciplina de seguridad), que explica gran parte de la diferencia de resultados. Lo que NO puede transferir es la capacidad del modelo en sí (razonamiento, visión, contexto de millones de tokens) — para eso, el skill instruye al modelo a escalar honestamente a Fable 5/Opus cuando la tarea lo exige.

## Estado del proyecto

Las tareas pendientes están documentadas en [PENDIENTES.md](PENDIENTES.md) con comandos exactos, salida esperada y qué requiere autorización del usuario — escritas para que **cualquier agente de Claude Code con cualquier modelo** pueda ejecutarlas.

## Filosofía

1. **La seguridad no es opcional**: todo código nuevo pasa por `secure-coding` al escribirse y por `security-gate` antes de commit.
2. **Entender antes de tocar**: mapear el flujo completo (callers, consumidores, máquina de estados) con evidencia antes de proponer un fix.
3. **Verificación empírica**: no basta con que el código "luzca bien" — se valida con herramientas (escáneres, tests, auditorías) y salida observable.
4. **Modelo correcto para la tarea correcta**: Fable 5/Opus para arquitectura y diagnóstico, Sonnet para refactor y tests, Haiku para tareas mecánicas.

## Compatibilidad

Los skills siguen el [formato de Agent Skills](https://docs.claude.com/en/docs/claude-code/skills) (`SKILL.md` con frontmatter YAML) y funcionan con cualquier modelo disponible en Claude Code.

## Licencia

MIT — ver [LICENSE](LICENSE).
