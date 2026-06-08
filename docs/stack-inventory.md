# Stack Inventory

Status: Phase 12 abgeschlossen
Ablageziel: `emc-infra/docs/stack-inventory.md`

---

# Zweck

Dieses Dokument beschreibt den aktuell produktiven Betriebsstand der EMC Infrastruktur einschließlich aller produktiv betriebenen Docker-Stacks, deren Zweck, Betriebsstatus und wesentlichen Betriebsparameter.

Zusätzlich dokumentiert dieses Inventar die Monitoring-Einbindung, Betriebsrelevanz und Recovery-Relevanz der einzelnen Komponenten.

---

# Stack Übersicht

| Stack                        | Zweck                                | Status                |
| ---------------------------- | ------------------------------------ | --------------------- |
| mariadb                      | zentrale Datenbank                   | produktiv             |
| mariadb-backup               | Datenbank-Backup                     | produktiv             |
| phpmyadmin                   | DB-Webadministration                 | produktiv / toleriert |
| uptime-kuma                  | Monitoring                           | produktiv             |
| portainer                    | Deploymenthilfe                      | produktiv             |
| syncthing                    | Security-Verzeichnis Synchronisation | produktiv             |
| emc-mitglieder-backend-dev   | Mitglieder Backend DEV               | produktiv             |
| emc-mitglieder-backend-prod  | Mitglieder Backend PROD              | produktiv             |
| emc-mitglieder-frontend-dev  | Mitglieder Frontend DEV              | produktiv             |
| emc-mitglieder-frontend-prod | Mitglieder Frontend PROD             | produktiv             |

---

# Monitoring Architektur

Seit Phase 10 wird Monitoring nach Betriebsrelevanz strukturiert.

## Anwendungen

```text
APP Backend DEV
APP Backend PROD
APP Frontend DEV
APP Frontend PROD
```

Zweck:

- Verfügbarkeit der Anwendungen
- Erreichbarkeit der Benutzeroberflächen
- Erreichbarkeit der Backend-Services

---

## Daten & Recovery

```text
DATA DB Backup Healthcheck
DATA MariaDB Login
DATA Syncthing NAS
```

Zweck:

- Datenverfügbarkeit
- Wiederherstellbarkeit
- Backupfähigkeit
- Replikationsfähigkeit

Diese Gruppe besitzt die höchste Recovery-Relevanz.

---

## Infrastruktur

```text
INFRA MariaDB
INFRA mariadb-backup
INFRA phpMyAdmin
INFRA Portainer
```

Zweck:

- technische Infrastrukturüberwachung
- Hilfsdienste
- Verwaltungsdienste

---

# Detailinventar

## mariadb

### Zweck

Zentrale MariaDB Datenbank.

### Image

```text
mariadb:11
```

### Container

```text
mariadb
```

### Ports

```text
3306:3306
```

### Volumes

```text
mariadb_mariadb_data -> /var/lib/mysql
```

### Netzwerke

```text
mariadb_default
emc_net
```

### Konfiguration

```text
compose/mariadb/docker-compose.yml
```

### Monitoring

```text
DATA MariaDB Login
INFRA MariaDB
```

### Bemerkungen

- Host-Port aktuell fachlich erforderlich wegen Access
- Root Passwort via .env
- Access-ODBC ist produktive Abhängigkeit
- Runtime- und Access-Benutzer vollständig getrennt
- Rollenmodell Phase 9 produktiv umgesetzt
- root@% entfernt
- root@localhost verbleibt als Break-Glass-Zugang
- Primärer DBA: JoergTitz
- phpMyAdmin verwendet dedizierten GUI-Admin (`emc_phpmyadmin_admin`)
- Phase 10 führte zusätzlich einen Login-basierten Monitoring-Check ein
- INFRA MariaDB dient aktuell noch als Parallelmonitor während der Beobachtungsphase
- Phase 11 führte die Standardisierung der Datenbanknamen auf das Schema <fachbereich>\_<umgebung> durch
- Produktive Datenbanken:
  - emc_mitglieder_prod
  - emc_finanzen_prod
- Entwicklungsdatenbanken:
  - emc_mitglieder_dev
  - emc_finanzen_dev
- Legacy-Datenbanken emc_mitglieder und emc_finanzen verbleiben temporär als Rollback-Sicherung

---

## mariadb-backup

### Zweck

Automatisierte MariaDB Dumps.

### Image

```text
fradelg/mysql-cron-backup
```

### Container

```text
mariadb-backup
```

### Ports

Keine.

### Volumes

```text
/volume1/docker/backups/mariadb:/backup
```

### Netzwerke

```text
mariadb_default
```

### Konfiguration

```text
compose/mariadb-backup/docker-compose.yml
```

### Monitoring

```text
INFRA mariadb-backup
DATA DB Backup Healthcheck
```

### Bemerkungen

- täglicher Backupjob
- Backup-Ziel entspricht Filesystem-Standard
- dedizierter DB-User `emc_backup`
- Bestandteil des Recovery-Konzepts
- Container-Monitor prüft nur Laufzeitstatus
- Recovery-Relevanz wird primär über DATA DB Backup Healthcheck überwacht

---

## phpmyadmin

### Zweck

DB Administration.

### Image

```text
phpmyadmin:latest
```

### Container

```text
phpmyadmin
```

### Ports

```text
8080:80
```

### Netzwerke

```text
mariadb_default
```

### Konfiguration

```text
compose/phpmyadmin/docker-compose.yml
```

### Monitoring

```text
INFRA phpMyAdmin
```

### Bemerkungen

- On-Demand Nutzung empfohlen
- dauerhaft produktiv toleriert
- phpMyAdmin 5.2.3 zeigt Rollen-Inkompatibilität bei rein rollenbasierten Admin-Benutzern
- Standardzugang für GUI-Administration: `emc_phpmyadmin_admin`
- Datenbankadministration erfolgt primär über JoergTitz (CLI)

---

## uptime-kuma

### Zweck

Monitoring.

### Image

```text
louislam/uptime-kuma:2
```

### Container

```text
uptime-kuma
```

### Ports

```text
3001:3001
```

### Volumes

```text
/volume1/docker/volumes/uptime-kuma/data:/app/data
/var/run/docker.sock:/var/run/docker.sock:ro
```

### Netzwerke

```text
uptime-kuma_default
emc_net
```

### Konfiguration

```text
compose/uptime-kuma/docker-compose.yml
```

### Monitoring

Selbstüberwachung indirekt über Plattformüberwachung.

### Bemerkungen

- Compose und Runtime sind konsistent
- Monitoring Bestandteil des Recovery-Konzepts
- Monitoring-Gruppenstruktur Phase 10 eingeführt
- MariaDB Login Monitoring ergänzt
- Syncthing Monitoring ergänzt

Phase 10 führte eine gruppierte Monitoring-Struktur ein:

- Anwendungen
- Daten & Recovery
- Infrastruktur

sowie die Monitore:

- DATA MariaDB Login
- DATA Syncthing NAS

---

## portainer

### Zweck

Deploymenthilfe / Runtime Verwaltung.

### Image

```text
portainer/portainer-ce:latest
```

### Container

```text
portainer
```

### Ports

```text
9000:9000
9443 intern aktiv
```

### Volumes

```text
/volume1/docker/volumes/portainer/data:/data
/var/run/docker.sock:/var/run/docker.sock
```

### Monitoring

```text
INFRA Portainer
```

### Bemerkungen

- Runtime Verwaltung
- Deployment-Werkzeug
- keine Primärquelle
- EMC-INFRA bleibt Source of Truth
- Portainer-Stacks wurden in Phase 12 auf den dokumentierten Compose-Stand synchronisiert
- Redeployments erfolgen bevorzugt über Portainer
- Änderungen an Compose-Dateien erfolgen ausschließlich in EMC-INFRA und werden anschließend nach Portainer übernommen
- Git bleibt Source of Truth

---

## syncthing

### Zweck

Synchronisation der zentralen Security- und Recovery-Struktur.

### Image

```text
syncthing/syncthing:latest
```

### Container

```text
syncthing
```

### Ports

```text
8384:8384
22000:22000/tcp
22000:22000/udp
21027:21027/udp
```

### Volumes

```text
/volume1/docker/volumes/syncthing/config:/var/syncthing/config
/volume1/home/JaitiNissi1968/Security:/var/syncthing/Security
```

### Konfiguration

```text
compose/syncthing/docker-compose.yml
```

### Monitoring

```text
DATA Syncthing NAS
```

### Architektur

```text
Laptop (Source of Truth)
        ⇅
     Syncthing
    ↙       ↘
 NAS       Desktop
```

### Pfade

```text
Laptop:
D:\Security

NAS:
/volume1/home/JaitiNissi1968/Security

Desktop:
D:\Security
```

### Bemerkungen

- Laptop ist die führende Arbeitskopie
- NAS dient als Replik und Recovery-Kopie
- Desktop dient als zusätzlicher Recovery-Client
- Dateiversionierung über `.stversions` aktiv
- GUI-Authentifizierung bewusst deaktiviert
- Phase 10 integrierte Syncthing in das produktive Monitoring
- Syncthing ersetzt kein Backup
- durch Portainer als "Limited Stack" erkannt
- bewusst außerhalb von Portainer erstellt
- keine operative Einschränkung
- kein Handlungsbedarf

---

## emc-mitglieder-backend-dev

### Zweck

DEV Backend.

### Image

```text
emc-mitglieder-backend:dev
```

### Container

```text
emc-mitglieder-backend-dev
```

### Ports

Keine Host-Ports.

### Netzwerke

```text
emc_net
```

### Konfiguration

```text
compose/emc-mitglieder-backend-dev/docker-compose.yml
```

### Monitoring

```text
APP Backend DEV
```

---

## emc-mitglieder-backend-prod

### Zweck

PROD Backend.

### Image

```text
emc-mitglieder-backend:prod
```

### Container

```text
emc-mitglieder-backend-prod
```

### Ports

Keine Host-Ports.

### Netzwerke

```text
emc_net
```

### Konfiguration

```text
compose/emc-mitglieder-backend-prod/docker-compose.yml
```

### Monitoring

```text
APP Backend PROD
```

### Bemerkungen

- DB_URL verweist seit Phase 11 auf emc_mitglieder_prod
- Runtime-User bleibt emc_mitglieder_prod_rw
- Runtime-Test gegen Produktivdatenbank erfolgreich validiert

---

## emc-mitglieder-frontend-dev

### Zweck

DEV Frontend.

### Image

```text
emc-mitglieder-frontend:1.1.1-SNAPSHOT-dev
```

### Container

```text
emc-mitglieder-frontend-dev
```

### Ports

```text
8082:80
```

### Netzwerke

```text
emc_net
```

### Konfiguration

```text
compose/emc-mitglieder-frontend-dev/docker-compose.yml
```

### Monitoring

```text
APP Frontend DEV
```

### Bemerkungen

- image-basiertes Deployment
- Dockerfile versioniert
- nginx-Konfiguration versioniert

---

## emc-mitglieder-frontend-prod

### Zweck

PROD Frontend.

### Image

```text
nginx:alpine
```

### Container

```text
emc-mitglieder-frontend-prod
```

### Ports

```text
9082:80
```

### Volumes

```text
/volume1/docker/build/mitglieder-frontend-prod/dist
/volume1/docker/build/mitglieder-frontend-prod/nginx.conf
```

### Netzwerke

```text
emc_net
```

### Konfiguration

```text
compose/emc-mitglieder-frontend-prod/docker-compose.yml
```

### Monitoring

```text
APP Frontend PROD
```

### Bemerkungen

- bind-mount Deployment
- Sonderfall der Infrastruktur
- in Phase 12 erneut bewertet
- aktuell bewusst beibehalten
- Migration auf image-basiertes Deployment noch ausstehend

---

# Datenbankinventar

## Produktive Datenbanken

```text
emc_mitglieder_prod
emc_finanzen_prod
```

## Entwicklungsdatenbanken

```text
emc_mitglieder_dev
emc_finanzen_dev
```

## Temporäre Legacy-Datenbanken

```text
emc_mitglieder
emc_finanzen
```

Status:

nicht mehr produktiv genutzt
dienen temporär als Rollback-Sicherung nach Phase 11
Löschung erst nach gesonderter Freigabe

---

# Portainer Governance

Seit Phase 12 gilt verbindlich:

```text
EMC-INFRA
=
Source of Truth

Portainer
=
Deployment
Monitoring
Runtime-Verwaltung
```

Änderungen an Docker-Stacks erfolgen grundsätzlich zuerst in EMC-INFRA.

Anschließend werden die Änderungen in die zugehörigen Portainer-Stacks übernommen.

Direkte Architekturänderungen ausschließlich in Portainer sind nicht zulässig.

Zielzustand:

```text
EMC-INFRA
=
Portainer Stack Definition
=
Container Runtime
```

---

# Historische Altstrukturen

## Home-basierte Altstruktur

```text
/volume1/home/JaitiNissi1968/docker
```

Historische Altstruktur.

Produktive Daten wurden weitgehend nach:

```text
/volume1/docker
```

migriert.

Verbleibende Nutzung nur in dokumentierten Ausnahmefällen.

---

# Offene spätere Themen

Nicht Bestandteil der bisherigen Phasen:

- Prüfung der Entfernung des redundanten INFRA MariaDB Portmonitors
  nach erfolgreicher Beobachtungsphase des DATA MariaDB Login Monitors.
- Frontend PROD Image Migration
- Syncthing-Hardening (optional)
- Erweiterte fachliche Backend-Healthchecks
- Spring Boot Actuator Einführung für Monitoring-Zwecke
- Weitere Recovery-orientierte Healthchecks

---

# Änderungslog

| Datum      | Änderung                                                                                             |
| ---------- | ---------------------------------------------------------------------------------------------------- |
| 2026-05-26 | Initiale Inventarisierung                                                                            |
| 2026-05-30 | Syncthing ergänzt                                                                                    |
| 2026-06-01 | Rollenmodell und Security-Härtung nachgeführt                                                        |
| 2026-06-03 | Monitoring-Gruppen, DATA MariaDB Login und DATA Syncthing NAS ergänzt                                |
| 2026-06-04 | Datenbank-Naming-Migration auf \_prod abgeschlossen, Backup- und Runtime-Konfiguration nachgeführt   |
| 2026-06-08 | Phase 12 Portainer-Konsistenzprüfung, Governance-Präzisierung und Stack-Synchronisation durchgeführt |
