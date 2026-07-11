# Baseline Audit — Office AI sobre ERPNext

Fecha de auditoría: 2026-07-11

## Repositorio

- Origin: `https://github.com/aminhatar01-dot/erpnext.git`
- Upstream oficial: `https://github.com/frappe/erpnext.git`
- Rama por defecto del fork: `develop`
- Rama de trabajo: `modificaciones1` (existe en `origin`, apunta actualmente al mismo commit que `develop`)

## Commit inicial

- HEAD de `develop` / `modificaciones1` al momento de la auditoría: `b96d6e2a933bc2e74e48d8e6f5603b7792abca56`
- `git describe --tags`: `v14-baseline-640-gb96d6e2a93` (no desciende de una serie de tags v16/v17; el fork no tiene tags propios)
- Relación con upstream: `HEAD` es ancestro directo de `upstream/develop`, **1 commit por detrás** de `upstream/develop` (`45102e12cc`). El fork está prácticamente sincronizado con upstream `develop`.
- Diferencia con el último tag estable publicado (`v16.26.2`): 4138 commits por delante / 2315 por detrás — es decir, `develop` **no es un descendiente lineal de v16.26.2**, son líneas de desarrollo distintas (v16 = rama de release estabilizada; develop = tronco de desarrollo continuo hacia v17).

## Versión de ERPNext

- `erpnext/__init__.py` → `__version__ = "17.0.0-dev"`
- Es decir: la rama por defecto del fork está en **pre-release de la versión 17**, no en una versión soportada para producción.
- Última versión estable publicada por Frappe/ERPNext: **v16.26.2** (serie v16, con tags `v16.x.y` regulares).

## Frappe requerido

- No hay un `requirements.txt` fijo para Frappe en este repo (se resuelve vía `bench`), pero el CI de este mismo commit usa la imagen `ghcr.io/frappe/erpnext-ci-mariadb:py3.14-node24`, que empareja Frappe `develop` (rama equivalente, también pre-release).

## Versión de Python

- Declarada en `pyproject.toml`: `requires-python = ">=3.14"`.
- CI usa Python 3.14 (imagen `py3.14-node24`).
- **Entorno local disponible: Python 3.11.9** — no cumple el mínimo declarado por el repo. No se puede instalar un `bench` nativo funcional para esta rama sin actualizar Python o usar contenedores.

## Versión de Node

- CI usa Node 24.
- **Entorno local disponible: Node v22.15.0** — por debajo de lo usado en CI, aunque probablemente compatible para build de assets (no confirmado).

## Base de datos

- MariaDB soportada (workflow `server-tests-mariadb.yml`) y PostgreSQL en modo experimental (`server-tests-postgres.yml`).
- No hay versión exacta fijada en el repo del fork; se hereda del contenedor CI de Frappe/ERPNext.
- **No hay cliente `mysql`/`mariadb` instalado localmente.**

## Redis

- Requerido por Frappe (cache, queue, socketio) pero no versionado explícitamente en este repo.
- **No hay `redis-cli` instalado localmente.**

## Sistema operativo

- Máquina de trabajo: Windows 11 Pro (10.0.26200), con Git Bash / PowerShell disponibles.
- Docker Desktop disponible (`Docker version 29.2.1`) — es la vía recomendada para levantar Frappe/MariaDB/Redis de forma reproducible, dado que no hay Python 3.14 ni MariaDB/Redis nativos en Windows.

## Dependencias relevantes (extracto de `pyproject.toml`)

```
Unidecode~=1.4.0
barcodenumber~=0.5.0
rapidfuzz~=3.14.3
holidays~=0.87
googlemaps~=4.10.0
plaid-python~=7.2.1
python-youtube~=0.9.8
pypng~=0.20220715.0
mt-940>=4.26.0
pdfplumber>=0.11.0
```

`package.json`: dependencia mínima (`onscan.js`), build de assets delega en submódulo `banking` vía `yarn`.

## Estado inicial de las pruebas

- **No ejecutadas todavía.** No es posible correr la suite de tests de ERPNext (`bench run-tests`) sin un `bench` completo (Frappe + sitio + MariaDB + Redis), que no está disponible nativamente en este entorno (Python 3.11 vs 3.14 requerido, sin MariaDB/Redis locales).
- Plan: levantar el stack vía Docker (Frappe Docker oficial) para poder ejecutar pruebas reales. Se documentará en `docs/TEST_REPORT.md` cuando se ejecute.
- CI del propio proyecto (`server-tests-mariadb.yml`, `server-tests-postgres.yml`, `linters.yml`) está definido y es reutilizable como referencia de comandos.

## Vulnerabilidades detectadas

- No se ejecutó todavía un escaneo de dependencias (`pip-audit`, `npm audit`, Dependabot/CodeQL). Pendiente como parte de la etapa de CI/CD (sección 10 del mandato). No se afirma ausencia de vulnerabilidades sin haber corrido la herramienta.

## Advertencias de compatibilidad

1. **Brecha de versión de Python**: repo requiere ≥3.14, entorno local tiene 3.11.9. Bloquea ejecución nativa de `bench`.
2. **Rama de desarrollo, no release**: `develop`/`modificaciones1` está en `17.0.0-dev`, por delante de la última versión estable soportada (`v16.26.2`). Ver `docs/VERSION_STRATEGY.md` para la decisión y el riesgo documentado.
3. Sin `redis-cli`/`mysql` locales — cualquier prueba real requiere contenedores (Docker) o una VM/WSL con el stack completo.
4. `modificaciones1` no tiene commits propios todavía (apunta al mismo commit que `develop`): no hay riesgo de pérdida de trabajo al decidir la estrategia de base.

## Diferencias con upstream

- Ninguna divergencia de código: el fork (`aminhatar01-dot/erpnext`) está a 1 commit de `upstream/develop`, sin commits propios todavía. Es, a efectos prácticos, un espejo de upstream `develop` en este momento.
