# Break-Glass Quick Recovery

Status: Phase 8 Security-Verzeichnis Synchronisation

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

### Recovery-Artefakte

Vorhanden?

- KeePassXC
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
Desktop = zusätzlicher Client
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

- MariaDB Status
- Benutzer
- Berechtigungen
- Backups
- Restorefähigkeit

---

### Access Problem

Maßnahme:

```text
Access Restore
```

Prüfen:

- ODBC / DSN-less Verbindung
- dbconfig.ini
- Netzwerkverbindung
- Datenbankzugriff

---

### Credential Problem

Maßnahme:

```text
KeePassXC prüfen
```

Prüfen:

- Security-Verzeichnis
- NAS-Replik
- Syncthing Status
- Credential Inventory

---

### NAS Totalausfall

Maßnahme:

```text
Bare Metal Recovery
```

Prüfen:

- Ersatzhardware
- USB Backup
- KeePassXC
- GitHub
- Security-Verzeichnis

---

## Sofortmaßnahmen vermeiden

Nicht ohne Recoverybewertung:

- MariaDB Root ändern
- Fritz!Box Passwort ändern
- WireGuard neu erzeugen
- SSH Benutzer löschen
- Runtime-Secrets überschreiben
- Datenbanken löschen
- Docker Volumes löschen

---

## Eskalationsregel

Keine ungeprüften Schnellschüsse auf Produktivsystem.

Recovery First.

Dokumentation vor Änderungen prüfen.

---

## Wichtige Referenzen

- Break-Glass Dokument
- Recovery Dependencies
- Bare Metal NAS Recovery
- Datenbank Restore Runbook
- Credential Inventory

---

## Änderungslog

| Datum      | Änderung                                                      |
| ---------- | ------------------------------------------------------------- |
| 2026-05-26 | Initiales Quick-Recovery Dokument erstellt                    |
| 2026-05-30 | Phase-8-Erweiterung: Security-Artefakte und Syncthing ergänzt |
