---
name: security-gate
description: Compuerta de validación de seguridad OBLIGATORIA antes de commit, push o PR. Usar cuando se va a commitear código, crear un PR, cerrar una tarea de desarrollo, o cuando el usuario pida validar seguridad, security review, auditar el código, escanear secretos o revisar vulnerabilidades. Bloquea el cierre si hay secretos expuestos, dependencias vulnerables o hallazgos de análisis estático sin resolver.
---

# Security Gate — validación antes de entregar

Ejecutar esta compuerta ANTES de cualquier `git commit`, `git push` o creación de PR con cambios de código. El resultado se reporta con evidencia (salida real de comandos), nunca con suposiciones. Si una herramienta no está instalada, se reporta como "no verificado" — no como "OK".

## Paso 1 — Escaneo de secretos en el diff

```bash
# Revisar el diff staged por patrones de secretos
git diff --cached -U0 | grep -nEi "(api[_-]?key|secret|passw(or)?d|token|private[_-]?key|BEGIN (RSA|EC|OPENSSH) PRIVATE|AKIA[0-9A-Z]{16}|AIza[0-9A-Za-z_-]{35}|ghp_[0-9A-Za-z]{36}|xox[baprs]-)" || echo "OK: sin patrones de secretos"
```

Si hay herramienta dedicada disponible, preferirla:

```bash
gitleaks protect --staged --verbose   # o: trufflehog git file://. --since-commit HEAD~1
```

**Cualquier match real bloquea el commit.** Si el secreto ya se commiteó antes: rotarlo en el proveedor (no basta con borrarlo del código — queda en el historial) y avisar al usuario.

## Paso 2 — Auditoría de dependencias

Solo si el cambio toca manifiestos de dependencias (`package.json`, `go.mod`, `requirements.txt`, `pom.xml`, `build.gradle`, `Gemfile`, `Cargo.toml`):

```bash
npm audit --audit-level=high      # Node.js
govulncheck ./...                 # Go
pip-audit                         # Python
osv-scanner -r .                  # multi-lenguaje
```

Vulnerabilidades **high/critical** en dependencias directas bloquean. En transitivas: evaluar alcance real (¿el código vulnerable es alcanzable?) y documentar la decisión.

## Paso 3 — Análisis estático de seguridad

```bash
semgrep scan --config auto --error   # multi-lenguaje, si está disponible
gosec ./...                          # Go
bandit -r . -ll                      # Python
```

Sin herramientas disponibles, hacer revisión manual dirigida del diff contra el checklist del skill `secure-coding`, priorizando: injection, secretos, authz faltante, deserialización insegura, criptografía débil.

## Paso 4 — Revisión manual del diff (siempre)

Leer el diff completo y responder:

1. ¿Alguna entrada externa llega a query/comando/path/HTML sin validar o escapar?
2. ¿Algún endpoint/handler nuevo carece de verificación de autorización?
3. ¿Se debilitó alguna validación, se comentó un check, o se agregó un bypass "temporal"?
4. ¿Los mensajes de error filtran información interna (stack traces, versiones, paths)?
5. ¿Se loggea PII, tokens o credenciales?
6. ¿Archivos sensibles nuevos (`.env`, keystores, dumps) están en `.gitignore`?

## Paso 5 — Reporte de la compuerta

Formato obligatorio al cerrar:

```
🔒 SECURITY GATE
├── Secretos:        ✅ limpio | ❌ N hallazgos | ⚠️ no verificado (herramienta ausente)
├── Dependencias:    ✅ sin high/critical | ❌ N vulnerabilidades | ➖ no aplica
├── Análisis estático: ✅ limpio | ❌ N hallazgos | ⚠️ revisión manual (detalle)
├── Revisión manual: ✅ 6/6 preguntas OK | ❌ hallazgos: <lista>
└── Veredicto:       APROBADO | BLOQUEADO (razones)
```

## Reglas duras

- **Nunca** reportar "seguro" sin haber ejecutado al menos los pasos 1 y 4 con salida real.
- **Nunca** usar `--no-verify` para saltar hooks de pre-commit.
- Un `⚠️ no verificado` no bloquea por sí solo, pero DEBE aparecer en el reporte para que el usuario decida.
- Los hallazgos se corrigen antes de commitear; si el usuario decide aceptar un riesgo, se documenta en el PR con su autorización explícita.
