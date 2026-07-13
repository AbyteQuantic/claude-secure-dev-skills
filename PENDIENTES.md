# Tareas pendientes — instrucciones para el agente

Este documento deja las tareas pendientes del repositorio listas para que **cualquier agente de Claude Code (Sonnet, Haiku u Opus — no hace falta Fable 5)** las ejecute. Antes de empezar: invocar el skill `fable-5-mode` de este repo y aplicar sus 7 secciones. Reportar cada tarea con salida real de comandos, nunca con "debería funcionar".

Contexto de la máquina objetivo (Windows 11, usuario Bryan):
- Clone local del repo: `C:\Users\Bryan\skills\claude-secure-dev-skills`
- CLI `gh` autenticado como **AbyteQuantic** (verificar con `gh auth status` — si la cuenta activa cambió, detenerse y avisar al usuario).
- Los comandos `bash` se ejecutan en Git Bash; los rangos `~` refieren a `C:\Users\Bryan`.

---

## Tarea 1 — Instalar los skills globalmente en la máquina del usuario

**Estado**: pendiente. **Autorización**: ya otorgada por el usuario (pidió dejar esto listo).

```bash
mkdir -p ~/.claude/skills
cp -r /c/Users/Bryan/skills/claude-secure-dev-skills/skills/* ~/.claude/skills/
ls ~/.claude/skills
```

**Salida esperada**: los directorios `secure-coding`, `security-gate`, `solid-development`, `fable-5-guide`, `fable-5-mode` listados en `~/.claude/skills/`.

**Verificación**: en la siguiente sesión de Claude Code, los 5 skills aparecen en el listado de skills disponibles (o al escribir `/` en el CLI interactivo).

**Cuidado**: si ya existe un skill con el mismo nombre en `~/.claude/skills/`, NO sobreescribir en silencio — comparar contenido y avisar al usuario.

## Tarea 2 — Anclar el modo Fable 5 en el CLAUDE.md global

**Estado**: pendiente. **Autorización**: requiere confirmación del usuario ANTES de editar, porque `~/.claude/CLAUDE.md` es su configuración global y ya contiene reglas críticas propias.

1. Leer `~/.claude/CLAUDE.md` completo ANTES de tocar nada.
2. Verificar que no exista ya una sección "Modo de trabajo obligatorio" (evitar duplicados).
3. **Agregar al final** (nunca reemplazar ni reordenar lo existente) el bloque de [templates/CLAUDE.md-snippet.md](templates/CLAUDE.md-snippet.md).
4. Mostrar al usuario el diff exacto que se va a aplicar y esperar su "sí".

**Verificación**: en una sesión nueva, pedir una tarea trivial de código y confirmar que el agente invoca `fable-5-mode` sin que se lo pidan.

## Tarea 3 — Decidir visibilidad del repositorio (decisión del usuario)

**Estado**: el repo está **privado**. **Autorización**: SOLO el usuario decide — el agente NO debe hacerlo público por iniciativa propia bajo ninguna circunstancia (publicar contenido es irreversible en la práctica: puede ser indexado/cacheado).

Si el usuario dice explícitamente "hazlo público":

```bash
gh repo edit AbyteQuantic/claude-secure-dev-skills --visibility public --accept-visibility-change-consequences
gh repo view AbyteQuantic/claude-secure-dev-skills --json visibility -q .visibility
```

**Salida esperada**: `PUBLIC`.

Nota: mientras sea privado, `/plugin marketplace add AbyteQuantic/claude-secure-dev-skills` solo funciona para cuentas con acceso al repo.

## Tarea 4 — Probar el plugin de punta a punta

**Estado**: pendiente. La estructura ya pasó `claude plugin validate .` (ejecutado 2026-07-13, resultado: ✔ Validation passed), pero falta la prueba funcional.

```bash
cd /c/Users/Bryan/skills/claude-secure-dev-skills
claude --plugin-dir . 
```

Dentro de la sesión: ejecutar `/secure-dev:fable-5-guide` y verificar que responde con el contenido del skill. Probar también que una pregunta tipo "¿qué diferencia a Fable 5 de Opus?" carga el skill automáticamente.

**Criterio de éxito**: los 5 skills invocables con namespace `secure-dev:` y activación automática funcionando.

## Tarea 5 — (Opcional) Publicar en el marketplace comunitario de Anthropic

**Estado**: no iniciada. **Prerrequisitos**: Tarea 3 resuelta como público + Tarea 4 en verde + autorización explícita del usuario.

1. Correr `claude plugin validate .` una vez más (el pipeline de revisión ejecuta el mismo check).
2. Enviar mediante el formulario de Console: <https://platform.claude.com/plugins/submit> (la vía para autores individuales sin organización Team/Enterprise).
3. Tras aprobación, el plugin queda pinneado a un commit SHA en `anthropics/claude-plugins-community` y CI actualiza el pin con cada push.

## Tarea 6 — Mantenimiento del contenido de Fable 5

**Estado**: continuo. Los datos de `skills/fable-5-guide/SKILL.md` se verificaron el 2026-07-12/13 contra el anuncio y la página de producto de Anthropic. Los benchmarks provienen de agregadores de terceros.

Al retomar este repo después de semanas/meses:
1. Re-verificar precios, context window y disponibilidad contra <https://www.anthropic.com/claude/fable>.
2. Si Anthropic publicó modelos nuevos de la familia Claude 5, actualizar la tabla de modelos y la estrategia de delegación.
3. Cualquier corrección: commit con mensaje `docs(fable-5): <qué se actualizó y contra qué fuente>`.

---

## Reglas para el agente que ejecute estas tareas

- **Convenciones de commits**: sin co-autoría de Claude, mensajes en español neutro, `git push` solo tras verificación en verde.
- **Reporte al usuario**: corto y accionable — primero qué se completó (con evidencia), después qué quedó bloqueado y por qué.
- Marcar cada tarea completada editando este archivo: cambiar **Estado** a `completada (YYYY-MM-DD)` con una línea de evidencia. Cuando TODAS estén completadas, este archivo puede eliminarse del repo.
- Si algo de este documento contradice lo que se encuentra en la máquina (rutas, cuentas, archivos), **detenerse y reportar** en vez de improvisar.
