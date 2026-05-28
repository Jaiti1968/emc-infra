# Finanzdomäne

Dieser Bereich enthält die Security-, Credential-, Runtime- und Recovery-Dokumentation der Finanzdomäne.

Die Finanzdomäne umfasst:

- Finanzdatenbanken
- Access Runtime
- Runtime-Secrets
- Backup
- Recovery-Abhängigkeiten
- Reporting-relevante Komponenten

## Architekturprinzip

Die Finanzdomäne wird fachlich getrennt von:

- shared Infrastruktur
- Mitglieder
- später Noten

geführt.

## Besondere Charakteristik

Die Finanzdomäne ist aktuell stark Access-orientiert und produktiv kritisch.

Änderungen erfolgen kontrolliert und konservativ.

## Schwerpunkte

### Datenbank

- emc_finanzen
- emc_finanzen_dev
- DB Runtime User
- RW/RO Konzepte
- Access DB Zugriff

### Access Runtime

- Access Finanzen DEV
- Access Finanzen PROD
- dbconfig-Dateien
- ODBC Runtime
- Netzwerkabhängigkeiten

### Runtime Secrets

- Access Runtime-Konfiguration
- DB Runtime Credentials
- Secret Storage Orte

### Backup / Recovery

- DB Backups
- Restorefähigkeit
- Runtime Recovery
- Recovery-Abhängigkeiten

## Recovery-Relevanz

Besonders kritisch:

- Access PROD
- Runtime DB User
- DB Restorefähigkeit
- Runtime-Konfiguration
- dbconfig-Dateien

## Zielarchitektur

Die Finanzdomäne soll langfristig:

- klar getrennte Runtime-Secrets
- getrennte Runtime-Konfiguration
- kontrollierte Credential Rotation
- belastbare Recoverypfade
- saubere Runtime-Dokumentation

besitzen.

## Wichtige Betriebsregel

Änderungen an produktiven Finanz-Credentials erfolgen nur kontrolliert und mit Recoverybewertung.
