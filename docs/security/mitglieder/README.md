# Mitglieder Domäne

Dieser Bereich enthält die Security-, Credential-, Runtime- und Recovery-Dokumentation der Mitglieder-Domäne.

Die Mitglieder-Domäne umfasst:

- Mitgliederdatenbank
- Backend Services
- Frontend Services
- Access Runtime
- Runtime-Secrets
- Monitoring
- Recovery-Abhängigkeiten

## Architekturprinzip

Die Mitglieder-Domäne wird fachlich getrennt von:

- shared Infrastruktur
- Finanzen
- später Noten

geführt.

## Schwerpunkte

### Datenbank

- emc_mitglieder
- emc_mitglieder_dev
- DB Runtime User
- RW/RO Konzepte
- Backend DB Zugriff
- Access DB Zugriff

### Backend

- Backend DEV
- Backend PROD
- Runtime Secrets
- Compose Runtime
- Recovery-Abhängigkeiten

### Frontend

- Frontend DEV
- Frontend PROD
- Runtime-Konfiguration
- API-Abhängigkeiten

### Access

- Access Mitglieder DEV
- Access Mitglieder PROD
- dbconfig-Dateien
- ODBC Runtime
- Netzwerkabhängigkeiten

### Monitoring

- Backend Monitoring
- Runtime Monitoring
- Backup Monitoring

## Recovery-Relevanz

Besonders kritisch:

- Backend PROD Runtime
- Access PROD
- Runtime DB User
- DB Restorefähigkeit
- Runtime-Secrets

## Zielarchitektur

Die Mitglieder-Domäne soll langfristig:

- klar getrennte Runtime-Secrets
- getrennte Runtime-Konfiguration
- getrennte DB User
- kontrollierte Credential Rotation
- saubere Recoverypfade

besitzen.
