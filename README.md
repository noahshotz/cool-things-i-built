# Referenzprojekte

## Kurzübersicht
1. Semantic Search Microservice
2. Satelliten-Monitoring
3. Ressourcenoptimierung mit CI/CD
4. Algorithmen
5. System design
6. Projekte im Dezember 2025

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

Für einen Handelsplatz für virtuelle Gegenstände entwickelte ich einen Algorithmus im Kontext von Counter-Strike:Global Offensive (CS:GO) bzw. Counter-Strike 2 (CS2). In diesem Spiel können Spieler digitale Sticker auf ihre Waffen kleben – diese Sticker zeigen oft einzelne Buchstaben oder kurze Zeichenfolgen (z.B. Teamlogos, Spielernamen). Eine beliebte Praxis ist es, Sticker so zu kombinieren, dass sie ein Wort oder einen Namen ergeben.

Mein Algorithmus ermittelt für eine gegebene Zeichenkette alle möglichen Sticker-Kombinationen, mit denen diese dargestellt werden kann. Geplant war die Integration in ein Web-Interface, über das Nutzer die benötigten Sticker auf der Handelsplattform direkt erwerben können. Der Markt für virtuelle Gegenstände in CS2 erreichte 2025 eine Kapitalisierung von ca. 6 Milliarden Euro.

Nach Änderung des Entwickler des Spiels, Valve, am Anwendungsprinzip der Sticker-Mechaniken wurde das Projekt nicht weiter verfolgt.

### Implementierung

Der Kern des Algorithmus basiert auf Dynamic Programming. Ein Vektor speichert für jede Position im Input-String alle gültigen Sticker-Kombinationen, die bis zu dieser Position führen. Durch Iteration über alle möglichen Teilstrings und Abgleich mit dem Dictionary werden schrittweise vollständige Lösungen aufgebaut.

**Besonderheiten:**
- Begrenzung auf maximal 4 Sticker pro Kombination (entspricht den verfügbaren Slots auf den meisten Waffen im Spiel)
- Sortierung der Ergebnisse nach Länge, um kürzeste Kombinationen zu priorisieren

Die Anwendung wurde als CLI-Tool in C++20 mit CMake implementiert und diente als Referenzimplementierung für eine geplante Web-Integration.

Link zum Repository: https://github.com/noahshotz/strtocraft

## System design

### Projektübersicht

Seit April 2025 leite ich als Gründungsmitglied die technische Umsetzung der Plattform egenus, die vom egenus e.V. entwickelt und betrieben wird.

Mit egenus stellen wir eine digitale Plattform bereit, die Menschen in schwierigen Lebenslagen den Zugang zu Hilfsangeboten erleichtert. Gleichzeitig sollen soziale Einrichtungen ihre Angebote bestmöglich präsentieren und verwalten können. Barrierefreiheit, Mehrsprachigkeit sowie Werbefreiheit und Datenschutz stehen für uns an erster Stelle.

### Übersicht über die Plattform

Grob zusammengefasst gibt es 2 verschiedene User:

#### Menschen in Not

"In Not" zu sein, bedeutet nicht immer nur Obdachlosigkeit. Soziale Not kann sich auf verschiedenste Weisen abbilden: Armut, Wohnungs- und Obdachlosigkeit, Arbeitslosigkeit und Überschuldung, Lebenskrisen und Wegfall des sozialen Netzes, Einsamkeit, häusliche Gewalt und vieles mehr.

Diesen Menschen geben wir mit unserer Plattform einen direkten, barrierefreien, mehrsprachigen und jederzeit von überall aus erreichbaren Überblick über mögliche Hilfsangebote in ihrer direkten Umgebung.

Schon mit einem einzigen Klick kann dabei der aktuell problematische Lebensumstand angegeben werden.

#### Soziale Einrichtungen

Deutschlandweit verbringen die Mitarbeitenden sozialer Einrichtungen einen Großteil ihrer Arbeit mit der "Arbeit neben der Arbeit" – also verwaltungstechnischen Aufgaben, die Ressourcen einbinden und Mitarbeiter davon abhalten, ihre eigentliche Arbeit machen zu können: Hilfe von Mensch zu Mensch.

Seit inzwischen mehr als einem Jahr sind wir in ständigem Austausch und Partnerschaft mit vielen Einrichtungen, Organisationen und Sozialarbeitern, um deren Bedürfnisse zu verstehen, Lösungen zu implementieren und diese im gemeinsamen Review zu verbessern.

Mit unserer Plattform bieten wir ein umfangreiches Toolset zur Digitalisierung und Verwaltung der Angebote und Möglichkeiten der verschiedenen sozialen Einrichtungen.

### Architektur-Überblick

Die Plattform folgt einer Schichten-Architektur mit klarer Trennung zwischen Präsentations-, Logik- und Datenschicht:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              CLIENTS                                        │
├─────────────────────┬─────────────────────┬─────────────────────────────────┤
│   Marketing Site    │   egenus Platform   │         Platform Users          │
│    (Next.js/TS)     │  (React SPA + Vite) │  Public | Einrichtungen | Admin │
│    egenus.org       │     egenus.app      │                                 │
└─────────────────────┴──────────┬──────────┴─────────────────────────────────┘
                                 │ HTTPS
                                 ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                            API LAYER                                        │
├─────────────────────────────────────────────────────────────────────────────┤
│                    Node.js / Express REST API (TypeScript)                  │
│                          api.egenus.app/api/v1/*                            │
│                                                                             │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐            │
│  │   /login    │ │  /facility  │ │    /user    │ │/organization│  ...       │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘            │
│                                                                             │
│  Middleware: CORS | Rate Limiting | JWT Auth | Compression | Validation     │
└───────────────────────────────────┬─────────────────────────────────────────┘
                                    │
              ┌─────────────────────┼─────────────────────┐
              ▼                     ▼                     ▼
┌─────────────────────┐ ┌─────────────────────┐ ┌─────────────────────┐
│      MongoDB        │ │   External APIs     │ │     Monitoring      │
│   (Prod + Staging)  │ │                     │ │                     │
│                     │ │  • DeepL            │ │  • Prometheus       │
│  • Users            │ │  • Brevo.           │ │  • Grafana          │
│  • Organizations    │ │  • Mapbox           │ │  • PostHog          │
│  • Facilities       │ │                     │ │                     │
│  • Logs             │ │                     │ │                     │
└─────────────────────┘ └─────────────────────┘ └─────────────────────┘
```

### Tech Stack

| Layer | Technologie | Anmerkungen |
|-------|-------------|-------------|
| **Frontend** | React 18, Vite, TypeScript | SPA mit react-router-dom |
| **UI/Styling** | HeroUI, Tailwind CSS |  |
| **Forms & Validation** | react-hook-form, zod | Schema-basierte Validierung |
| **State Management** | React Context + Hooks |  |
| **Backend** | Node.js, Express, TypeScript | Implementierung als REST API |
| **Datenbank** | MongoDB + Mongoose | Flexible Schemas für sich entwickelnde Datenmodelle |
| **Auth** | JWT in HTTP-only Cookies | Rollenbasiert: System-Rollen (admin/user) + Organization-Rollen (owner/maintainer/staff) |
| **API Docs** | Apidog | Zentrale API-Dokumentation für das Team |

### Deployment & CI/CD

Die gesamte Infrastruktur wird über GitLab CI/CD automatisiert deployed:

```
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│  Feature     │      │     Dev      │      │    Main      │
│   Branch     │ ──▶  │    Branch    │ ──▶  │   Branch     │
└──────────────┘      └──────────────┘      └──────────────┘
       │                     │                     │
       │                     ▼                     ▼
       │              ┌─────────────┐       ┌─────────────┐
       │              │   Staging   │       │ Production  │
       │              │ Environment │       │ Environment │
       │              └─────────────┘       └─────────────┘
       │               dev.egenus.app        egenus.app
       │              dev.api.egenus.app    api.egenus.app
       │
       ▼
  Code Review
  via Merge Request
```

| Komponente | Hosting | Deployment |
|------------|---------|------------|
| Frontend | Vercel | Automatisch via GitLab CI |
| Backend | Railway | Automatisch via GitLab CI |
| MongoDB | Railway | Managed, wöchentliche Backups |
| Monitoring | Railway | Prometheus + Grafana Stack |

**CI/CD Pipeline Features:**
- Automatische Tests (Jest + MongoDB Memory Server für isolierte DB-Tests)
- Build-Artefakte mit Expiration
- Branch-basierte Deployment-Targets (main → prod, dev → staging)
- Automatische Release-Erstellung bei Version-Tags

### API Design

Die REST API folgt einer ressourcenorientierten Struktur mit Versionierung:

```
POST   /api/v1/login
GET    /api/v1/facility/:id
POST   /api/v1/facility
PATCH  /api/v1/facility/:id
GET    /api/v1/organization/:id/members
...
```

**Security-Maßnahmen:**
- JWT-Authentifizierung in HTTP-only Cookies
- Rate Limiting (1000 Requests / 5 Min pro IP)
- CORS-Whitelist (nur eigene Frontend-Domains)
- Input-Validierung auf Router-Ebene via express-validator
- Request Compression (gzip)

**Autorisierungsmodell:**
```
System-Ebene:           role: "a" (Admin) | "u" (User)

Organization-Ebene:     role: owner | maintainer | staff
```

### Datenmodell

Die Plattform basiert auf einem Multi-Tenancy-Modell mit der Organization als zentralem Verwaltungsorgan:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                  USER                                       │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │      id  │ email │ firstName │ lastName │ admin │ organization[]       │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                     │                                       │
│                    ┌────────────────┴────────────────┐                      │
│                    ▼                                 ▼                      │
│     ┌──────────────────────────┐      ┌──────────────────────────┐          │
│     │  { organizationId: "A",  │      │  { organizationId: "B",  │          │
│     │    role: "owner" }       │      │    role: "staff" }       │          │
│     └──────────────────────────┘      └──────────────────────────┘          │
└─────────────────────────────────────────────────────────────────────────────┘
                    │                                 │
                    ▼                                 ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                             ORGANIZATION                                    │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │ orgId │ orgName │ orgAddress │ orgPhone │ isVerified │ orgStatus       │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────┘
                                     ▲
                                     │ organization (Reference)
                                     │
┌─────────────────────────────────────────────────────────────────────────────┐
│                               FACILITY                                      │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │ id │ title │ type │ public │ inOperation │ organization │ creator      │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                     │                                       │
│              ┌──────────────────────┼──────────────────────┐                │
│              ▼                      ▼                      ▼                │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐          │
│  │ address         │    │ services[]      │    │ serviceTimes[]  │          │
│  │ coordinates     │    │ subfilter[]     │    │ contact[]       │          │
│  └─────────────────┘    └─────────────────┘    └─────────────────┘          │
│       Embedded               Embedded               Embedded                │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Kernentitäten:**

| Entität | Beschreibung | Beziehungen |
|---------|--------------|-------------|
| **User** | Registrierte Nutzer der Plattform | n:m zu Organizations (mit Rolle pro Org) |
| **Organization** | Verwaltungsorgan / Tenant | 1:n zu Facilities, 1:n zu User-Memberships |
| **Facility** | Soziale Einrichtung (für Endnutzer sichtbar) | n:1 zu Organization |

**Design-Entscheidungen:**

- **Memberships im User embedded:** Ein Array von `{ organizationId, role }` im User-Dokument.
- **Facility-Details embedded:** Services, Öffnungszeiten und Kontaktdaten sind Teil des Facility-Dokuments. Diese Daten werden immer gemeinsam abgefragt.
- **Referenzen für Kernbeziehungen:** Facility → Organization als ObjectId-Referenz, da Organizations unabhängig existieren und Facilities bei Bedarf migriert werden können.

**Rollen-Hierarchie pro Organization:**

- owner (1x)      → Vollzugriff, kann Organization löschen, Mitglieder verwalten
- maintainer (n)  → Kann Facilities erstellen/bearbeiten, Mitglieder einladen
- staff (n)       → Kann Facilities erstellen/bearbeiten

### Observability

| Tool | Zweck |
|------|-------|
| **Prometheus + Grafana** | API-Metriken (Request Rate, Latenz, Error Rate) mit Alerting |
| **PostHog** | User Analytics und Feature-Tracking |
| **Custom Logger → MongoDB** | Application Logs, einsehbar im Admin-Dashboard |

### Meine Rolle & Arbeitsweise

Als technischer Lead in einem 4-köpfigen Entwicklerteam verantworte ich:

- **Technische Architekturentscheidungen:** Auswahl von Technologien, Definition von Coding Standards
- **Entwicklungsprozesse:** Feature-Branch-Workflow mit Merge Requests und Code Reviews
- **Code Quality:** Code Review, Refactoring, Implementierung von Best Practices
- **Infrastruktur:** CI/CD-Pipelines, Deployment-Strategie, Monitoring-Setup

**Workflow:**
1. Feature als Ticket mit Acceptance Criteria definieren
2. Feature Branch aus `dev` erstellen (`123-feat/feature-name`)
3. Entwicklung mit lokalem Testing
4. Merge Request → Code Review → Staging Deployment
5. QA auf Staging → Merge in `main` → Production Release

### Erkannte Trade-offs & Weiterentwicklung

Als Startup mit schnellen Iterationszyklen treffen wir bewusste Trade-off-Entscheidungen:

| Bereich | Aktueller Stand | Geplante Verbesserung |
|---------|-----------------|----------------------|
| **Type Safety** | Manuelle Typ-Synchronisation FE/BE | Shared Types oder Code-Generierung aus API-Spec |
| **Error Handling** | Verbose Error Messages | Standardisierte Error Codes für i18n und Security |
| **Testing** | Unit Tests im Backend, Basic im Frontend | E2E-Tests |
| **Secrets Management** | Environment Variables | Zentrales Secret Management |
| **Caching** | Noch nicht implementiert | z.B. Redis |
| **DB Performance** | Keine expliziten Indizes | Index-Strategie bei Performance-Bedarf |

Diese bewusste Priorisierung erlaubt uns, schnell auf Nutzerfeedback zu reagieren, während wir technische Schulden dokumentieren und schrittweise adressieren.

## Projekte im Dezember 2025

- **[Innocence Project Deutschland](https://www.innocence-deutschland.de/)** – Implementierung einer Website mit integriertem CMS (Next.js, Payload, S3 Storage) für die deutsche Repräsentation des internationalen [Innocence Network](https://de.wikipedia.org/wiki/Innocence_Project_Deutschland) – einer Non-Profit-Organisation, die sich für die Wiederaufnahme von Fehlverurteilungen einsetzt.

- **CRM-Pipeline-Integration** – Für ein skalierendes deutsches Unternehmen im Bereich Mietwagen, Mobilitätslösungen und Reparaturservices: Implementierung einer Pipeline, die Kundenanfragen von der Website automatisiert als vorkonfigurierte Tickets in deren CRM-System überführt.