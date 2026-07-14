# Notas del fork (PedroWharton)

## Objetivo del fork
Features futuras (cada una con su propio spec/plan):
1. Cotizaciones argentinas (BYMA / dólar MEP-CCL) — implementar `QuoteFeed`
2. Importadores de brokers locales (Balanz, IOL, Cocos) — CSV/PDF extractors
3. UI / reportes propios (por plataforma, por moneda, ahorros vs inversiones)

## Compilar y correr
- JDK: Temurin 21.0.11. Hace falta setear `JAVA_HOME` explícitamente antes de invocar Maven, si no Maven resuelve a Homebrew OpenJDK 26:
  ```bash
  export JAVA_HOME=/Library/Java/JavaVirtualMachines/temurin-21.jdk/Contents/Home
  export MAVEN_OPTS="-Xmx4g"
  ```
- Build: `mvn -f portfolio-app/pom.xml clean verify -DskipTests` (desde la raíz del repo). BUILD SUCCESS confirmado en 13:22 min.
- App resultante: `portfolio-product/target/products/name.abuchen.portfolio.product/macosx/cocoa/aarch64/PortfolioPerformance.app` (build Apple Silicon; también existe variante `x86_64` y un zip en `portfolio-product/target/portfolio.product-0.85.1-SNAPSHOT.zip`).
- Tests: no corridos todavía (pendiente). Comando completo: `mvn -f portfolio-app/pom.xml clean verify` (sin `-DskipTests`). Opción más rápida y offline para los tests del core (de CONTRIBUTING.md):
  ```bash
  mvn -f portfolio-app/pom.xml verify -o \
    -pl :portfolio-target-definition,:name.abuchen.portfolio.pdfbox1,:name.abuchen.portfolio.pdfbox3,:name.abuchen.portfolio,:name.abuchen.portfolio.junit,:name.abuchen.portfolio.tests -am -amd
  ```

## Mapa del código
- Quote feeds: `name.abuchen.portfolio/src/name/abuchen/portfolio/online/impl/` —
  implementaciones de `QuoteFeed` (ej. `AlphavantageQuoteFeed.java`, `BinanceQuoteFeed.java`, etc.).
  Registro real: **Java `ServiceLoader`**, vía el archivo
  `name.abuchen.portfolio/META-INF/services/name.abuchen.portfolio.online.QuoteFeed`,
  que lista el nombre totalmente calificado de cada clase implementadora (una por línea).
  No es una extensión de `plugin.xml`/Eclipse; una `QuoteFeed` nueva debe agregarse a esa
  lista de `META-INF/services` para que el runtime la descubra.
- Importadores: `name.abuchen.portfolio/src/name/abuchen/portfolio/datatransfer/` —
  `csv/` para CSV, `pdf/` para extractores de PDF por banco/broker, `ibflex/` para Interactive
  Brokers Flex, `traderepublic/` para Trade Republic, `actions/` para acciones de importación.
- UI: plugin `name.abuchen.portfolio.ui` (vistas, wizards, reportes). Existe además
  `name.abuchen.portfolio.ui.tests` para tests de UI.

## Branching
- `master`: limpio, sigue a upstream (`git pull upstream master`)
- Trabajo en `feature/*` rebaseadas sobre master
