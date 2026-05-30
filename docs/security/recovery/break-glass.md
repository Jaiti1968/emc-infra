# EMC NAS Betriebsstandardisierung – Break-Glass Dokument

Status: Phase 8 / Security-Verzeichnis Synchronisation
Ablageziel: `emc-infra/docs/security/recovery/break-glass.md`
Wichtig: Dieses Dokument enthält bewusst keine Klartextpasswörter.

---

## Zweck

Dieses Dokument beschreibt den Notfallzugriff auf die EMC NAS-/Docker-/Datenbank-Infrastruktur.

Break-Glass bedeutet:

> Zugriff und Wiederherstellung müssen auch dann möglich sein, wenn einzelne Systeme, der normale Admin-PC oder das NAS ausfallen.

---

## Grundprinzipien

- Recovery First.
- Keine Secrets im Git.
- Portainer ist operative Runtime-Verwaltung, aber nicht architektonische Primärquelle.
- GitHub `emc-infra` ist Source of Truth für Compose / Betriebsstruktur.
- Root- und Admin-Zugänge nur für Break-Glass / Notfall.
- Recovery-Dokumente dürfen nicht ausschließlich auf dem NAS liegen.

---

## Kritische Systeme

| System              | Zweck                                  | Kritikalität |
| ------------------- | -------------------------------------- | -----------: |
| UGREEN DH2300 NAS   | Docker Runtime / Daten / Access Master |     kritisch |
| MariaDB             | Datenbankkern Mitglieder / Finanzen    |     kritisch |
| Access Master Share | Access Finanzen / Mitglieder Artefakte |     kritisch |
| Fritz!Box           | LAN / WireGuard / Netzwerkzugriff      |     kritisch |
| GitHub              | Recovery Source für Repositories       |     kritisch |
| Portainer           | operative Docker-Verwaltung            |  mittel-hoch |
| Uptime Kuma         | Monitoring / Alerting                  |       mittel |
| Syncthing           | Replikation der Security-Artefakte     |         hoch |
| Windows PC / Laptop | Access / ODBC / Admin-Arbeitsplatz     |     relevant |

---

## Wichtige bekannte Pfade

### NAS

```text
/volume1/docker
/volume1/docker/compose
/volume1/docker/docs
/volume1/docker/runbooks
/volume1/docker/recovery
/volume1/docker/secrets
/volume1/docker/backups
/volume1/docker/build
```

### Aktueller MariaDB Backup-Pfad

```text
/volume1/docker/backups/mariadb
```

### Externes USB Backup Medium

```text
/mnt/@usb/sdc1
```

Aktueller Befund:

- Gerät erkannt als `/dev/sdc1`
- Dateisystem: NTFS
- Kapazität: ca. 1,8 TB frei
- NAS-Schreibtest erfolgreich
- Windows-Lesbarkeit aufgrund NTFS gegeben

### Access Master Share

```text
\\NAS-DH2300-C64C\apps\access
```

### Lokale Windows Arbeitskopien

```text
D:\Chor\Access\emc_mitglieder\dev
D:\Chor\Access\emc_mitglieder\prod
D:\Chor\Access\emc_finanzen\dev
D:\Chor\Access\emc_finanzen\prod
```

### Security-Artefakte

Führender Speicherort:

```text
C:\Users\Joerg\Documents\Security
```

NAS-Replik:

```text
/volume1/home/JaitiNissi1968/Security
```

Synchronisation:

```text
Syncthing
```

Betriebsmodell:

```text
Laptop = Source of Truth
NAS = Replik
Desktop = zusätzlicher Client (optional)
```

---

## Notfall-Recovery Kurzreihenfolge

### Szenario: NAS lebt, Anwendung defekt

1. Zugriff per SSH oder NAS Web-UI herstellen.
2. Docker / Containerstatus prüfen.
3. Betroffene Compose-Konfiguration aus `emc-infra` bzw. `/volume1/docker/compose` prüfen.
4. Secrets / `.env` prüfen.
5. Falls DB betroffen: aktuellen Dump identifizieren.
6. Restore gemäß DB-Runbook durchführen.
7. Funktionstest durchführen.
8. Monitoring prüfen.

---

### Szenario: NAS vollständig defekt

1. Ersatzsystem / Ersatz-NAS bereitstellen.
2. Netzwerkzugriff herstellen.
3. Admin- und SSH-Zugang herstellen.
4. Docker bereitstellen.
5. Externes USB Backup Medium anschließen.
6. Credential-Quelle öffnen.
7. Security-Verzeichnis auf Laptop prüfen.
8. Falls NAS-Replik verloren ging, gilt die Laptop-Kopie als führende Quelle.
9. Syncthing-Konfiguration aus Backup oder Dokumentation wiederherstellen.
10. GitHub Zugriff herstellen.
11. `emc-infra` klonen.
12. Standardstruktur `/volume1/docker` herstellen.
13. Secrets / `.env` aus Backup wiederherstellen.
14. Compose Stacks in Reihenfolge wiederherstellen:
    1. MariaDB
    2. Uptime Kuma
    3. Portainer
    4. Syncthing
    5. Backend DEV
    6. Backend PROD
    7. Frontend DEV
    8. Frontend PROD
    9. phpMyAdmin nur bei Bedarf

15. Datenbanken aus Dumps wiederherstellen, falls Datenvolume nicht verfügbar.
16. Access Master-Artefakte wiederherstellen.
17. Windows Access-Arbeitskopien testen.
18. ODBC / DSN-less Verbindung prüfen.
19. Monitoring / Telegram Alerting prüfen.
20. Backupjob wieder aktivieren und testen.

---

## Break-Glass Zugänge

Die eigentlichen Zugangsdaten liegen nicht in diesem Dokument.

Siehe:

```text
docs/security/credential-inventory.md
```

Credential Source of Truth:

```text
KeePassXC
```

Führender Speicherort:

```text
C:\Users\Joerg\Documents\Security
```

NAS-Replik:

```text
/volume1/home/JaitiNissi1968/Security
```

Die eigentlichen Zugangsdaten befinden sich ausschließlich in KeePassXC und nicht in dieser Dokumentation.

---

## Minimal benötigte Zugänge im Notfall

| Zweck                | Zugang                          |
| -------------------- | ------------------------------- |
| NAS Verwaltung       | NAS Admin                       |
| Shell / Docker       | SSH User mit sudo               |
| Netzwerk / VPN       | Fritz!Box Admin                 |
| DB Recovery          | MariaDB Root                    |
| Infra Recovery       | GitHub Account                  |
| Runtime Recovery     | Portainer Admin                 |
| Monitoring           | Uptime Kuma Admin               |
| Access Recovery      | Windows Admin / Access Umgebung |
| Security-Artefakte   | KeePassXC Datenbank             |
| Security-Replikation | Syncthing Verwaltung            |

---

## Recovery der Security-Artefakte

Die Security-Artefakte sind Bestandteil der Recovery-Kette.

Primärquelle:

```text
C:\Users\Joerg\Documents\Security
```

NAS-Replik:

```text
/volume1/home/JaitiNissi1968/Security
```

Synchronisation erfolgt über Syncthing.

Wichtige Grundsätze:

- Laptop bleibt Source of Truth.
- NAS dient als Replikat.
- Syncthing ersetzt kein Backup.
- Die NAS-Replik wird zusätzlich durch das bestehende Backup-Konzept geschützt.
- Die Syncthing-Versionierung (`.stversions`) unterstützt die Wiederherstellung versehentlich gelöschter oder beschädigter Dateien.

---

## Nicht Bestandteil dieser Phase

- Credential Rotation
- GitHub MFA Einführung
- Docker Secrets Migration
- Netzwerk-Härtung
- DB-User-Härtung

Diese Themen bleiben separaten Folgephasen vorbehalten.

---

## Änderungslog

| Datum      | Änderung                                                                         |
| ---------- | -------------------------------------------------------------------------------- |
| 2026-05-26 | Initiales Break-Glass Dokument für Phase 2 erstellt                              |
| 2026-05-30 | Phase-8-Erweiterung: Syncthing und Security-Artefakte in Break-Glass aufgenommen |
