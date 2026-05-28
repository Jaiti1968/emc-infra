# Inventory

Dieser Bereich enthält die Inventarisierung aller bekannten Credentials, Secrets, Runtime-Abhängigkeiten und Recovery-relevanten Komponenten der EMC Infrastruktur.

Ziel:

vollständige nachvollziehbare Dokumentation produktiv relevanter Security- und Credential-Strukturen.

## Schwerpunkte

- Credential Inventory
- Credential Klassifizierung
- Runtime Secret Mapping
- Runtime-Abhängigkeiten
- Recovery-Klassifizierung
- Altlastenbewertung
- Shadow-Konfigurationen
- Secret Storage Orte

## Wichtige Grundregeln

### Keine Secrets im Inventory

Dieses Verzeichnis dokumentiert:

- Struktur
- Nutzung
- Verantwortlichkeit
- Kritikalität
- Speicherorte

aber keine produktiven Passwortwerte.

### Recovery-Relevanz dokumentieren

Besonders wichtig:

- Break-Glass-Zugänge
- Recovery-Ketten
- gekoppelte Credentials
- Single Points of Failure

### Fachliche Domänentrennung

Inventarisierung erfolgt getrennt nach:

- shared
- mitglieder
- finanzen
- noten (später)

## Typische Inhalte

- MariaDB User
- Runtime Secrets
- Compose-Abhängigkeiten
- Access Runtime-Konfiguration
- Monitoring Credentials
- GitHub Recovery
- WireGuard Recovery
- NAS-/SSH-Zugänge
- Altlasten-Compose-Strukturen
