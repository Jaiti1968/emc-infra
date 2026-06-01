# EMC NAS Betriebsstandardisierung – Credential Inventory

Status: Phase 9 abgeschlossen
Ablageziel: `emc-infra/docs/security/inventory/credential-inventory.md`

Wichtig: Dieses Dokument enthält bewusst keine Klartextpasswörter.

---

# Zweck

Dieses Dokument beschreibt alle sicherheitsrelevanten Zugänge der EMC-Infrastruktur, deren Zweck, Verantwortlichkeiten sowie die jeweilige Credential Source of Truth.

Ziel ist schnelle Orientierung für Betrieb, Recovery und Break-Glass ohne Speicherung von Secrets in Git.

---

# Grundsätze

- Keine Klartextpasswörter in Git.
- Keine Klartextpasswörter in Dokumentationen.
- KeePass ist die Credential Source of Truth.
- Runtime-Secrets werden ausschließlich über KeePass verwaltet.
- `.env` Dateien enthalten produktive Secrets, werden jedoch nicht versioniert.
- Root-Zugänge sind Break-Glass-Zugänge.
- Credential-Änderungen sind zu dokumentieren.
- Credential-Rotationen sind in KeePass nachzuführen.

---

# Credential Source of Truth

## Verbindlicher Standard

```text
KeePass
=
Credential Source of Truth
```

Produktive Laufzeit-Credentials:

```text
NAS Runtime .env Dateien
```

werden ausschließlich aus KeePass gepflegt.

---

# Kritische Infrastrukturzugänge

| System            | Zugang                  | Kritikalität | Credential Source | Bemerkung           |
| ----------------- | ----------------------- | -----------: | ----------------- | ------------------- |
| NAS UGREEN DH2300 | NAS Admin               |     kritisch | KeePass           | Web-Administration  |
| NAS UGREEN DH2300 | SSH User JaitiNissi1968 |     kritisch | KeePass           | Betriebszugang      |
| Fritz!Box         | Router Admin            |     kritisch | KeePass           | Netzwerk-Recovery   |
| WireGuard         | VPN Zugriff             |     kritisch | KeePass           | Remote-Zugang       |
| GitHub            | GitHub Account          |     kritisch | KeePass           | Repository Recovery |
| Portainer         | Portainer Admin         |         hoch | KeePass           | Runtime-Verwaltung  |
| Uptime Kuma       | Uptime Kuma Admin       |       mittel | KeePass           | Monitoring          |

---

# MariaDB Administrationsmodell

## Break-Glass

### root@localhost

Zweck:

```text
Letzter Recovery-Zugang
Break-Glass
```

Verwendung:

```text
NAS Shell
Docker Konsole
Recovery
```

Nicht vorgesehen für:

```text
Tägliche Administration
Runtime
Access
```

Bemerkung:

```text
root@% wurde entfernt.
```

---

## Primärer DBA

### JoergTitz

Zweck:

```text
Tägliche Datenbankadministration
```

Verwendung:

```text
Benutzerprüfung
Rollenprüfung
Datenbankadministration
Recovery-Unterstützung
```

Berechtigungen:

```text
role_emc_admin
```

Besonderheit:

```text
kein GRANT OPTION
keine Rechteweitergabe
```

---

## phpMyAdmin Administrator

### emc_phpmyadmin_admin

Zweck:

```text
phpMyAdmin GUI Administration
```

Verwendung:

```text
phpMyAdmin
Benutzerverwaltung
Rollenverwaltung
```

Grund:

```text
phpMyAdmin 5.2.3 Rollen-Inkompatibilität
mit role_emc_admin
```

Nicht verwenden für:

```text
Runtime
Access
Anwendungen
```

---

# Runtime Benutzer

## Mitgliederverwaltung

### DEV

```text
emc_mitglieder_dev_rw
```

Verwendung:

```text
Backend DEV Runtime
```

---

### PROD

```text
emc_mitglieder_prod_rw
```

Verwendung:

```text
Backend PROD Runtime
```

---

### TEST

```text
emc_backend_test_rw
```

Verwendung:

```text
Lokale Integrationstests
```

---

## Backup

### emc_backup

Verwendung:

```text
MariaDB Backup Container
```

Rechte:

```text
Read Only Backup Rechte
```

---

# Access Benutzer

## Mitglieder DEV

```text
emc_access_mitglieder_dev_rw
```

Verwendung:

```text
Access Mitglieder DEV
```

---

## Mitglieder PROD RW

```text
emc_access_mitglieder_prod_rw
```

Verwendung:

```text
Access Mitglieder PROD
```

---

## Mitglieder PROD RO

```text
emc_access_mitglieder_prod_ro
```

Verwendung:

```text
Read-Only Auswertungen
```

---

## Finanzen DEV

```text
emc_access_finanzen_dev_rw
```

Verwendung:

```text
Access Finanzen DEV
```

---

## Finanzen PROD

```text
emc_access_finanzen_prod_rw
```

Verwendung:

```text
Access Finanzen PROD
```

---

# Access Recovery

Verbindlicher Standard:

```text
DSN-less Betrieb
```

Konfiguration:

```text
dbconfig.ini
```

Architektur:

```text
BuildMariaDbConnectString()
```

Recovery-Ziel:

```text
Benutzerwechsel
Passwortwechsel
Credential-Rotation
```

ohne manuelle Neuverknüpfung der Tabellen.

---

# Lokale Security-Struktur

Source of Truth:

```text
C:\Users\Joerg\Documents\Security
```

Struktur:

```text
Security
├── FritzBox
└── KeePass
```

Laptop bleibt führendes System.

---

# Synchronisation

Replikationsmodell:

```text
Laptop
⇅
Syncthing
⇅
NAS
```

NAS-Ziel:

```text
/volume1/home/JaitiNissi1968/Security
```

---

# MFA / 2FA Status

| System      | MFA aktiv             | Bemerkung             |
| ----------- | --------------------- | --------------------- |
| GitHub      | prüfen                | zukünftiges Hardening |
| NAS         | derzeit nicht genutzt |                       |
| Fritz!Box   | derzeit nicht genutzt |                       |
| Portainer   | derzeit nicht genutzt |                       |
| Uptime Kuma | derzeit nicht genutzt |                       |

---

# Änderungslog

| Datum      | Änderung                                                                   |
| ---------- | -------------------------------------------------------------------------- |
| 2026-05-26 | Initiale Version Phase 2                                                   |
| 2026-05-29 | Secret Management und Runtime Credential Rotation                          |
| 2026-05-30 | Lokale Security-Struktur und Syncthing ergänzt                             |
| 2026-06-01 | Rollenmodell, Access-User, Break-Glass-Modell und phpMyAdmin-Admin ergänzt |
