# EMC NAS Betriebsstandardisierung – Credential Inventory

Status: Phase 11 abgeschlossen
Ablageziel: `emc-infra/docs/security/inventory/credential-inventory.md`

Wichtig: Dieses Dokument enthält bewusst keine Klartextpasswörter.

---

# Zweck

Dieses Dokument beschreibt alle sicherheitsrelevanten Zugänge der EMC-Infrastruktur, deren Zweck, Verantwortlichkeiten sowie die jeweilige Credential Source of Truth.

Ziel ist schnelle Orientierung für Betrieb, Recovery, Monitoring und Break-Glass ohne Speicherung von Secrets in Git.

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
- Einträge ohne Credential-Charakter werden nicht im KeePass gepflegt.

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

# Datenbank Naming Standard

Seit Phase 11 gilt verbindlich:

```text
<fachbereich>_<umgebung>
```

Produktiv:

```text
emc_mitglieder_prod
emc_finanzen_prod
```

Entwicklung:

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
nicht mehr produktiv genutzt
temporäre Rollback-Sicherung
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

Datenbank:

```text
emc_mitglieder_dev
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

Datenbank:

```text
emc_mitglieder_prod
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

# Monitoring Benutzer

## MariaDB Monitoring

### emc_monitoring

Verwendung:

```text
Uptime Kuma
DATA MariaDB Login
```

Zweck:

```text
Technischer Monitoring-Benutzer
für echte MariaDB-Verbindungsprüfungen
```

Architektur:

```text
Uptime Kuma
      ↓
emc_monitoring
      ↓
MariaDB Login
      ↓
SELECT 1
```

Nicht verwenden für:

```text
Runtime
Access
Administration
Backup
Recovery
Tests
```

Besonderheit:

```text
Ausschließlich für Monitoring vorgesehen.
```

Einführung:

```text
Phase 10 Monitoring / Healthcheck Ausbau
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

Datenbank:

```text
emc_mitglieder_dev
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

Datenbank:

```text
emc_mitglieder_prod
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

Datenbank:

```text
emc_mitglieder_prod
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

Datenbank:

```text
emc_finanzen_dev
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

Datenbank:

```text
emc_finanzen_prod
```

---

# Access Recovery

Verbindlicher Standard:

```text
DSN-less Betrieb
```

Konfiguration:

```text
DEV:
dbconfig.ini

PROD:
dbconfig_prod.ini
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

## NAS Source of Truth

Mitglieder PROD:

```text
/volume1/apps/access/emc_mitglieder/prod/dbconfig_prod.ini
```

Finanzen PROD:

```text
/volume1/apps/access/emc_finanzen/prod/dbconfig_prod.ini
```

---

# Lokale Security-Struktur

Source of Truth:

```text
D:\Security
```

auf dem Laptop.

Struktur:

```text
Security
├── FritzBox
└── KeePass
```

Laptop bleibt führendes System.

Desktop und NAS sind Repliken.

---

# Synchronisation

Replikationsmodell:

```text
Laptop (Source of Truth)
        ⇅
     Syncthing
    ↙       ↘
 NAS       Desktop
```

Pfade:

```text
Laptop:
D:\Security

NAS:
/volume1/home/JaitiNissi1968/Security

Desktop:
D:\Security
```

Rollen:

```text
Laptop
=
führende Arbeitskopie

NAS
=
Replik
+ Dateiversionierung
+ Recovery-Kopie

Desktop
=
zusätzliche Replik
+ Recovery-Client
```

---

# Syncthing GUI

Die Syncthing-Weboberflächen werden ohne GUI-Authentifizierung betrieben.

Zugriff Windows lokal:

```text
http://127.0.0.1:8384
```

NAS Zugriff im Heimnetz:

```text
http://<NAS-IP>:8384
```

Bewertung:

```text
Keine Syncthing-GUI-Credentials vorhanden.
Keine KeePass-Einträge erforderlich.
```

Die früheren KeePass-Einträge wurden entfernt:

```text
Syncthing GUI (Laptop)
Syncthing GUI (Desktop-Joerg2)
Syncthing GUI (NAS)
```

Die Absicherung erfolgt über:

```text
Windows-Anmeldung
NAS-Anmeldung
Syncthing-Gerätezertifikate
KeePass-Masterpasswort
```

---

# Monitoring Credential Governance

Monitoring-Credentials werden getrennt von Runtime-, Access- und Administrations-Credentials verwaltet.

Grundsatz:

```text
Monitoring ≠ Runtime
Monitoring ≠ Administration
Monitoring ≠ Recovery
```

Ziel:

```text
Least Privilege
minimierte Auswirkungen
saubere Rollentrennung
```

Neue produktive Monitoring-Credentials sind in:

- KeePass
- Credential Inventory

zu dokumentieren.

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

| Datum      | Änderung                                                                                                                |
| ---------- | ----------------------------------------------------------------------------------------------------------------------- |
| 2026-05-26 | Initiale Version Phase 2                                                                                                |
| 2026-05-29 | Secret Management und Runtime Credential Rotation                                                                       |
| 2026-05-30 | Lokale Security-Struktur und Syncthing ergänzt                                                                          |
| 2026-06-01 | Rollenmodell, Access-User, Break-Glass-Modell und phpMyAdmin-Admin ergänzt                                              |
| 2026-06-02 | Desktop-Recovery-Client ergänzt, Security-Struktur nach D:\Security migriert, Syncthing GUI-Credentials entfernt        |
| 2026-06-03 | Monitoring-Benutzer emc_monitoring ergänzt, Monitoring Credential Governance eingeführt                                 |
| 2026-06-04 | Datenbank Naming Migration auf \_prod abgeschlossen, Runtime-, Access-, Backup- und Monitoring-Zuordnungen aktualisiert |

```

```
