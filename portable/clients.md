# Instalación por cliente — modo portable

El archivo [`AGENTS.md`](AGENTS.md) de esta carpeta es la versión model-agnostic de los skills. Cópialo o enlázalo según el cliente. Todas las rutas asumen que ya clonaste el repo:

```bash
git clone https://github.com/AbyteQuantic/claude-secure-dev-skills
```

## OpenCode

Lee `AGENTS.md` de forma nativa. Dos alcances:

```bash
# Global — aplica a TODAS tus sesiones de OpenCode
mkdir -p ~/.config/opencode
cp claude-secure-dev-skills/portable/AGENTS.md ~/.config/opencode/AGENTS.md

# Por proyecto — solo ese repo (o combínalo con las reglas propias del proyecto)
cp claude-secure-dev-skills/portable/AGENTS.md <tu-proyecto>/AGENTS.md
```

Si el proyecto ya tiene un `AGENTS.md`, no lo sobrescribas: agrega el contenido al final, o referencia este archivo desde `opencode.json` con `"instructions"`. Verificación: inicia OpenCode en el proyecto y pídele una función con validación de email — debe validar la entrada y no hardcodear nada.

## Cursor

```bash
mkdir -p <tu-proyecto>/.cursor/rules
cp claude-secure-dev-skills/portable/AGENTS.md <tu-proyecto>/.cursor/rules/secure-dev.mdc
```

Cursor también lee `AGENTS.md` de la raíz del proyecto de forma nativa, así que copiarlo ahí también funciona.

## Cline

```bash
mkdir -p <tu-proyecto>/.clinerules
cp claude-secure-dev-skills/portable/AGENTS.md <tu-proyecto>/.clinerules/secure-dev.md
```

## Continue.dev

```bash
mkdir -p <tu-proyecto>/.continue/rules
cp claude-secure-dev-skills/portable/AGENTS.md <tu-proyecto>/.continue/rules/secure-dev.md
```

## Aider, Windsurf, Zed, Gemini CLI, Copilot, Factory, Jules

Todos leen `AGENTS.md` de la raíz del proyecto de forma nativa:

```bash
cp claude-secure-dev-skills/portable/AGENTS.md <tu-proyecto>/AGENTS.md
```

(Aider también acepta `--read AGENTS.md` o declararlo en `.aider.conf.yml`.)

## Cualquier modelo por API o chat (free tier incluido)

Si usas un modelo gratuito en un playground, chat web, o llamada directa a la API (OpenAI, Groq, DeepSeek, Together, Ollama local, etc.), pega el contenido de `AGENTS.md` como **system prompt** (o como primer mensaje si no hay campo de system prompt). El modelo seguirá las reglas durante esa conversación.

Para Ollama local, puedes fijarlo en un `Modelfile`:

```
FROM llama3.1
SYSTEM """<pega aquí el contenido de portable/AGENTS.md>"""
```

## Nota de mantenimiento

`portable/AGENTS.md` es una destilación de los skills de este repo. Si actualizas un skill (`skills/*/SKILL.md`), revisa si el cambio también aplica al portable y sincronízalo. El portable es intencionalmente más corto y prescriptivo que los skills completos, porque los modelos pequeños tienen ventanas de contexto reducidas.
