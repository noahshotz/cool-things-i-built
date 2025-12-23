# Referenzprojekte

## Kurzübersicht
1. Semantic Search Microservice
2. Satelliten-Monitoring
3. Ressourcenoptimierung mit CI/CD
4. Algorithmen
5. System design

## Semantic Search Microservice

### Projektübersicht

Die Anwendung wurde im Rahmen meiner Abschlussarbeit des Studiengangs Medieninformatik B.Sc. an der Berliner Hochschule über einen Zeitraum von 3 Monaten konzipiert und implementiert. Betreut wurde die Arbeit von Prof. Dr. Siamak Haschemi, Zweitgutachter war Prof. Dr. Zbigniew Jerzak.

Das von Herrn Haschemi neu gegründete non-profit Startup *donista* zielt darauf ab, durch Affiliate-Links bei Partnershops Provisionen zu generieren, die als Spenden sozialen Zwecken zugewendet werden sollen.

Dies geschieht zum einen über eine Browser-Extension, zum anderen ist jedoch auch geplant, relevante Produkte für die ein Affiliate-Programm vorliegt, direkt auf der eigenen Website und nativen App darzustellen. Die möglichen Produkte kommen hierbei von einem externen Datenlieferanten (https://s24.com/).

Für Produktsuche und -empfehlungen sollte auf Basis von Vector-Search-Technologien eine semantische Suche konzipiert und implementiert werden.

### Implementierung

Die Anwendung habe ich als Erweiterung des in Kotlin geschrieben Haupt-Backend als einen Microservice in Python implementiert, der Technologie-Stack sieht hierbei folgendermaßen aus:

- PostgreSQL als Datenbank
- pgvector: Vector similarity search für PostgreSQL
- Python als Programmiersprache
- sentence-transformers für die Integration von Embedding-Modellen
- SQLAlchemy als ORM
- FastAPI und Uvicorn für die Implementierung der RestAPI und Bereitstellung als Webserver
- Containervirtualisierung mit Docker
- Unit Tests mit pytest

Link zum Repository: https://github.com/noahshotz/s24.com-pgvector

[Schriftliche Ausarbeitung](https://github.com/noahshotz/cool-things-i-built/blob/master/docs/BA.pdf)

Für die Demo während der Verteidigung habe ich auch noch ein einfaches UI in Next.js implementiert, welches die über die API bereitgestellten Funktionen verwendet: https://github.com/noahshotz/donista-semantic-ui

## Satelliten-Monitoring

### Projektübersicht

Bei diesem Projekt handelt es sich zum Teil um eine Praxisleistung für ein Studienmodul als auch Hobbyprojekt. Ziel war es, eine einfache Web-Anwendung zu implementieren und diese unter Verwendung aktueller CI/CD- und Monitoring-Tools zu veröffentlichen.

Hierfür habe ich ein Anwendung implementiert, mit der verschiedenen Satelliten im Erd-Orbit in Echtzeit verfolgt werden können und einige Telemetriedaten eingesehen werden können. Der technische Hintergrund ist hier das parsen von TLE (codierte Satellitenbahnelemente).

### Implementierung

- Framework: React / Next.js
- UI: Shadcn & Tailwind

Die TLE-Daten werden von der N2YO API (https://www.n2yo.com/api/) bereitgestellt. Um diese zu parsen habe ich satellite.js verwendet (https://github.com/shashwatak/satellite-js), die Daten wurden dann mittels GeoJSON und Mapbox visualisiert.

Live-Demo: https://sattrack-v1-0.vercel.app/

Link zum Repository: https://github.com/noahshotz/sattrack-next

## Ressourcenoptimierung mit CI/CD

### Ausgangssituation

Für das Deployment der Infrastruktur von Website, internen Verwaltungstools, Backend mit REST API und Datenbank während meiner Tätigkeit bei der MCG Investitions Holding GmbH & Co. KG war eine passende Lösung notwendig.

Aus verschiedenen Gründen wollte ich vermeiden, dafür verschiedene PaaS-Anbieter (z.B. Vercel, Netlify, Railway, Fly.io, Render) zu verwenden, welche separat abgerechnet werden.

Stattdessen habe ich bei einem deutschen Serveranbieter, bei welchem auch schon unsere Domains liegen, einen Linux VPS gemietet und dort Coolify (https://coolify.io/) installiert.

### Das Problem

Der laufende Betrieb der Infrastruktur verbraucht im Schnitt relativ wenig Ressourcen, sodass ein vergleichsweise günstiger VPS seinen Zweck dafür erfüllt. 

Problematisch wird es jedoch beim Build-Prozess: Das Kompilieren einer React-Anwendung mit `npm run build` ist erheblich ressourcenintensiver als der spätere Betrieb. Während des Builds müssen alle Dependencies aufgelöst, JavaScript-Dateien transpiliert, gebundelt und optimiert werden. Webpack oder Vite laden dabei große Teile des Projekts in den Arbeitsspeicher, führen Tree-Shaking durch, minimieren den Code und generieren optimierte Assets.

Bei unserem VPS mit begrenztem RAM führte dieser Prozess regelmäßig dazu, dass der verfügbare Arbeitsspeicher vollständig ausgeschöpft wurde und es in einem Server-Crash endete.

### Die Lösung

Um dieses Problem zu umgehen, habe ich den Build-Prozess in eine GitHub Actions CI/CD-Pipeline ausgelagert. Bei jedem Push auf die `master`- oder `dev`-Branch wird automatisch ein Workflow ausgelöst, der folgende Schritte durchführt:

1. **Versionierung**: Basierend auf den Commit-Messages wird automatisch eine neue semantische Version (SemVer) generiert
2. **Build & Test**: Die Anwendung wird gebaut und getestet
3. **Docker Image erstellen**: Ein vollständiges Docker Image wird mit der React-Anwendung erstellt und mit entsprechenden Tags (Version, `latest`, `prod`/`dev`) versehen
4. **Push zu GitHub Container Registry**: Das Image wird in die ghcr.io Registry hochgeladen
5. **Automatisches Deployment**: Per Webhook wird Coolify benachrichtigt, welches das fertige Image vom VPS herunterlädt und deployed

Der VPS muss somit nur noch das fertige, optimierte Docker Image ausführen, ohne selbst den ressourcenintensiven Build-Prozess durchlaufen zu müssen. Die gesamte Kompilierung findet auf der GitHub-Infrastruktur statt, während der VPS lediglich für das Ausführen der fertigen Container zuständig ist.

Link zur CI/CD-Konfiguration: https://github.com/noahshotz/cool-things-i-built/blob/master/examples/ci-cd/react-docker-coolify.yml

## Algorithmen

### Projektübersicht

### Implementierung

Link zum Repository: https://github.com/noahshotz/strtocraft

## System design