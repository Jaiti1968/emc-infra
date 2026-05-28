# Security / Credential Management

Dieser Bereich enthält die Security-, Credential-, Secret- und Recovery-Dokumentation der EMC Infrastruktur.

Ziele:

- kontrollierte Betriebsstandardisierung
- Recoveryfähigkeit
- Credential Governance
- Runtime-Secret-Standardisierung
- fachliche Domänentrennung
- kontrollierte Credential Rotation
- dokumentierte Recoverypfade

Wichtige Grundprinzipien:

- Recovery First
- keine Blindbereinigung
- keine produktiven Klartext-Secrets im Git
- kontrollierte evolutionäre Migration
- fachliche Trennung:
  - shared
  - mitglieder
  - finanzen
  - noten (später)

## Struktur

### governance/

Regeln, Standards und Zielmodelle.

### inventory/

Inventarisierung vorhandener Credentials, Secrets und Runtime-Abhängigkeiten.

### recovery/

Break-Glass-, Recovery- und Abhängigkeitsdokumentation.

### shared/

Gemeinsame Infrastruktur:
NAS, Netzwerk, GitHub, Portainer, Monitoring, MariaDB Admin.

### mitglieder/

Security- und Credential-Dokumentation der Mitglieder-Domäne.

### finanzen/

Security- und Credential-Dokumentation der Finanzdomäne.

### noten/

Reservierte zukünftige Domäne.
