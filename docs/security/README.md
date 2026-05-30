# Security / Credential Management

Dieser Bereich enthält die Security-, Credential-, Secret- und Recovery-Dokumentation der EMC Infrastruktur.

## Ziele

- kontrollierte Betriebsstandardisierung
- Recoveryfähigkeit
- Credential Governance
- Runtime-Secret-Standardisierung
- fachliche Domänentrennung
- kontrollierte Credential Rotation
- dokumentierte Recoverypfade

## Wichtige Grundprinzipien

- Recovery First
- keine Blindbereinigung
- keine produktiven Klartext-Secrets im Git
- kontrollierte evolutionäre Migration
- fachliche Trennung:
  - shared
  - mitglieder
  - finanzen
  - noten (später)

## Security-Verzeichnis Synchronisation

Seit Phase 8 wird die lokale Security-Struktur zusätzlich auf das NAS repliziert.

Führender Speicherort:

```text
C:\Users\Joerg\Documents\Security
```

NAS-Replik:

```text
/volume1/home/JaitiNissi1968/Security
```

Betriebsstandard:

```text
Laptop = Source of Truth
NAS = Replik
Desktop = zusätzlicher Client (optional)
```

Wichtige Hinweise:

- KeePassXC bleibt Credential Source of Truth.
- Syncthing dient ausschließlich der Replikation.
- Syncthing ersetzt kein Backup.
- Die NAS-Replik wird zusätzlich durch das etablierte Backup-Konzept geschützt.
- Die Syncthing-Dateiversionierung (.stversions) ist aktiviert.

## Struktur

### governance/

Regeln, Standards und Zielmodelle.

### inventory/

Inventarisierung vorhandener Credentials, Secrets und Runtime-Abhängigkeiten.

### recovery/

Break-Glass-, Recovery- und Abhängigkeitsdokumentation.

### shared/

Gemeinsame Infrastruktur:

- NAS
- Netzwerk
- GitHub
- Portainer
- Monitoring
- MariaDB Admin
- Syncthing

### mitglieder/

Security- und Credential-Dokumentation der Mitglieder-Domäne.

### finanzen/

Security- und Credential-Dokumentation der Finanzdomäne.

### noten/

Reservierte zukünftige Domäne.

## Dokumentationsgrundsatz

Credential-Dokumentation, Recovery-Dokumentation und produktiver Betriebszustand sollen jederzeit konsistent sein.

Die führenden Informationsquellen sind:

1. KeePassXC
2. Dokumentation unter `docs/security`
3. Runtime-Konfiguration auf dem NAS

Abweichungen sind zeitnah zu korrigieren.
