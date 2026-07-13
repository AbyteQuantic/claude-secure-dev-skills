# Checklist OWASP Top 10 (2021) — mapeo práctico

Referencia rápida para auditar código nuevo o existente contra las categorías OWASP.

## A01 — Broken Access Control
- Autorización verificada por recurso, no solo por ruta.
- IDs opacos o verificación de ownership en cada lectura/escritura (anti-IDOR).
- Métodos HTTP restringidos (no aceptar GET donde solo aplica POST).
- CORS: allowlist explícita de orígenes; nunca reflejar `Origin` arbitrario con credenciales.

## A02 — Cryptographic Failures
- TLS en tránsito siempre; datos sensibles cifrados en reposo.
- Sin algoritmos rotos: MD5, SHA1, DES, RC4, ECB mode.
- Claves fuera del código; rotación documentada.

## A03 — Injection
- SQL/NoSQL: prepared statements o ORM. Grep de alerta: string concat/interpolación cerca de `query(`, `exec(`, `raw(`.
- Command injection: nunca `shell=True` / `exec` con entrada externa; usar arrays de argumentos.
- XSS: escape contextual; frameworks con auto-escape (React, plantillas con escape por defecto); no `dangerouslySetInnerHTML` / `v-html` / `innerHTML` con datos externos.
- LDAP/XPath/Template injection: mismas reglas — entrada nunca interpretada como código.

## A04 — Insecure Design
- Modelar amenazas antes de implementar features sensibles (auth, pagos, uploads).
- Rate limiting en endpoints de login, OTP, recuperación de contraseña.
- Límites de tamaño en uploads y bodies.

## A05 — Security Misconfiguration
- Sin credenciales/configuración por defecto en producción.
- Stack traces y modos debug apagados en producción.
- Headers: `Content-Security-Policy`, `Strict-Transport-Security`, `X-Content-Type-Options: nosniff`, `X-Frame-Options`/`frame-ancestors`.
- Buckets/almacenamiento cloud: sin acceso público salvo decisión explícita documentada.

## A06 — Vulnerable and Outdated Components
- `npm audit` / `pip-audit` / `govulncheck` / `osv-scanner` en CI.
- Dependencias sin mantenimiento (> 2 años sin release) requieren justificación.
- Imágenes base de contenedores actualizadas y mínimas (distroless/alpine cuando aplique).

## A07 — Identification and Authentication Failures
- MFA disponible para operaciones sensibles.
- Sesiones invalidadas en logout y en cambio de contraseña.
- Sin enumeración de usuarios (mismo mensaje/tiempo para "usuario no existe" y "contraseña incorrecta").
- Tokens de reset de un solo uso y con expiración corta.

## A08 — Software and Data Integrity Failures
- CI/CD: dependencias con integridad verificada (lockfiles, checksums, firmas).
- Deserialización de datos no confiables prohibida o con validación de schema estricta.
- Webhooks entrantes: verificar firma HMAC antes de procesar.

## A09 — Security Logging and Monitoring Failures
- Loggear eventos de seguridad: logins fallidos, cambios de permisos, accesos denegados.
- Logs con `traceId` correlacionable; sin PII/secretos.
- Alertas sobre patrones anómalos (fuerza bruta, spikes de 403).

## A10 — Server-Side Request Forgery (SSRF)
- URLs externas: allowlist de esquemas (`https`) y hosts.
- Bloquear rangos privados: `127.0.0.0/8`, `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`, `169.254.0.0/16`, `::1`.
- Resolver DNS y validar la IP resultante (prevenir DNS rebinding) cuando el riesgo lo amerite.

## Comandos de auditoría rápida por ecosistema

Ejecutar en la raíz del repo (terminal del proyecto):

```bash
# Node.js
npm audit --audit-level=high

# Python
pip-audit || pip install pip-audit && pip-audit

# Go
govulncheck ./...

# Multi-lenguaje (si está instalado)
osv-scanner -r .
semgrep scan --config auto
```

Salida esperada: cero vulnerabilidades high/critical. Cualquier hallazgo bloquea el merge hasta resolverse o documentar la excepción con fecha de remediación.
