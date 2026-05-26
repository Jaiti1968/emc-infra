# compose-stack-restore.md

# Compose / Stack Restore Runbook

Status: Phase 2 Skeleton

## Zweck

Wiederherstellung einzelner Docker Stacks.

---

## Architekturgrundsatz

Source of Truth:

Git:

```text
emc-infra
```

Portainer:

operative Runtime-Verwaltung.

Nicht Primärquelle.

---

## Voraussetzungen

Benötigt:

- NAS Zugriff
- Docker Zugriff
- Git Zugriff
- .env Dateien
- Break-Glass

---

## Restore-Szenarien

### Einzelner Stack

Beispiele:

- mariadb
- uptime-kuma
- backend
- frontend
- portainer

---

## Ablauf

### 1. Incident bewerten

Was ist kaputt?

- Container
- Compose
- Runtime
- Secrets

---

### 2. Source of Truth prüfen

Git Stand.

---

### 3. Secrets bereitstellen

`.env`

---

### 4. Redeploy

Kontrolliert.

---

### 5. Validierung

Pflicht:

- Container läuft
- Healthcheck
- Logs
- Funktionstest

---

## Spezialfall MariaDB

Zusätzlich:

DB Integrität prüfen.

---

## Rollback

Vorherigen Stand wiederherstellen.
