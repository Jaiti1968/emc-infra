# Governance

Dieser Bereich enthält die verbindlichen Governance-, Betriebs- und Zielmodellregeln für Credentials, Secrets und Recovery der EMC Infrastruktur.

Ziel:

kontrollierte evolutionäre Betriebsreife ohne Verlust der Recoveryfähigkeit.

## Schwerpunkte

- Credential Governance
- Secret Storage Standards
- Runtime-Secret-Modell
- KeePassXC-Struktur
- Credential Rotation Strategie
- Recovery First Prinzipien
- fachliche Domänentrennung

## Wichtige Architekturentscheidungen

### Recovery First

Recoveryfähigkeit hat höhere Priorität als maximale Security-Härte.

### Keine Blindbereinigung

Altlasten werden zuerst inventarisiert und bewertet, erst danach entfernt.

### Keine produktiven Klartext-Secrets im Git

Produktive Secrets gehören nicht in versionierte Dateien.

### Fachliche Domänentrennung

Security- und Credential-Strukturen werden getrennt geführt für:

- shared
- mitglieder
- finanzen
- noten (später)

### Evolutionäre Migration

Keine Big-Bang-Umstellungen.

Credential-Härtung erfolgt kontrolliert und schrittweise.

## Zielzustand

- zentrale Credential-Verwaltung
- kontrollierte Runtime-Secrets
- belastbare Recoverypfade
- klare Verantwortlichkeiten
- dokumentierte Rotationsstrategie
- saubere Domänentrennung
