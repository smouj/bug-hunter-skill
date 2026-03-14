skill: bug-hunter
name: Bug Hunter
description: Agente avanzado enfocado en seguridad especializado en detectar, analizar y eliminar vulnerabilidades de código en aplicaciones web, APIs y sistemas backend. Se integra con herramientas estándar de la industria para auditorías de seguridad completas.
version: 1.2.3
author: SMOUJBOT
tags: [bugs, security, hunting, vulnerability, owasp, penetration-testing, static-analysis, dynamic-scanning]
dependencies:
  - owasp-zap (para escaneo dinámico)
  - bandit (para linting de seguridad en Python)
  - semgrep (para escaneo basado en reglas personalizadas)
  - trufflehog (para detección de secretos)
  - eslint-security-plugin (para JavaScript/Node.js)
  - sqlmap (para pruebas de inyección SQL, opcional)
environment_variables:
  - ZAP_API_KEY: Clave API para integración con OWASP ZAP
  - SEMGREP_APP_TOKEN: Token para funciones en la nube de Semgrep
  - TRUFFLEHOG_GITHUB_TOKEN: Token de GitHub para escaneo de repositorios
  - VIRUSTOTAL_API_KEY: Para detección de malware en dependencias
requirements:
  - Python 3.8+ para Bandit y Semgrep
  - Node.js 16+ para plugins de ESLint
  - Docker para escaneos en contenedores
  - Acceso al código base objetivo (local o vía Git)
  - Permisos de escritura para correcciones propuestas

---

# Bug Hunter Skill

## Purpose

La skill Bug Hunter rastrea y elimina vulnerabilidades de código en proyectos de software, enfocándose en amenazas de seguridad que podrían llevar a brechas de datos, acceso no autorizado o compromiso del sistema. Esta skill está diseñada para defensa proactiva, transformando correcciones reactivas de bugs en erradicación sistemática de vulnerabilidades.

### Real Use Cases

- **Mitigación de OWASP Top 10**: Escanea automáticamente aplicaciones web en busca de fallos de inyección, autenticación rota, exposición de datos sensibles y otras vulnerabilidades del top-10, proporcionando recomendaciones de corrección priorizadas.
- **Integración de Seguridad en CI/CD**: Incorpora escaneos de vulnerabilidades en pipelines de desarrollo para detectar problemas como secretos codificados o dependencias inseguras antes del despliegue.
- **Forense Post-Incidente**: Analiza código bases comprometidos para identificar causas raíz de brechas, como inyección SQL en formularios de login o XSS en entradas de usuario.
- **Preparación de Auditoría de Terceros**: Genera informes de seguridad completos para auditorías de cumplimiento, destacando vulnerabilidades corregidas y riesgos restantes.
- **Fortificación de Seguridad en APIs**: Detecta y parchea vulnerabilidades en APIs REST/GraphQL, incluyendo bypasses de límite de tasa y manejo inadecuado de errores que filtran información sensible.

## Alcance

La skill Bug Hunter maneja detección y remediación de vulnerabilidades en múltiples capas de seguridad de aplicaciones. Opera en código bases, entornos en ejecución y configuraciones de despliegue.

### Specific Commands

- `/bug-hunt scan-static`: Realiza análisis estático de código usando herramientas como Bandit (Python), plugins de seguridad de ESLint (JavaScript) y reglas de Semgrep para patrones de vulnerabilidades personalizadas.
- `/bug-hunt scan-dynamic <url>`: Lanza escaneos OWASP ZAP o similares en endpoints en vivo para identificar vulnerabilidades en tiempo de ejecución (ej. XSS, CSRF).
- `/bug-hunt secrets-hunt`: Usa Trufflehog para detectar secretos expuestos, claves API y credenciales en código e historial de commits.
- `/bug-hunt dep-audit`: Audita dependencias en busca de CVEs conocidas usando herramientas como npm audit, pip-audit o Snyk.
- `/bug-hunt exploit-test <payload>`: Prueba de manera segura exploits específicos como inyección SQL o inyección de comandos usando entornos controlados.
- `/bug-hunt fix-vuln <cve-id>`: Genera y aplica correcciones automatizadas para vulnerabilidades comunes (ej. escapar entradas de usuario, agregar headers CSP).
- `/bug-hunt report-gen`: Compila hallazgos en informes amigables para ejecutivos con puntuaciones de severidad, métricas de explotabilidad y cronogramas de remediación.

### Supported File Types and Environments

- **Languages**: Python, JavaScript/Node.js, Java, Go, PHP, Ruby (con integraciones de herramientas apropiadas).
- **Frameworks**: Express.js, Django, Spring Boot, Rails, Flask.
- **Environments**: Desarrollo local, contenedores Docker, despliegues en la nube (AWS, GCP, Azure vía integraciones API).

## Proceso de Trabajo Detallado

El Bug Hunter sigue un enfoque sistemático y basado en evidencia para la gestión de vulnerabilidades, asegurando cobertura completa sin introducir falsos positivos o romper funcionalidad.

1. **Evaluación Inicial**: Analiza la estructura del código base usando herramientas `grep` y `glob` para identificar puntos de entrada (ej. manejadores de entrada de usuario, consultas de base de datos). Estima alcance del escaneo y requisitos de recursos.

2. **Fase de Análisis Estático**: Ejecuta Bandit en archivos Python (`bandit -r . -f json`) y ESLint con plugins de seguridad en archivos JS/TS. Usa Semgrep con reglas personalizadas para problemas específicos del framework (ej. `semgrep --config=owasp-top-10`).

3. **Detección de Secretos**: Ejecuta Trufflehog en el repositorio (`trufflehog git --repo-path=. --json`) para encontrar credenciales filtradas, luego referencia cruzada con variables de entorno.

4. **Escaneo de Dependencias**: Ejecuta `npm audit --audit-level=moderate` o `pip-audit` para identificar paquetes vulnerables. Para contenedores, usa `docker scan` o Trivy.

5. **Pruebas Dinámicas**: Si aplica, inicia un entorno de prueba con Docker Compose y realiza escaneos ZAP (`zap.sh -cmd -quickurl http://localhost:3000 -quickout /tmp/zap_report.html`).

6. **Triaje de Vulnerabilidades**: Categoriza hallazgos por severidad (puntuación CVSS), explotabilidad (usando mapeos CWE) e impacto en el negocio. Prioriza problemas críticos como RCE o filtraciones de datos.

7. **Generación de Correcciones**: Propone parches de código, como reemplazar `eval(user_input)` con alternativas seguras o agregar sanitización de entrada. Usa herramienta `edit` para aplicar cambios directamente.

8. **Pruebas y Validación**: Ejecuta pruebas unitarias (`pytest` o `jest`) y pruebas de integración para asegurar que las correcciones no rompan funcionalidad. Realiza re-escaneos para confirmar que las vulnerabilidades están eliminadas.

9. **Documentación e Informes**: Genera informes en markdown con snippets de código antes/después, estrategias de mitigación y medidas preventivas para desarrollo futuro.

10. **Monitoreo Continuo**: Sugiere integración en CI/CD (ej. GitHub Actions con workflows de seguridad) para prevención continua de vulnerabilidades.

## Reglas de Oro

- **Tolerancia Cero para Alta Severidad**: Nunca dejar vulnerabilidades críticas (CVSS > 7.0) sin corregir; escalar inmediatamente si las correcciones automatizadas fallan.
- **Escaneo Consciente del Contexto**: Siempre considerar el contexto de la aplicación—ej. no marcar falsos positivos en archivos de prueba o código demo intencionalmente vulnerable.
- **Seguro por Diseño**: Las correcciones deben seguir el principio de menor privilegio; ej. preferir listas de permitidos sobre listas de denegados para validación de entrada.
- **Sin Cambios Disruptivos**: Los parches deben mantener compatibilidad hacia atrás a menos que se apruebe explícitamente; probar exhaustivamente en entornos de staging.
- **Hacking Ético Únicamente**: Los escaneos deben realizarse en sistemas propios o autorizados; nunca apuntar a producción sin permiso.
- **Registro de Auditoría**: Registrar todos los hallazgos, decisiones y cambios en el repositorio para cumplimiento y responsabilidad.
- **Seguridad en Control de Versiones**: Usar ramas de feature para correcciones; nunca commitear directamente a main/master sin revisión.
- **Manejo de Falsos Positivos**: Verificar manualmente hallazgos automatizados antes de reportar; descartar con evidencia si son benignos.

## Ejemplos

### Ejemplo 1: Escaneo Estático para Inyección SQL
**Prompt de Entrada de Usuario**: `/bug-hunt scan-static --lang=python --focus=sql-injection`

**Salida del Proceso**:
- Escaneo iniciado en `/src/**/*.py`
- Encontrados 3 puntos potenciales de inyección SQL:
  - Línea 45 en `user_auth.py`: `cursor.execute(f\"SELECT * FROM users WHERE id = {user_id}\")` (Alta severidad, CWE-89)
  - Línea 112 en `search.py`: `db.query(\"SELECT * FROM products WHERE name LIKE '%\" + search_term + \"%'\")` (Alta severidad, CWE-89)
  - Línea 78 en `admin_panel.py`: `execute_query(user_input)` (Media severidad, inyección indirecta)
- Recomendaciones: Usar consultas parametrizadas (ej. `cursor.execute(\"SELECT * FROM users WHERE id = %s\", (user_id,))`)

**Corrección Aplicada**: Actualizadas todas las instancias con statements preparados; re-escaneo muestra 0 vulnerabilidades.

### Ejemplo 2: Detección de Secretos en Historial de Git
**Prompt de Entrada de Usuario**: `/bug-hunt secrets-hunt --repo=. --since=2023-01-01`

**Salida del Proceso**:
- Escaneando historial de commits en busca de secretos expuestos...
- Detectados 2 hallazgos:
  - Commit abc123: AWS_ACCESS_KEY_ID en `config/old_env.py` (Entropía: 4.2, verificado válido)
  - Commit def456: Token de GitHub en `scripts/deploy.sh` (Coincidencia de patrón: formato clásico de token de GitHub)
- Acciones: Secretos rotados vía consola AWS; historial de commits limpiado con `git filter-branch` (después de backup).

**Verificación**: Re-ejecutar escaneo; no se detectan secretos.

### Ejemplo 3: Escaneo Dinámico en API en Vivo
**Prompt de Entrada de Usuario**: `/bug-hunt scan-dynamic https://api.example.com/login --spider`

**Salida del Proceso**:
- Spider de ZAP crawling iniciado...
- Escaneo activo completado en 5m 32s
- Vulnerabilidades encontradas:
  - XSS en formulario de login (Reflejado, Media severidad): Payload `<script>alert(1)</script>` ejecuta
  - Token CSRF faltante en endpoint de reset de contraseña (Alta severidad)
- Prueba de exploit: Login simulado con payload malicioso exitoso.

**Corrección Aplicada**: Agregados headers CSP (`Content-Security-Policy: default-src 'self'`) y tokens CSRF usando Flask-WTF.

### Ejemplo 4: Corrección de CVE en Dependencias
**Prompt de Entrada de Usuario**: `/bug-hunt dep-audit --package-manager=npm`

**Salida del Proceso**:
- Auditando `package.json`...
- Paquetes vulnerables:
  - lodash@4.17.4: CVE-2020-8203 (Contaminación de prototipo, Alta)
  - express@4.17.1: CVE-2021-23343 (DoS de expresión regular, Media)
- Actualizaciones: `npm update lodash@^4.17.21` y `express@^4.17.2`

**Verificación**: `npm audit` retorna limpio; pruebas de integración pasan.

## Comandos de Rollback

- **Revertir Correcciones de Código**: `git revert <commit-hash>` para parches de seguridad que introducen regresiones (ej. si sanitización de entrada rompe datos válidos).
- **Deshacer Actualizaciones de Dependencias**: `npm install <package>@<previous-version>` o `pip install <package>==<version>` para rollback de correcciones de paquetes vulnerables si causan problemas de compatibilidad.
- **Restaurar Archivos Escaneados**: Si ediciones corrompieron archivos, usar `git checkout -- <file>` para revertir a estado pre-escaneo.
- **Deshabilitar Headers de Seguridad**: Remover temporalmente headers CSP o HSTS en archivos de config (ej. editar `nginx.conf` o `.htaccess`) si bloquean tráfico legítimo post-corrección.
- **Resetear Entorno**: Para escaneos dinámicos, detener contenedores con `docker compose down` y restaurar desde backup si escaneos causaron corrupción de datos.
- **Limpiar Artefactos de Escaneo**: Remover reportes ZAP o archivos temp con `rm -rf /tmp/zap_*` si contienen datos sensibles de escaneo.

## Pasos de Verificación

- **Escaneos Post-Corrección**: Re-ejecutar escaneos estáticos/dinámicos para confirmar vulnerabilidades eliminadas (objetivo: 0 hallazgos críticos/alta).
- **Pruebas Funcionales**: Ejecutar suite completa de pruebas (`npm test`, `pytest`, etc.) para asegurar no hay regresiones.
- **Benchmarks de Seguridad**: Usar checklists como OWASP ASVS para verificar cumplimiento.
- **Pruebas de Penetración**: Revisión manual o pentest de terceros para validar correcciones contra exploits del mundo real.
- **Monitoreo**: Verificar logs por picos de errores o eventos de seguridad después del despliegue.

## Solución de Problemas Comunes

- **Falsos Positivos en Análisis Estático**: Ajustar reglas de Semgrep con flags `--exclude` o agregar comentarios `# nosec` para código intencional.
- **Timeouts en Escaneo ZAP**: Aumentar timeout con `--timeout=600` o usar `--quickscan` para resultados más rápidos pero menos exhaustivos.
- **Fallos en Escaneo de Dependencias**: Asegurar herramientas actualizadas (`pip install --upgrade bandit`); manejar registros privados con tokens de auth.
- **Sobrecarga en Detección de Secretos**: Limitar escaneos con `--max-depth=5` para repos grandes; ignorar fixtures de prueba con patrones personalizados.
- **Conflictos de Corrección**: Usar `git merge --abort` si parches de seguridad entran en conflicto con features en curso; reaplicar después de resolución.
- **Impacto en Rendimiento**: Ejecutar escaneos en horas de bajo tráfico o usar procesamiento paralelo (`semgrep --jobs=4`).
- **Errores de Instalación de Herramientas**: Verificar dependencias del sistema (ej. `apt install python3-dev` para Bandit); usar entornos virtuales para evitar conflictos.