# Credential Rotation Strategy

## Zweck

Dieses Dokument definiert die Strategie für kontrollierte Credential Rotation innerhalb der EMC Infrastruktur.

Ziel:

- sichere Credential-Härtung
- recoveryfähige Rotation
- Vermeidung von Lockout-Situationen
- kontrollierte Runtime-Migration
- nachvollziehbare Betriebsprozesse
- langfristig wartbare Security-Struktur

Keine produktiven Secretwerte in diesem Dokument.

---

# Grundprinzipien

## Recovery First

Credential Rotation darf niemals die Wiederherstellbarkeit gefährden.

Vor jeder Rotation müssen geprüft werden:

- Recoverypfade
- Rollbackfähigkeit
- gekoppelte Systeme
- Runtime-Abhängigkeiten
- betroffene Services

---

## Keine Big-Bang-Rotation

Credentials werden schrittweise und kontrolliert rotiert.

Keine parallele Vollumstellung.

---

## Domänentrennung

Rotation erfolgt getrennt für:

- shared
- mitglieder
- finanzen
- noten (später)

---

# Rotationsziele

## Sicherheitsziele

- Entfernung historischer Alt-Credentials
- Entfernung von Klartext-Secrets
- kontrollierte Runtime-Secrets
- nachvollziehbare Verantwortlichkeiten
- reduzierte Recovery-Risiken

---

## Betriebsziele

- minimale Ausfallzeiten
- reproduzierbare Rotation
- rollbackfähige Migration
- kontrollierte Runtime-Neustarts

---

# Kritische Rotationsbereiche

## Shared Infrastruktur

### Besonders kritisch

- Fritz!Box
- WireGuard
- NAS SSH
- sudo
- GitHub
- MariaDB Root

---

## Mitglieder Domäne

### Besonders kritisch

- Backend PROD
- Access PROD
- Runtime DB User
- Runtime Secrets

---

## Finanzdomäne

### Besonders kritisch

- Access PROD
- Runtime DB User
- Access Runtime-Konfiguration

---

# Verbotene Parallelrotationen

## Netzwerk Recovery

Nicht gleichzeitig ändern:

- Fritz!Box
- WireGuard
- DynDNS/MyFritz

---

## Infrastruktur Recovery

Nicht gleichzeitig ändern:

- NAS SSH
- sudo
- Root Recovery

---

## Datenbank Recovery

Nicht gleichzeitig ändern:

- MariaDB Root
- `JoergTitz`
- Backend PROD
- Access PROD

---

## Runtime

Nicht gleichzeitig ändern:

- Compose-Struktur
- Runtime-Secrets
- produktive Runtime-Credentials

---

# Rotationsreihenfolge

## Phase 1 – Governance & Recovery

### Ziele

- Inventarisierung
- Recoverybewertung
- KeePassXC-Struktur
- Secret-Governance

### Keine produktive Rotation

---

## Phase 2 – Recovery Absicherung

### Ziele

- Fritz!Box Export
- WireGuard Export
- GitHub Recovery
- dokumentierte Break-Glass-Zugänge

---

## Phase 3 – DEV / TEST

### Ziele

- DEV Runtime
- TEST Runtime
- nichtkritische Credentials
- erste Secret-Migrationen

### Beispiele

- `emc_backend_dev_rw`
- `emc_backend_test_rw`
- DEV Access Runtime

---

## Phase 4 – Shared Runtime

### Ziele

- Backup Credentials
- Monitoring Credentials
- Portainer
- Uptime Kuma

---

## Phase 5 – Produktive Runtime

### Ziele

- Backend PROD
- Access PROD
- Runtime-Secrets
- Compose Cleanup

### Besondere Vorsicht

Recovery und Rollback vollständig prüfen.

---

## Phase 6 – Administrative Credentials

### Ziele

- MariaDB Root
- persönliche Admin-Zugänge
- finale Governance-Härtung

---

## Phase 7 – Cleanup

### Ziele

- Altuser entfernen
- Alt-Compose entfernen
- Klartext-Secrets entfernen
- historische Runtime bereinigen

---

# Runtime Rotation

## Zielmodell

Produktive Runtime-Secrets sollen langfristig getrennt geführt werden.

Compose-Dateien enthalten nur:

- Referenzen
- Variablennamen
- Runtime-Struktur

---

## Zielstruktur

```text id="mrtxj1"
/volume1/docker/secrets/emc
├── shared
├── mitglieder
├── finanzen
└── noten
```

---

# Rollback Governance

## Vor jeder Rotation erforderlich

- Backup vorhanden
- Recovery dokumentiert
- Rollback möglich
- Runtime validiert
- Abhängigkeiten bekannt

---

## Rollback muss möglich bleiben

Credential Rotation ohne Rollbackfähigkeit ist unzulässig.

---

# Dokumentationspflicht

## Pro Rotation dokumentieren

- Datum
- betroffene Systeme
- Verantwortlichkeit
- Recovery-Auswirkungen
- Rollbackstatus
- Runtime-Auswirkungen

---

# Zielzustand

Langfristiger Zielzustand:

- kontrollierte Credential Rotation
- recoveryfähige Runtime
- getrennte Runtime-Secrets
- keine produktiven Klartext-Secrets
- dokumentierte Recoverypfade
- nachvollziehbare Betriebsprozesse

Die Weiterentwicklung erfolgt evolutionär und recovery-sicher.
