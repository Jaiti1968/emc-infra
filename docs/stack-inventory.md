# Stack Inventory

## Zweck

Dieses Dokument beschreibt den aktuell dokumentierten Betriebsstand der EMC Docker-/NAS-Infrastruktur.

Stand: Phase 1 Governance / Compose Standardisierung

Hinweis:

Dies ist ein Ist-Zustandsinventar.

Nicht alle hier dokumentierten Konfigurationen entsprechen bereits dem Zielstandard.

---

# Stack Übersicht

| Stack                        | Zweck                    | Status                |
| ---------------------------- | ------------------------ | --------------------- |
| mariadb                      | zentrale Datenbank       | produktiv             |
| mariadb-backup               | Datenbank-Backup         | produktiv             |
| phpmyadmin                   | DB-Webadministration     | produktiv / toleriert |
| uptime-kuma                  | Monitoring               | produktiv             |
| portainer                    | Deploymenthilfe          | produktiv             |
| emc-mitglieder-backend-dev   | Mitglieder Backend DEV   | produktiv             |
| emc-mitglieder-backend-prod  | Mitglieder Backend PROD  | produktiv             |
| emc-mitglieder-frontend-dev  | Mitglieder Frontend DEV  | produktiv             |
| emc-mitglieder-frontend-prod | Mitglieder Frontend PROD | produktiv             |

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
- Root Passwort aktuell via .env

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
/volume1/home/JaitiNissi1968/docker/backups/mariadb:/backup
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
- TEST DB aktuell nicht enthalten
- Backup-Ziel noch Altstruktur

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

Aktiv toleriert, Zielarchitektur später prüfen.

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
/volume1/home/JaitiNissi1968/docker/volumes/uptime-kuma/data:/app/data
/var/run/docker.sock:/var/run/docker.sock:ro
```

**Netzwerke**

Runtime prüfen:

```text
uptime-kuma_default
emc_net
```

**Konfiguration**

```text
compose/uptime-kuma/docker-compose.yml
```

**Bemerkungen**

Runtime-Netzwerkzustand weicht vom Compose-Stand ab.

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
/var/run/docker.sock:/var/run/docker.sock
../../volumes/portainer/data:/data
```

**Konfiguration**

```text
compose/portainer/docker-compose.yml
```

**Bemerkungen**

Historische Home-basierte Volume-Struktur.

---

## emc-mitglieder-backend-dev

**Zweck**

DEV Backend.

**Image**

```text
emc-mitglieder-backend:dev
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
emc-mitglieder-backend:prod
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
nginx:alpine
```

**Container**

```text
emc-mitglieder-frontend-dev
```

**Ports**

```text
8082:80
```

**Volumes**

```text
/volume1/docker/build/mitglieder-frontend-dev/dist
/volume1/docker/build/mitglieder-frontend-dev/nginx.conf
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

Noch bind-mount Deployment.

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

Noch bind-mount Deployment.

---

# Bekannte Altlasten

## Docker-Netze

```text
emc-net
phpmyadmin_default
emc-mitglieder-backend-dev_default
```

## Historische Home-Strukturen

```text
/volume1/home/JaitiNissi1968/docker
```

Teilweise noch produktiv genutzt.

---

# Offene spätere Themen

Nicht Bestandteil Phase 1:

- Secret Migration
- Credential Rotation
- Netzwerkstandardisierung
- Backup-Umbau
- Restore-Tests
- Frontend Image Migration
- DB Naming
- Altlastenbereinigung
