# Bare Metal NAS Recovery Runbook

Status: Phase 11 abgeschlossen

## Zweck

Worst-Case Recovery:

```text
NAS vollständig ausgefallen
```

Ziel:

Vollständige Wiederherstellung der EMC Infrastruktur auf Ersatzhardware oder einem neu installierten NAS.

---

## Voraussetzungen

Benötigt:

- Ersatzhardware oder Ersatz-NAS
- Netzwerkzugriff
- Break-Glass Dokumentation
- externe Backups
- GitHub Zugriff
- KeePass Zugriff
- Zugriff auf Security-Verzeichnis

---

## Recovery Reihenfolge

### 1. Basisbetrieb herstellen

- NAS initialisieren
- Speicher konfigurieren
- Netzwerk konfigurieren
- SSH aktivieren
- Docker installieren

---

### 2. Security-Artefakte herstellen

Primärquelle:

```text
D:\Security
```

Falls verfügbar:

- KeePass Datenbank
- Fritz!Box Backup
- Recovery-Dokumentation
- Credential-Dokumentation

Wichtig:

```text
Laptop = Source of Truth
```

---

### 3. Credentials herstellen

- KeePass öffnen
- Break-Glass prüfen
- Admin-Zugänge validieren

Mindestens prüfen:

```text
root@localhost
JoergTitz
emc_phpmyadmin_admin
JaitiNissi1968
```

---

### 4. Git Recovery

Repositories klonen:

- emc-infra
- emc-mitglieder-backend
- emc-mitglieder-frontend
- emc-dokumentation

Zusätzlich sind die archivierten Docker-Images der Eigenentwicklungen bereitzustellen.

Ablage:

/volume1/docker/build

---

### 5. Betriebsstruktur herstellen

Standardstruktur:

```text
/volume1/docker
```

Unterstruktur:

```text
compose
docs
runbooks
recovery
monitoring
backups
secrets
volumes
build
```

---

### 6. Secrets Restore

Wiederherstellen:

```text
.env
```

aus:

- Backup
- dokumentierter Recoveryquelle
- KeePass

---

### 7. Compose Restore

Reihenfolge:

```text
MariaDB
Uptime Kuma
Portainer
Syncthing
Backend DEV
Backend PROD
Frontend DEV
Frontend PROD
optional phpMyAdmin
```

Hinweis:

Vor dem Start der produktiven Anwendungs-Stacks müssen die zugehörigen Docker Images auf dem NAS verfügbar sein.

Dies betrifft insbesondere:

- emc-mitglieder-backend-dev
- emc-mitglieder-backend-prod
- emc-mitglieder-frontend-dev
- emc-mitglieder-frontend-prod

Falls die Images nicht mehr vorhanden sind, müssen sie aus den archivierten Deployment-Artefakten wiederhergestellt werden.

Beispiel:

docker load -i <image>.tar

---

### 8. Security-Replikation wiederherstellen

Syncthing konfigurieren.

Zielpfad:

```text
/volume1/home/JaitiNissi1968/Security
```

Synchronisationsmodell:

```text
Laptop = Source of Truth
NAS = Replik
Desktop = zusätzlicher Client
```

Prüfen:

- Verbindung hergestellt
- Synchronisation aktiv
- Dateiversionierung aktiv

---

### 9. Datenbank Restore

Falls Daten verloren:

- Dump identifizieren
- Restore durchführen
- Integrität prüfen

## Datenbankstandard

Seit Phase 11 gilt:

```text
<fachbereich>_<umgebung>
```

Produktive Datenbanken:

```text
emc_mitglieder_prod
emc_finanzen_prod
```

Entwicklungsdatenbanken:

```text
emc_mitglieder_dev
emc_finanzen_dev
```

Temporäre Legacy-Datenbanken:

```text
emc_mitglieder
emc_finanzen
```

Status:

```text
temporäre Rollback-Sicherung
nicht produktiv
```

Nach Restore prüfen:

```text
Runtime User
Access User
Rollenmodell
Break-Glass Benutzer
```

Insbesondere validieren:

```text
emc_mitglieder_dev_rw
emc_mitglieder_prod_rw
emc_backend_test_rw
emc_backup

emc_access_mitglieder_dev_rw
emc_access_mitglieder_prod_rw
emc_access_mitglieder_prod_ro

emc_access_finanzen_dev_rw
emc_access_finanzen_prod_rw

JoergTitz
emc_phpmyadmin_admin
root@localhost
```

---

### 10. Access Restore

Prüfen:

- NAS Access Welt
- Windows Arbeitskopien
- ODBC / DSN-less Verbindungen
- Mitglieder DEV
- Mitglieder PROD
- Finanzen DEV
- Finanzen PROD

Starttest durchführen.

Besonders prüfen:

```text
dbconfig.ini
dbconfig_prod.ini
BuildMariaDbConnectString()
automatischer Relink
```

Produktive NAS-Konfiguration:

```text
/volume1/apps/access/emc_mitglieder/prod/dbconfig_prod.ini
/volume1/apps/access/emc_finanzen/prod/dbconfig_prod.ini
```

---

### 11. Monitoring Restore

Prüfen:

- Uptime Kuma
- Telegram Alerts
- Healthchecks
- Backup Monitoring

Insbesondere:

```text
DATA MariaDB Login
DATA DB Backup Healthcheck
```

---

### 12. Gesamtvalidierung

Pflichtprüfungen:

- MariaDB
- Rollenmodell
- Backend DEV
- Backend PROD
- Frontend DEV
- Frontend PROD
- Access
- Backup
- Monitoring
- Syncthing
- Access Benutzer
- DBA Benutzer
- Break-Glass Benutzer

---

## Erfolgreicher Recovery-Endzustand

Folgende Komponenten müssen funktionsfähig sein:

```text
MariaDB
Backend DEV
Backend PROD
Frontend DEV
Frontend PROD
Access DEV
Access PROD
Backup
Monitoring
Syncthing
```

Zusätzlich:

```text
KeePass verfügbar
Security-Verzeichnis repliziert
Recovery-Dokumentation vorhanden

Rollenmodell funktionsfähig
Access Benutzer funktionsfähig
JoergTitz funktionsfähig
root@localhost funktionsfähig
```

Produktive Datenbanken:

```text
emc_mitglieder_prod
emc_finanzen_prod
```

müssen vollständig funktionsfähig sein.

---

## Referenzen

- Break-Glass Dokument
- Recovery Dependencies
- Datenbank Restore Runbook
- Compose Restore Dokumentation
- Credential Inventory

---

## Änderungslog

| Datum      | Änderung                                                                                                                       |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------ |
| 2026-05-30 | Initiale Version                                                                                                               |
| 2026-06-01 | Phase-9-Erweiterung: Rollenmodell, DBA-Modell, Access-Härtung und Break-Glass-Validierung ergänzt                              |
| 2026-06-04 | Phase-11-Erweiterung: Datenbank Naming Migration, Access-Konfigurationsstandard und Recovery-Zielsysteme auf \_prod umgestellt |

```

```
