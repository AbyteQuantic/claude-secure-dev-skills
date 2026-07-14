# Changelog

Todos los cambios notables de este repositorio. Formato basado en [Keep a Changelog](https://keepachangelog.com/es/1.1.0/); versionado [SemVer](https://semver.org/lang/es/).

## [1.2.0] — 2026-07-13

### Añadido
- Carpeta `portable/`: versión model-agnostic de la metodología para clientes que no son Claude Code y para modelos gratuitos/pequeños.
  - `portable/AGENTS.md`: ruleset portable en el estándar abierto AGENTS.md (OpenCode, Cursor, Aider, Windsurf, Zed, Gemini CLI, Copilot…), calibrado para modelos pequeños (prescriptivo, con checklists, y guía específica cuando no se puede escalar a un modelo mayor).
  - `portable/clients.md`: instrucciones de instalación por cliente (OpenCode global y por proyecto, Cursor, Cline, Continue, Aider/Windsurf/Zed, y system prompt para API/chat/Ollama).
- README: Opción C de instalación para otros clientes y modelos gratuitos.

## [1.1.0] — 2026-07-13

### Añadido
- Skill `spec-driven`: desarrollo contract-first con OpenAPI/AsyncAPI/proto, workflow de 5 fases, gates de CI, reglas de versionado y anti-patrones.
- Skill `fable-5-mode`: modo de trabajo Fable 5 para cualquier modelo (autonomía sostenida, evidencia sobre inferencia, notas propias, escalamiento honesto), anclado a los rasgos oficiales verificados del modelo.
- Referencia `secure-coding/references/prompt-injection-examples.md`: patrones reales de inyección de prompt a reconocer y rechazar (handshake de jailbreak, URL con token embebido, órdenes en contenido observado, falsa autoridad).
- `templates/CLAUDE.md-snippet.md`: bloque para activar el modo Fable 5 de forma obligatoria en todas las sesiones.
- `PENDIENTES.md`: tareas de despliegue documentadas para ejecución por cualquier agente/modelo.

### Cambiado
- Skill `fable-5-guide`: datos verificados contra fuentes oficiales de Anthropic — context window de 1M tokens, output 128K, pricing completo (incluye cache y batch), benchmarks de terceros, y detalle de los clasificadores de seguridad con fallback a Opus 4.8.

## [1.0.0] — 2026-07-12

### Añadido
- Skills iniciales: `secure-coding` (con `references/owasp-checklist.md`), `security-gate`, `solid-development`, `fable-5-guide`.
- Empaquetado como plugin de Claude Code: `.claude-plugin/plugin.json` + `marketplace.json`.
- `README.md`, `LICENSE` (MIT), `.gitignore`.
