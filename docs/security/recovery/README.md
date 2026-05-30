# Recovery

Dieser Bereich enthält die Recovery-, Break-Glass- und Wiederherstellungsdokumentation der EMC Infrastruktur.

Ziel:

belastbare Wiederherstellbarkeit aller produktiv relevanten Systeme und Zugänge.

## Schwerpunkte

- Break-Glass-Zugänge
- Recovery-Ketten
- gekoppelte Credentials
- Recovery-Abhängigkeiten
- Remote-Recovery
- Access-Recovery
- Runtime-Recovery
- Single Points of Failure
- Wiederanlauf kritischer Systeme

## Wichtigste Grundregel

Recoveryfähigkeit hat höhere Priorität als maximale Security-Härte.

## Wichtige Recovery-Bereiche

### Shared Infrastruktur

- NAS
- SSH
- sudo
- Fritz!Box
- WireGuard
- GitHub
- Docker
- Portainer
- Uptime Kuma
- Syncthing

### Datenbank

- MariaDB Root
- persönliche Admin-Zugänge
- Runtime DB User
- Restorefähigkeit

### Access

- dbconfig-Dateien
- ODBC Runtime
- Netzwerkabhängigkeiten
- NAS-IP-Abhängigkeiten

### Runtime

- Compose Source of Truth
- Runtime-Secrets
- Monitoring
- Container Recovery

### Security-Artefakte

- KeePassXC Datenbanken
- Fritz!Box Konfigurationssicherungen
- Credential Inventory
- Break-Glass Dokumentation
- Recovery-Dokumentation

## Security-Verzeichnis Synchronisation

Seit Phase 8 werden die Security-Artefakte zusätzlich auf das NAS repliziert.

Führender Speicherort:

```text
C:\Users\Joerg\Documents\Security
```

NAS-Replik:

```text
/volume1/home/JaitiNissi1968/Security
```

Betriebsmodell:

```text
Laptop = Source of Truth
NAS = Replik
Desktop = zusätzlicher Client (optional)
```

Synchronisation:

```text
Syncthing
```

Wichtige Hinweise:

- Syncthing dient der Replikation.
- Syncthing ersetzt kein Backup.
- Die NAS-Replik wird zusätzlich durch das bestehende Backup-Konzept geschützt.
- Die Syncthing-Versionierung (.stversions) unterstützt die Wiederherstellung versehentlich gelöschter oder überschriebener Dateien.

## Recovery-Szenarien

### Laptop-Ausfall

Recovery über:

- NAS-Replik
- NAS Backup
- USB Backup

### NAS-Ausfall

Recovery über:

- Laptop (führende Quelle)
- GitHub
- USB Backup

### Beschädigte KeePass-Datei

Recovery über:

- Syncthing Versionierung (.stversions)
- NAS Backup
- USB Backup

## Wichtige Grundprinzipien

### Keine gleichzeitige Rotation gekoppelter Credentials

Besonders kritisch:

- Fritz!Box
- WireGuard
- NAS SSH
- MariaDB Root
- Backend PROD
- Access PROD

### Recovery vor Bereinigung

Altlasten werden erst entfernt, wenn Recoveryfähigkeit gesichert ist.

### Recovery dokumentieren

Recoverypfade müssen nachvollziehbar dokumentiert sein.

### Laptop bleibt führendes System

Für Security-Artefakte gilt:

```text
Laptop = Source of Truth
```

Die NAS-Kopie dient der Replikation und Recovery-Unterstützung.
