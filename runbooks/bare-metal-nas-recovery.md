# bare-metal-nas-recovery.md

# Bare Metal NAS Recovery Runbook

Status: Phase 2 Skeleton

## Zweck

Worst-Case Recovery:

```text
NAS vollständig ausgefallen
```

---

## Voraussetzungen

Benötigt:

- Ersatzhardware
- Netzwerkzugriff
- Break-Glass
- externe Backups
- Git Zugriff

---

## Recovery Reihenfolge

### 1. Basisbetrieb herstellen

- NAS initialisieren
- Netzwerk
- SSH
- Docker

---

### 2. Credentials herstellen

- Break-Glass
- Admin Zugänge

---

### 3. Git Recovery

Repos:

- emc-infra
- backend
- frontend
- docs

---

### 4. Betriebsstruktur herstellen

```text
/volume1/docker
```

---

### 5. Secrets Restore

`.env`

---

### 6. Compose Restore

Reihenfolge:

```text
MariaDB
Uptime Kuma
Portainer
Backend
Frontend
optional phpMyAdmin
```

---

### 7. DB Restore

Falls Daten verloren.

---

### 8. Access Restore

- NAS Access Welt
- Windows Arbeitskopien
- ODBC
- Starttest

---

### 9. Monitoring Restore

Alerting aktivieren.

---

### 10. Gesamtvalidierung

Pflicht:

- DB
- Backend
- Frontend
- Access
- Backup
- Monitoring

---

## Referenzen

- Break-Glass
- DB Restore
- Compose Restore
