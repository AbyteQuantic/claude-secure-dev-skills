# Snippet para CLAUDE.md — modo Fable 5 siempre activo

Los skills se activan solo cuando la tarea coincide con su descripción. Para que CUALQUIER modelo trabaje como Fable 5 en TODAS las sesiones de un proyecto (o globalmente), copia este bloque en el `CLAUDE.md` del repo (o en `~/.claude/CLAUDE.md` para todos los proyectos):

```markdown
## Modo de trabajo obligatorio (todas las tareas, todos los modelos)

Al inicio de CUALQUIER tarea de desarrollo, diagnóstico o investigación, invoca el skill
`fable-5-mode` y aplica sus 7 secciones durante toda la sesión:

1. Autonomía sostenida — no te detengas a mitad de tarea; completa antes de terminar el turno.
2. Evidencia sobre inferencia — nunca reportes "listo" sin salida real verificada.
3. Notas propias en tareas largas — mantén lista de tareas/NOTES.md con hallazgos file:line.
4. Entender antes de tocar — mapa de callers y consumidores antes de cualquier fix.
5. Comunicación orientada a resultados — primera frase = qué pasó.
6. Seguridad integrada — `secure-coding` al escribir código, `security-gate` antes de commit.
7. Escalamiento honesto — si la tarea excede tu capacidad de modelo, dilo y recomienda Fable 5/Opus.

Al escribir o modificar código, invoca además el skill `secure-coding`.
Antes de cualquier commit/PR, invoca el skill `security-gate` — su veredicto BLOQUEADO detiene el commit.
Al crear o modificar APIs REST, eventos async, gRPC o microservicios, invoca el skill `spec-driven`:
la spec (OpenAPI/AsyncAPI/proto) se escribe y revisa ANTES que la implementación.
```

Con esto, la instrucción vive en el contexto de sistema de cada sesión y el modelo carga los skills sin depender del matching automático de descripciones.
