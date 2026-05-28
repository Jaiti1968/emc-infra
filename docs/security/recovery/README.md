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

## Wichtige Grundprinzipien

### Keine gleichzeitige Rotation gekoppelte Credentials

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
