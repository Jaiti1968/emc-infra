# Break-Glass Quick Recovery

Status: Phase 11 abgeschlossen

## Zweck

Schneller Incident-Zugriff.

Dieses Dokument dient als Kurzreferenz im Störungsfall und verweist auf die ausführlichen Recovery-Dokumente.

---

## Prioritäten

1. Ruhe bewahren
2. Incident klassifizieren
3. Zugriff sichern
4. Recovery-Weg wählen
5. Recovery dokumentieren
6. Keine unkontrollierten Änderungen durchführen

---

## Schnellcheck

### Zugriff

Vorhanden?

- NAS Weboberfläche
- SSH Zugang
- Fritz!Box Zugriff
- WireGuard Zugriff
- GitHub Zugriff
- MariaDB Administration
- Portainer Zugriff

---

### Kritische Administrationszugänge

Prüfen:

```text
JaitiNissi1968
JoergTitz
emc_phpmyadmin_admin
root@localhost
```

---

### Recovery-Artefakte

Vorhanden?

- KeePass
- Credential Inventory
- Break-Glass Dokument
- Recovery Dependencies
- Backup
- Runbooks

---

### Security-Artefakte

Prüfen:

Führender Speicherort:

```text
D:\Security
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
Desktop = zusätzlicher Recovery-Client
```

---

## Incident Typen

### Anwendung ausgefallen

Maßnahme:

```text
Compose Restore
```

Prüfen:

- Containerstatus
- Logs
- Runtime-Secrets
- Datenbankverbindung

---

### Datenbankproblem

Maßnahme:

```text
DB Restore
```

Prüfen:

```text
MariaDB Status
Rollenmodell
Runtime User
Access User
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

Wichtige Benutzer:

```text
JoergTitz
root@localhost
```

---

### Access Problem

Maßnahme:

```text
Access Restore
```

Prüfen:

```text
DSN-less Verbindung
dbconfig.ini
dbconfig_prod.ini
BuildMariaDbConnectString()
Netzwerkverbindung
Datenbankzugriff
```

Produktive Konfiguration:

```text
dbconfig_prod.ini
```

Entwicklung:

```text
dbconfig.ini
```

---

### Credential Problem

Maßnahme:

```text
KeePass prüfen
```

Prüfen:

```text
Security-Verzeichnis
NAS-Replik
Syncthing Status
Credential Inventory
```

---

### NAS Totalausfall

Maßnahme:

```text
Bare Metal Recovery
```

Prüfen:

```text
Ersatzhardware
USB Backup
KeePass
GitHub
Security-Verzeichnis
```

---

## Sofortmaßnahmen vermeiden

Nicht ohne Recoverybewertung:

- root@localhost ändern
- JoergTitz ändern
- Fritz!Box Passwort ändern
- WireGuard neu erzeugen
- SSH Benutzer löschen
- Runtime-Secrets überschreiben
- produktive Datenbanken löschen
- Docker Volumes löschen

Zusätzlich:

```text
Legacy-Datenbanken nicht löschen
```

solange sie als Rollback-Sicherung dienen.

---

## Eskalationsregel

Keine ungeprüften Schnellschüsse auf Produktivsystem.

```text
Recovery First
```

Dokumentation vor Änderungen prüfen.

---

## Wichtige Referenzen

- Break-Glass Dokument
- Recovery Dependencies
- Bare Metal NAS Recovery
- Access Recovery
- Credential Inventory

---

## Datenbankstandard

Seit Phase 11 gilt:

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

Temporäre Rollback-Datenbanken:

```text
emc_mitglieder
emc_finanzen
```

---

## Änderungslog

| Datum      | Änderung                                                                                       |
| ---------- | ---------------------------------------------------------------------------------------------- |
| 2026-05-26 | Initiale Version                                                                               |
| 2026-05-30 | Security-Artefakte und Syncthing ergänzt                                                       |
| 2026-06-01 | Rollenmodell, DBA-Modell und Access-Härtung ergänzt                                            |
| 2026-06-04 | Datenbank Naming Migration auf \_prod dokumentiert, Access-Konfigurationsstandard aktualisiert |

```

```
