# Bare Metal NAS Recovery Runbook

Status: Phase 8 Security-Verzeichnis Synchronisation

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
- KeePassXC Zugriff
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
C:\Users\Joerg\Documents\Security
```

Falls verfügbar:

- KeePassXC Datenbank
- Fritz!Box Backup
- Recovery-Dokumentation
- Credential-Dokumentation

Wichtig:

```text
Laptop = Source of Truth
```

---

### 3. Credentials herstellen

- KeePassXC öffnen
- Break-Glass prüfen
- Admin-Zugänge validieren

---

### 4. Git Recovery

Repositories klonen:

- emc-infra
- emc-mitglieder-backend
- emc-mitglieder-frontend
- emc-dokumentation

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
- KeePassXC

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

---

### 11. Monitoring Restore

Prüfen:

- Uptime Kuma
- Telegram Alerts
- Healthchecks
- Backup Monitoring

---

### 12. Gesamtvalidierung

Pflichtprüfungen:

- MariaDB
- Backend DEV
- Backend PROD
- Frontend DEV
- Frontend PROD
- Access
- Backup
- Monitoring
- Syncthing

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
KeePassXC verfügbar
Security-Verzeichnis repliziert
Recovery-Dokumentation vorhanden
```

---

## Referenzen

- Break-Glass Dokument
- Recovery Dependencies
- Datenbank Restore Runbook
- Compose Restore Dokumentation
- Credential Inventory
