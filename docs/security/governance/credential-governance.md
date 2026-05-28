# Credential Governance

## Zweck

Dieses Dokument definiert die verbindlichen Governance- und Betriebsregeln für Credentials, Secrets und Recovery der EMC Infrastruktur.

Ziel:

- kontrollierte Betriebsreife
- recovery-sichere Credential-Verwaltung
- nachvollziehbare Runtime-Strukturen
- kontrollierte Credential Rotation
- Vermeidung von Shadow-Secrets
- langfristig wartbare Betriebsarchitektur

Keine produktiven Secretwerte in diesem Dokument.

---

# Grundprinzipien

## Recovery First

Recoveryfähigkeit besitzt höhere Priorität als maximale Security-Härte.

Credential-Härtung darf niemals die Wiederherstellbarkeit gefährden.

---

## Evolutionäre Migration

Die Infrastruktur wird kontrolliert und schrittweise weiterentwickelt.

Keine Big-Bang-Migrationen.

---

## Keine Blindbereinigung

Historische Runtime-, Compose- oder Credential-Strukturen werden zuerst:

- inventarisiert
- bewertet
- recoverytechnisch eingeordnet

und erst danach entfernt.

---

## Fachliche Domänentrennung

Credentials und Runtime-Strukturen werden getrennt geführt für:

- shared
- mitglieder
- finanzen
- noten (später)

---

# Credential Source of Truth

## Zielmodell

KeePassXC bildet langfristig die zentrale Credential Source of Truth.

Dort werden dokumentiert:

- Zugangsdaten
- Recoveryinformationen
- Rotationshistorie
- Verantwortlichkeiten
- Runtime-Zuordnungen

---

## Nicht zulässige Secret-Orte

Produktive Secrets gehören nicht in:

- Git Repositories
- versionierte Compose-Dateien
- zufällige Textdateien
- Chatverläufe
- Browsernotizen
- historische Altstrukturen

---

# Runtime Secret Governance

## Zielmodell

Compose-Dateien enthalten langfristig nur noch:

- Variablennamen
- Referenzen
- Runtime-Struktur

Keine produktiven Secretwerte.

---

## Zielstruktur Runtime-Secrets

```text id="v5hqgt"
/volume1/docker/secrets/emc
├── shared
├── mitglieder
├── finanzen
└── noten
```

---

# Recovery Governance

## Recovery vor Rotation

Vor jeder kritischen Credential-Änderung:

- Recoverypfad prüfen
- Backup prüfen
- Rollbackfähigkeit prüfen
- gekoppelte Credentials prüfen

---

## Verbotene Paralleländerungen

Nicht gleichzeitig ändern:

- Fritz!Box
- WireGuard
- NAS SSH
- MariaDB Root
- Backend PROD
- Access PROD

---

## Break-Glass

Break-Glass-Zugänge müssen:

- dokumentiert
- nachvollziehbar
- recoveryfähig
- kontrolliert zugreifbar

sein.

---

# Runtime Governance

## Compose Source of Truth

Die operative Compose-Governance erfolgt über:

```text id="dxoc72"
emc-infra
```

---

## Produktive Runtime

Die operative Runtime befindet sich unter:

```text id="dwjlsd"
/volume1/docker
```

---

## Historische Runtime-Strukturen

Historische Compose- und Runtime-Strukturen bleiben erhalten bis:

- Recovery validiert
- Migration abgeschlossen
- Runtime standardisiert
- Rollback unnötig

ist.

---

# MariaDB Governance

## Zielmodell

MariaDB Credentials werden langfristig fachlich getrennt geführt:

### Shared

- Root
- Recovery
- Backup
- Monitoring

### Mitglieder

- Backend
- Access
- Reporting
- DEV
- PROD

### Finanzen

- Access
- Reporting
- DEV
- PROD

### später Noten

- Runtime
- Reporting
- Services

---

## Credential Rotation

Credential Rotation erfolgt:

- kontrolliert
- recoverybewertet
- domänengetrennt
- schrittweise

---

# Monitoring Governance

## Uptime Kuma

Uptime Kuma ist Bestandteil der produktiven Betriebsarchitektur.

Die Datei:

```text id="k44h8o"
kuma.db
```

ist recoverykritisch.

---

# GitHub Governance

## GitHub Rolle

GitHub dient als:

- Source of Truth
- Infrastruktur-Dokumentation
- Governance-Plattform
- Compose-Repository

Nicht als Secret Storage.

---

## Zielzustand

Später:

- 2FA
- Recoverycodes
- dokumentierte Recoverypfade

kontrolliert einführen.

---

# Zielarchitektur

Die EMC Infrastruktur entwickelt sich langfristig zu einer:

- recoveryfähigen
- domänengetrennten
- kontrolliert betreibbaren
- dokumentierten

Betriebsplattform.

Die Weiterentwicklung erfolgt evolutionär und recovery-sicher.
