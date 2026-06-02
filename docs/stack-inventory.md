# Stack Inventory

## Zweck

Dieses Dokument beschreibt den aktuell produktiven Betriebsstand der EMC Infrastruktur einschließlich aller produktiv betriebenen Docker-Stacks, deren Zweck, Betriebsstatus und wesentlichen Betriebsparameter.

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

# Detailinventar

## mariadb

**Zweck**

Zentrale MariaDB Datenbank.

**Image**

```text
mariadb:11
```

**Container**

```text
mariadb
```

**Ports**

```text
3306:3306
```

**Volumes**

```text
mariadb_mariadb_data -> /var/lib/mysql
```

**Netzwerke**

```text
mariadb_default
emc_net
```

**Konfiguration**

```text
compose/mariadb/docker-compose.yml
```

**Bemerkungen**

- Host-Port aktuell fachlich erforderlich wegen Access
- Root Passwort via .env
- Access-ODBC ist produktive Abhängigkeit
- Runtime- und Access-Benutzer vollständig getrennt
- Rollenmodell Phase 9 produktiv umgesetzt
- root@% entfernt
- root@localhost verbleibt als Break-Glass-Zugang
- Primärer DBA: JoergTitz
- phpMyAdmin verwendet dedizierten GUI-Admin (emc_phpmyadmin_admin)

---

## mariadb-backup

**Zweck**

Automatisierte MariaDB Dumps.

**Image**

```text
fradelg/mysql-cron-backup
```

**Container**

```text
mariadb-backup
```

**Ports**

Keine.

**Volumes**

```text
/volume1/docker/backups/mariadb:/backup
```

**Netzwerke**

```text
mariadb_default
```

**Konfiguration**

```text
compose/mariadb-backup/docker-compose.yml
```

**Bemerkungen**

- täglicher Backupjob
- Backup-Ziel entspricht Filesystem-Standard
- dedizierter DB-User emc_backup
- Bestandteil des Recovery-Konzepts

---

## phpmyadmin

**Zweck**

DB Administration.

**Image**

```text
phpmyadmin:latest
```

**Container**

```text
phpmyadmin
```

**Ports**

```text
8080:80
```

**Netzwerke**

```text
mariadb_default
```

**Konfiguration**

```text
compose/phpmyadmin/docker-compose.yml
```

**Bemerkungen**

- On-Demand Nutzung empfohlen
- dauerhaft produktiv toleriert
- phpMyAdmin 5.2.3 zeigt Rollen-Inkompatibilität bei rein rollenbasierten Admin-Benutzern
- Standardzugang für GUI-Administration: emc_phpmyadmin_admin
- Datenbankadministration erfolgt primär über JoergTitz (CLI)

---

## uptime-kuma

**Zweck**

Monitoring.

**Image**

```text
louislam/uptime-kuma:2
```

**Container**

```text
uptime-kuma
```

**Ports**

```text
3001:3001
```

**Volumes**

```text
/volume1/docker/volumes/uptime-kuma/data:/app/data
/var/run/docker.sock:/var/run/docker.sock:ro
```

**Netzwerke**

```text
uptime-kuma_default
emc_net
```

**Konfiguration**

```text
compose/uptime-kuma/docker-compose.yml
```

**Bemerkungen**

- Compose und Runtime sind konsistent
- Monitoring Bestandteil des Recovery-Konzepts

---

## portainer

**Zweck**

Deploymenthilfe / Runtime Verwaltung.

**Image**

```text
portainer/portainer-ce:latest
```

**Container**

```text
portainer
```

**Ports**

```text
9000:9000
9443 intern aktiv
```

**Volumes**

```text
/volume1/docker/volumes/portainer/data:/data
/var/run/docker.sock:/var/run/docker.sock
```

**Konfiguration**

```text
compose/portainer/docker-compose.yml
```

**Bemerkungen**

- Runtime Verwaltung
- keine Primärquelle
- Git bleibt Source of Truth

---

## syncthing

**Zweck**

Synchronisation der zentralen Security- und Recovery-Struktur.

**Image**

```text
syncthing/syncthing:latest
```

**Container**

```text
syncthing
```

**Ports**

```text
8384:8384
22000:22000/tcp
22000:22000/udp
21027:21027/udp
```

**Volumes**

```text
/volume1/docker/volumes/syncthing/config:/var/syncthing/config
/volume1/home/JaitiNissi1968/Security:/var/syncthing/Security
```

**Konfiguration**

```text
compose/syncthing/docker-compose.yml
```

**Architektur**

```text
Laptop (Source of Truth)
        ⇅
     Syncthing
    ↙       ↘
 NAS       Desktop
```

**Pfade**

```text
Laptop:
D:\Security

NAS:
/volume1/home/JaitiNissi1968/Security

Desktop:
D:\Security
```

**Bemerkungen**

- Laptop ist die führende Arbeitskopie.
- NAS dient als Replik und Recovery-Kopie.
- Desktop dient als zusätzlicher Recovery-Client.
- Dateiversionierung über `.stversions` aktiv.
- GUI-Authentifizierung bewusst deaktiviert.
- Windows-Clients starten Syncthing automatisch im Hintergrund.
- Syncthing ersetzt kein Backup.
- KeePass-Datenbank und Security-Artefakte werden über Syncthing repliziert.
- Recovery-Konzept basiert auf Laptop, NAS und Desktop.

---

## emc-mitglieder-backend-dev

**Zweck**

DEV Backend.

**Image**

```text
emc-mitglieder-backend:emc-mitglieder-backend:dev
```

**Container**

```text
emc-mitglieder-backend-dev
```

**Ports**

Keine Host-Ports.

**Netzwerke**

```text
emc_net
```

**Konfiguration**

```text
compose/emc-mitglieder-backend-dev/docker-compose.yml
```

---

## emc-mitglieder-backend-prod

**Zweck**

PROD Backend.

**Image**

```text
emc-mitglieder-backend:emc-mitglieder-backend:prod
```

**Container**

```text
emc-mitglieder-backend-prod
```

**Ports**

Keine Host-Ports.

**Netzwerke**

```text
emc_net
```

**Konfiguration**

```text
compose/emc-mitglieder-backend-prod/docker-compose.yml
```

---

## emc-mitglieder-frontend-dev

**Zweck**

DEV Frontend.

**Image**

```text
emc-mitglieder-frontend:emc-mitglieder-frontend:1.1.1-SNAPSHOT-dev
```

**Container**

```text
emc-mitglieder-frontend-dev
```

**Ports**

```text
8082:80
```

**Netzwerke**

```text
emc_net
```

**Konfiguration**

```text
compose/emc-mitglieder-frontend-dev/docker-compose.yml
```

**Bemerkungen**

- image-basiertes Deployment
- Dockerfile versioniert
- nginx-Konfiguration versioniert

---

## emc-mitglieder-frontend-prod

**Zweck**

PROD Frontend.

**Image**

```text
nginx:alpine
```

**Container**

```text
emc-mitglieder-frontend-prod
```

**Ports**

```text
9082:80
```

**Volumes**

```text
/volume1/docker/build/mitglieder-frontend-prod/dist
/volume1/docker/build/mitglieder-frontend-prod/nginx.conf
```

**Netzwerke**

```text
emc_net
```

**Konfiguration**

```text
compose/emc-mitglieder-frontend-prod/docker-compose.yml
```

**Bemerkungen**

- bind-mount Deployment
- Migration auf image-basiertes Deployment noch ausstehend

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

- Monitoring Ausbau / echte Healthchecks
- DB Naming Migration
- Frontend PROD Image Migration
- Syncthing-Hardening (optional)
