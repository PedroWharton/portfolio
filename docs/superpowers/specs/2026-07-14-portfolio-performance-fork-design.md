# Fork de Portfolio Performance — Setup del entorno de desarrollo

**Fecha:** 2026-07-14
**Estado:** aprobado en conversación, pendiente de review escrito

## Contexto y objetivo

Pedro quiere trackear sus finanzas personales (acciones, bonos, ahorros, en múltiples
cuentas/brokers, pesos + dólares). La herramienta elegida es
[Portfolio Performance](https://github.com/portfolio-performance/portfolio)
(Java / Eclipse RCP, open source), pero la versión oficial no cubre el mercado argentino.

El objetivo de **este proyecto** es dejar armado un fork propio con entorno de desarrollo
funcionando, como base para features futuras. Las features en sí quedan explícitamente
**fuera de alcance** y tendrán cada una su propio ciclo diseño → plan → implementación:

1. **Cotizaciones argentinas** — feed de precios para acciones/bonos/CEDEARs locales
   (BYMA, IOL u otra fuente) y dólar MEP/CCL como tipo de cambio.
2. **Importadores de brokers locales** — CSV/PDF de Balanz, IOL, Cocos, etc.
3. **UI / reportes propios** — vistas por plataforma, por moneda, ahorros vs. inversiones.

## Alcance (entregables de este proyecto)

1. **Fork en GitHub** de `portfolio-performance/portfolio` en la cuenta `PedroWharton`
   (vía `gh repo fork`), clonado en `~/Desktop/proyectos/portfolio/`, con remote
   `upstream` apuntando al repo oficial para poder traer actualizaciones.
2. **Toolchain instalado** vía Homebrew:
   - Temurin JDK 21 (requisito del proyecto)
   - Apache Maven (el build usa Eclipse Tycho)
3. **Build verificado:** `mvn -f portfolio-app/pom.xml clean verify -DskipTests`
   (u orden equivalente según el README del repo — verificar al clonar, la estructura
   de módulos puede haber cambiado). Primera compilación lenta: descarga la plataforma
   Eclipse completa. Criterio de éxito: la app compilada arranca en macOS.
4. **`NOTES.md`** en la raíz del fork (rama propia, no `master`) con:
   - Cómo compilar y correr la app desde el fork.
   - Mapa del código relevante a las tres features futuras:
     - Quote feeds: implementaciones de `QuoteFeed` en `name.abuchen.portfolio/…/online`
     - Importadores: `name.abuchen.portfolio/…/datatransfer` (CSV y extractores PDF)
     - UI: plugin `name.abuchen.portfolio.ui`
   - Estrategia de branching: `master` limpio siguiendo upstream; trabajo en ramas
     `feature/*`.

## Fuera de alcance

- Implementar cualquiera de las tres features.
- Configurar Eclipse IDE (se evalúa cuando se encare trabajo de UI; para feeds e
  importadores alcanza editor + Maven).
- Carga de datos financieros reales de Pedro.

## Riesgos conocidos

- **Build pesado:** Eclipse Tycho descarga cientos de MB; la primera build puede tardar
  >20 min y fallar por temas de red — reintentable.
- **Versión de Java:** si el repo exige una versión distinta a 21 al momento de clonar,
  se instala la que pida el `README`/`pom.xml` (mandan sobre este spec).
- **Divergencia con upstream:** mitigada con `master` limpio + rebase periódico de las
  ramas feature.

## Criterio de terminado

- `gh repo view PedroWharton/portfolio` existe; clon local con `origin` (fork) y
  `upstream` (oficial).
- `mvn … verify` termina en verde y la app arranca localmente.
- `NOTES.md` commiteado en el fork.
