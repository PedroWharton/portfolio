# Portfolio Performance Fork — Setup Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Dejar un fork funcional de Portfolio Performance en `~/Desktop/proyectos/portfolio/` que compila y arranca en macOS, con NOTES.md de orientación commiteado.

**Architecture:** No hay código nuevo: es setup de entorno. Fork en GitHub (cuenta PedroWharton) + clon local con remote `upstream`, toolchain Java/Maven vía Homebrew, build Tycho verificado, y documentación de orientación en una rama propia.

**Tech Stack:** git/gh, Homebrew, Temurin JDK 21, Apache Maven, Eclipse Tycho (lo trae el build).

## Global Constraints

- Directorio destino: `/Users/pedrowharton/Desktop/proyectos/portfolio/`
- Cuenta GitHub: `PedroWharton` (gh ya autenticado)
- JDK: Temurin 21 — **salvo** que el README/`pom.xml` del repo pida otra versión: en ese caso manda el repo (spec, sección Riesgos)
- `master` queda limpio siguiendo upstream; todo trabajo propio en ramas (`docs/notes`, futuras `feature/*`)
- No implementar features financieras; no cargar datos reales

---

### Task 1: Fork y clon con upstream

**Files:**
- Create: `/Users/pedrowharton/Desktop/proyectos/portfolio/` (clon)

**Interfaces:**
- Produces: repo local con remotes `origin` = `PedroWharton/portfolio`, `upstream` = `portfolio-performance/portfolio`. Tasks 2–4 trabajan dentro de este directorio.

- [ ] **Step 1: Fork + clon**

```bash
cd /Users/pedrowharton/Desktop/proyectos
gh repo fork portfolio-performance/portfolio --clone --remote
```

Nota: `--clone --remote` deja `origin` apuntando al fork y `upstream` al oficial automáticamente.

- [ ] **Step 2: Verificar remotes**

Run: `git -C /Users/pedrowharton/Desktop/proyectos/portfolio remote -v`
Expected: `origin … PedroWharton/portfolio`, `upstream … portfolio-performance/portfolio` (fetch y push de cada uno).

- [ ] **Step 3: Verificar rama default y estado limpio**

Run: `git -C /Users/pedrowharton/Desktop/proyectos/portfolio status`
Expected: rama default (master o main — anotar cuál para el resto del plan), working tree clean.

---

### Task 2: Toolchain — JDK y Maven

**Files:**
- Read: `portfolio/README.md`, `portfolio/CONTRIBUTING.md`, `portfolio/pom.xml` (o `portfolio-app/pom.xml`) para confirmar versión de Java y comando de build.

**Interfaces:**
- Consumes: clon de Task 1.
- Produces: `java -version` → Temurin 21 (o la versión que exija el repo), `mvn -version` funcional. Task 3 usa este toolchain.

- [ ] **Step 1: Confirmar versión de Java requerida**

Leer README/CONTRIBUTING del clon; buscar `maven.compiler` o `java.version` en el pom raíz. Si pide ≠21, sustituir la versión en los pasos siguientes.

- [ ] **Step 2: Instalar JDK y Maven**

```bash
brew install --cask temurin@21
brew install maven
```

- [ ] **Step 3: Verificar**

Run: `java -version && mvn -version`
Expected: `Temurin-21…` y Maven ≥3.9 usando ese JDK (si `mvn -version` muestra otro Java, exportar `JAVA_HOME=$(/usr/libexec/java_home -v 21)` y anotarlo para NOTES.md).

---

### Task 3: Build y arranque de la app

**Files:**
- No se modifica nada del repo; artefactos quedan en `target/`.

**Interfaces:**
- Consumes: clon (Task 1) + toolchain (Task 2).
- Produces: build en verde y ruta del binario macOS generado (`.app` bajo `portfolio-product/target/products/…` o similar — confirmar y anotar la ruta exacta para NOTES.md).

- [ ] **Step 1: Identificar el comando de build oficial**

Leer el README/CONTRIBUTING del clon. Candidato esperado (verificar):

```bash
cd /Users/pedrowharton/Desktop/proyectos/portfolio
mvn -f portfolio-app/pom.xml clean verify -DskipTests
```

- [ ] **Step 2: Correr la build**

Run: el comando confirmado, con timeout generoso (primera vez >20 min; descarga la plataforma Eclipse). Si falla por red, reintentar antes de diagnosticar.
Expected: `BUILD SUCCESS`.

- [ ] **Step 3: Arrancar la app compilada**

```bash
find . -name "*.app" -path "*products*" -maxdepth 6
open <ruta encontrada>
```

Expected: Portfolio Performance abre en macOS. Anotar la ruta exacta.

- [ ] **Step 4: Correr la suite de tests una vez (informativo)**

Run: `mvn -f portfolio-app/pom.xml verify` (sin skipTests) **solo si** la build anterior fue rápida de incrementar; si tarda demasiado, saltear y anotarlo en NOTES.md como pendiente.
Expected: PASS o anotación del estado.

---

### Task 4: NOTES.md en rama propia

**Files:**
- Create: `portfolio/NOTES.md` (en rama `docs/notes`)

**Interfaces:**
- Consumes: rutas y comandos confirmados en Tasks 2–3.
- Produces: rama `docs/notes` pusheada a `origin` con NOTES.md; `master` intacto.

- [ ] **Step 1: Crear rama**

```bash
cd /Users/pedrowharton/Desktop/proyectos/portfolio
git checkout -b docs/notes
```

- [ ] **Step 2: Escribir NOTES.md**

Contenido (completar los `<…>` con los valores reales confirmados en Tasks 2–3 — no dejar placeholders sin resolver):

```markdown
# Notas del fork (PedroWharton)

## Objetivo del fork
Features futuras (cada una con su propio spec/plan):
1. Cotizaciones argentinas (BYMA / dólar MEP-CCL) — implementar `QuoteFeed`
2. Importadores de brokers locales (Balanz, IOL, Cocos) — CSV/PDF extractors
3. UI / reportes propios (por plataforma, por moneda, ahorros vs inversiones)

## Compilar y correr
- JDK: <versión y cómo se setea JAVA_HOME si hizo falta>
- Build: <comando exacto que funcionó>
- App resultante: <ruta exacta del .app>
- Tests: <comando; estado de la última corrida>

## Mapa del código
- Quote feeds: name.abuchen.portfolio/src/name/abuchen/portfolio/online/impl/ —
  implementaciones de `QuoteFeed`; registrarse vía <mecanismo real observado>
- Importadores: name.abuchen.portfolio/src/name/abuchen/portfolio/datatransfer/ —
  `csv/` para CSV, `pdf/` para extractores de PDF por banco/broker
- UI: plugin name.abuchen.portfolio.ui (vistas, reportes)
(verificar rutas reales contra el árbol del repo y corregir si difieren)

## Branching
- `master`: limpio, sigue a upstream (`git pull upstream master`)
- Trabajo en `feature/*` rebaseadas sobre master
```

- [ ] **Step 3: Verificar rutas del mapa de código**

Run: `ls name.abuchen.portfolio/src/name/abuchen/portfolio/online/impl | head` y equivalentes para `datatransfer` y el plugin UI.
Expected: existen; si no, corregir NOTES.md con las rutas reales.

- [ ] **Step 4: Copiar spec y plan al fork**

```bash
mkdir -p docs/superpowers/specs docs/superpowers/plans
cp /Users/pedrowharton/Desktop/proyectos/docs/superpowers/specs/2026-07-14-portfolio-performance-fork-design.md docs/superpowers/specs/
cp /Users/pedrowharton/Desktop/proyectos/docs/superpowers/plans/2026-07-14-portfolio-performance-fork.md docs/superpowers/plans/
```

- [ ] **Step 5: Commit y push**

```bash
git add NOTES.md docs/
git commit -m "docs: add fork notes, spec and setup plan"
git push -u origin docs/notes
```

Expected: rama visible en `gh repo view PedroWharton/portfolio --web` (no abrir; verificar con `git ls-remote origin docs/notes`).

---

## Criterio de terminado (del spec)

- [ ] `gh repo view PedroWharton/portfolio` existe; clon con `origin`+`upstream`
- [ ] Build en verde y la app arranca localmente
- [ ] `NOTES.md` commiteado y pusheado en `docs/notes`
