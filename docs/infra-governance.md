# EMC Infrastruktur Governance

Status: gültig
Stand: 2026-06-04

---

# 1. Zweck

Dieses Dokument definiert die technischen Governance-Regeln für die Betriebsumgebung der EMC Mitgliederverwaltung.

Ziele:

- nachvollziehbarer Betrieb
- reproduzierbare Deployments
- kontrollierte Änderungen
- dokumentierte Recovery-Prozesse
- minimierte Betriebsrisiken
- klare Verantwortlichkeiten
- standardisierte Monitoring- und Security-Strukturen

Dieses Dokument bildet die übergeordnete Governance für alle produktiven und Entwicklungsumgebungen der EMC Mitgliederverwaltung.

---

# 2. Source of Truth

## 2.1 Grundsatz

Das Git-Repository

```text
emc-infra
```

ist die technische Source of Truth für die Infrastruktur.

Dort werden gepflegt:

- Docker Compose Dateien
- Betriebsdokumentation
- Recovery-Dokumentation
- Security-Dokumentation
- Inventare
- Governance-Dokumente

---

## 2.2 Änderungen

Infrastrukturänderungen werden grundsätzlich zuerst im Repository dokumentiert.

Ausnahmen:

- Break-Glass Situationen
- akute Störungsbehebung

Diese Änderungen sind zeitnah nachzudokumentieren.

---

## 2.3 NAS

Das NAS ist Laufzeitumgebung.

Das NAS ist nicht die primäre Dokumentationsquelle.

Portainer ist ebenfalls nicht die primäre Dokumentationsquelle.

Portainer dient ausschließlich als Deployment-, Monitoring- und Runtime-Werkzeug.

---

# 3. Docker Governance

## 3.1 Compose Standard

Alle produktiven Services werden über Docker Compose betrieben.

Einzelne manuelle Docker-Container sind nicht zulässig.

---

## 3.2 Container-Namen

Containernamen sind explizit festzulegen.

Beispiele:

```text
emc-mitglieder-backend-prod
emc-mitglieder-backend-dev
emc-mitglieder-frontend-prod
emc-mitglieder-frontend-dev
mariadb
phpmyadmin
portainer
uptime-kuma
syncthing
```

---

## 3.3 Netzwerke

Produktive Container werden ausschließlich über definierte Docker-Netzwerke verbunden.

Standardnetz:

```text
emc_net
```

Ausnahmen sind dokumentationspflichtig.

Beispiel:

mariadb_default

---

## 3.4 Volumes

Persistente Daten dürfen niemals ausschließlich im Container liegen.

Alle produktiven Daten werden über persistente Volumes oder Bind Mounts gespeichert.

---

## 3.5 Portainer

Portainer dient der operativen Verwaltung und dem Redeployment von Docker-Stacks.

Verbindlicher Grundsatz:

```text
EMC-INFRA
=
Source of Truth

Portainer
=
Runtime- und Deployment-Werkzeug
```

Änderungen an Compose-Dateien erfolgen zuerst in EMC-INFRA.

Anschließend werden die Änderungen in die zugehörigen Portainer-Stacks übernommen.

Direkte Architekturänderungen ausschließlich in Portainer sind nicht zulässig.

Zielzustand:

```text
EMC-INFRA
=
Portainer Stack
=
Container Runtime
```

---

# 4. Deployment Governance

## 4.1 Frontend

Frontend-Deployments erfolgen image-basiert.

Standardprozess:

Git Repository
→ npm run build
→ Docker Image Build (linux/arm64)
→ docker save
→ Artefaktübertragung auf NAS
→ docker load
→ Portainer Redeploy

Versionsstandard:

DEV verwendet Snapshot-Images:

X.Y.Z-SNAPSHOT

PROD verwendet Release-Images:

X.Y.Z

Beispiel:

DEV:
1.1.2-SNAPSHOT

PROD:
1.1.1

Nach einem Release wird die Entwicklungsversion auf die nächste Snapshot-Version angehoben.

Die im Frontend angezeigte Versionsnummer wird aus package.json zum Build-Zeitpunkt erzeugt.

Vor Release-Builds ist die Version daher explizit zu prüfen.

Frontend-Deployments auf dem NAS verwenden ARM64-kompatible Docker Images.

---

## 4.2 Backend

Backend-Deployments erfolgen über:

```text
GitHub Actions
Build
Docker Image
Deployment auf NAS
```

---

## 4.3 Rollback

Rollback muss jederzeit möglich sein.

Vor produktiven Änderungen sind vorhandene Artefakte aufzubewahren.

---

# 5. Backup Governance

## 5.1 Datenbank

MariaDB wird regelmäßig gesichert.

Sicherungen müssen:

- erfolgreich erstellt werden
- lesbar sein
- aktuell sein

---

## 5.2 Recovery

Backup ohne getestete Wiederherstellung gilt nicht als verifiziert.

Restore-Tests sind Bestandteil der Betriebsdokumentation.

---

## 5.3 USB-Offsite-Sicherung

Die zusätzliche USB-Sicherung ist Bestandteil der Recovery-Strategie.

Die USB-Sicherung wird bewusst als Offline-Backup betrieben.

Grundsätze:

- USB-Datenträger sind außerhalb des Backup-Prozesses nicht dauerhaft mit dem NAS verbunden.
- Die externe Sicherung ergänzt die täglichen internen Datenbank-Backups.
- Die externe Sicherung wird im Rahmen eines dokumentierten Wochenprozesses erstellt und geprüft.
- Die USB-Sicherung ist nicht Bestandteil des permanent verfügbaren Betriebsbestandes.

---

# 6. Security Governance

## 6.1 Passwortmanagement

Alle produktiven Zugangsdaten werden in KeePass verwaltet.

Klartextpasswörter dürfen nicht dokumentiert werden.

---

## 6.2 Passwortstandard

Passwörter:

```text
mindestens 26 Zeichen
empfohlen 28 Zeichen
```

---

## 6.3 Rollenmodell

MariaDB-Zugriffe erfolgen über dedizierte Benutzerrollen.

Grundsätze:

- Least Privilege
- Trennung von Runtime und Administration
- Trennung von DEV und PROD
- Trennung von Access und Backend

---

## 6.4 Root

MariaDB Root ist Break-Glass-Zugang.

Root wird nicht für den Regelbetrieb verwendet.

---

# 7. Datenbank Governance

## 7.1 Namenskonvention

Seit Phase 11 gilt verbindlich:

```text
<fachbereich>_<umgebung>
```

Beispiele:

```text
emc_mitglieder_dev
emc_mitglieder_prod

emc_finanzen_dev
emc_finanzen_prod
```

---

## 7.2 Umgebungskennzeichnung

Jede produktive und nichtproduktive Datenbank muss ihre Umgebung im Datenbanknamen tragen.

Nicht zulässig:

```text
emc_mitglieder
emc_finanzen
```

Ausnahme:

```text
temporäre Legacy-Datenbanken
für dokumentierte Rollback-Zwecke
```

---

## 7.3 Datenbankrollen

Datenbankrollen werden umgebungsbezogen vergeben.

Beispiele:

```text
role_emc_mitglieder_dev_rw
role_emc_mitglieder_prod_rw

role_emc_access_mitglieder_dev_rw
role_emc_access_mitglieder_prod_rw
```

Ziel:

```text
eindeutige Zuordnung
keine Umgebungsvermischung
```

---

## 7.4 Access-Kompatibilität

Die Datenbank-Namenskonvention muss mit der DSN-less Access-Architektur kompatibel bleiben.

Produktive Konfigurationen werden über:

```text
dbconfig_prod.ini
```

bereitgestellt.

Entwicklungskonfigurationen über:

```text
dbconfig.ini
```

---

# 8. Monitoring Governance

## 8.1 Ziel

Monitoring dient nicht primär der Containerüberwachung.

Monitoring dient der frühzeitigen Erkennung von:

- Betriebsstörungen
- Datenverlust-Risiken
- Recovery-Risiken
- Synchronisationsproblemen
- Infrastrukturfehlern

---

## 8.2 Monitoring-Prinzip

Fachliche Prüfungen sind technischen Prüfungen vorzuziehen.

Beispiel:

Nicht nur:

```text
Port 3306 erreichbar
```

sondern:

```text
MariaDB Login erfolgreich
```

---

## 8.3 Monitoring-Struktur

Die Uptime-Kuma-Monitore werden nach Betriebsrelevanz gruppiert.

### Anwendungen

```text
APP Backend DEV
APP EMC Mitglieder Backend DEV Health
APP EMC Mitglieder Backend DEV Readiness

APP Backend PROD
APP EMC Mitglieder Backend PROD Health
APP EMC Mitglieder Backend PROD Readiness

APP Frontend DEV
APP Frontend PROD
```

### Daten & Recovery

```text
DATA DB Backup Healthcheck
DATA External DB Backup Healthcheck
DATA MariaDB Login
DATA Syncthing NAS
```

Backup-Monitoring wird getrennt durchgeführt:

- DATA DB Backup Healthcheck überwacht die täglichen internen MariaDB-Backups.
- DATA External DB Backup Healthcheck überwacht das wöchentliche Offline-Backup auf externem USB-Datenträger.

Die Trennung verhindert Fehlalarme, da externe USB-Datenträger im Regelbetrieb nicht dauerhaft mit dem NAS verbunden sind.

### Infrastruktur

```text
INFRA mariadb-backup
INFRA phpMyAdmin
INFRA Portainer
```

Für Spring-Boot-Backends gilt zusätzlich:

```text
fachliche Betriebsbereitschaft
vor
reiner Container-Erreichbarkeit
```

Backend-Monitoring erfolgt bevorzugt über Spring Boot Actuator.

Mindestanforderungen:

```text
/actuator/health
/actuator/health/readiness
```

Healthchecks werden bevorzugt über interne Docker-Netzwerke ausgeführt.

Readiness-Monitoring besitzt höhere Betriebsrelevanz als reine Container- oder Portprüfungen, da zusätzlich die Datenbankerreichbarkeit bewertet wird.

---

## 8.4 Recovery-Relevante Monitore

Besondere Bedeutung besitzen:

```text
DATA DB Backup Healthcheck
DATA External DB Backup Healthcheck
DATA MariaDB Login
DATA Syncthing NAS
```

Diese Monitore überwachen unmittelbar:

- Datenverfügbarkeit
- Backupfähigkeit
- Wiederherstellbarkeit
- Replikation kritischer Sicherheitsartefakte

---

## 8.5 Neue Dienste

Jeder neue produktive Dienst muss vor Inbetriebnahme bewertet werden hinsichtlich:

- Monitoring
- Backup
- Recovery
- Security
- Dokumentation

---

# 9. Dokumentations Governance

## 9.1 Pflichtdokumentation

Folgende Bereiche sind aktuell zu halten:

- Stack Inventory
- Credential Inventory
- Runtime Secret Mapping
- Recovery Dokumentation
- Governance Dokumentation

---

## 9.2 Nachvollziehbarkeit

Technische Entscheidungen sind nachvollziehbar zu dokumentieren.

---

## 9.3 Ergebnisdokumente

Größere Infrastrukturmaßnahmen werden durch eigene Ergebnisdokumente dokumentiert.

Diese dienen als Projekt- und Entscheidungsnachweis.

---

# 10. Änderungsmanagement

## 10.1 Grundsatz

Änderungen erfolgen kontrolliert.

Vor Änderungen sind zu prüfen:

- Auswirkungen
- Abhängigkeiten
- Recovery-Möglichkeiten

---

## 10.2 Produktionssysteme

Änderungen an PROD erfolgen bewusst und nachvollziehbar.

---

## 10.3 Nachdokumentation

Jede dauerhafte Infrastrukturänderung wird in EMC-INFRA dokumentiert.

Falls Portainer verwendet wird, sind zugehörige Stackdefinitionen nachzuführen.

EMC-INFRA, Portainer und Runtime dürfen dauerhaft nicht voneinander abweichen.

---

# 11. Gültigkeit

Dieses Dokument gilt für die gesamte Infrastruktur der EMC Mitgliederverwaltung auf dem UGREEN NAS einschließlich:

- DEV
- PROD
- MariaDB
- Frontend
- Backend
- Backup
- Monitoring
- Recovery
- Syncthing
- Security-Struktur
- zukünftige Erweiterungen

---

# Änderungslog

| Datum      | Änderung                                                                                                                                           |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2026-06-03 | Initiale Governance-Fassung                                                                                                                        |
| 2026-06-04 | Datenbank Governance ergänzt, verbindliche Naming-Konvention `<fachbereich>_<umgebung>` eingeführt, Access- und Rollenmodellbezug nachgeführt      |
| 2026-06-08 | Portainer Governance ergänzt, Synchronisationsprinzip EMC-INFRA = Portainer = Runtime verbindlich festgelegt                                       |
| 2026-06-12 | BL-007 Backend Healthchecks: Spring Boot Actuator als Standard für Backend-Monitoring eingeführt, DEV- und PROD-Health-/Readiness-Monitore ergänzt |
| 2026-06-14 | Trennung von internem Backup-Monitoring und externem Offline-Backup-Monitoring dokumentiert                                                        |
| 2026-06-15 | BL-008 Frontend Deployment standardisiert: DEV und PROD verwenden image-basiertes Deployment, Snapshot-/Release-Strategie verbindlich festgelegt   |

```

```
