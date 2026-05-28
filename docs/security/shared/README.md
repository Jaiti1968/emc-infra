# Shared Infrastruktur

Dieser Bereich enthält die Security-, Credential- und Recovery-Dokumentation der gemeinsamen Infrastrukturkomponenten der EMC Plattform.

Diese Komponenten sind fachdomänenübergreifend und werden gemeinsam von:

- Mitglieder
- Finanzen
- später Noten

genutzt.

## Schwerpunkte

- NAS
- SSH / sudo
- Fritz!Box
- WireGuard
- GitHub
- Docker
- Portainer
- Uptime Kuma
- MariaDB Administration
- Backup / Recovery
- Runtime-Infrastruktur

## Wichtige Architekturentscheidung

Shared Infrastruktur wird getrennt dokumentiert von fachlichen Domänen.

Dadurch bleiben:

- Verantwortlichkeiten
- Recoverypfade
- Credential Rotation
- Runtime-Abhängigkeiten

klar nachvollziehbar.

## Typische Inhalte

### Plattformzugänge

- NAS Benutzer
- SSH Zugriff
- sudo
- Break-Glass Zugriff

### Netzwerk

- Fritz!Box
- WireGuard
- DynDNS
- Remote Recovery

### Infrastruktur Runtime

- Docker
- Portainer
- Compose Runtime
- Monitoring
- Logging

### Monitoring

- Uptime Kuma
- Telegram Notifications
- Runtime Monitoring

### Datenbank Administration

- MariaDB Root
- persönliche Admin-Zugänge
- Recoveryzugänge

## Recovery-Relevanz

Die Shared Infrastruktur enthält mehrere Recovery-kritische Komponenten.

Besonders kritisch:

- Fritz!Box
- WireGuard
- NAS SSH
- MariaDB Root
- GitHub Zugriff
