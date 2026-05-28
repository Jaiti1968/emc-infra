# Secret Storage Standard

## Zweck

Dieses Dokument definiert die verbindlichen Standards für Speicherung, Strukturierung und Governance von Secrets innerhalb der EMC Infrastruktur.

Ziel:

- nachvollziehbare Runtime-Secret-Struktur
- Vermeidung von Shadow-Secrets
- recoveryfähige Secret-Verwaltung
- kontrollierte Credential Rotation
- saubere Domänentrennung
- Vermeidung produktiver Klartext-Secrets in Git

Keine produktiven Secretwerte in diesem Dokument.

---

# Grundprinzipien

## Keine produktiven Secrets im Git

Produktive Secrets dürfen niemals in versionierten Dateien gespeichert werden.

Dazu gehören insbesondere:

- `.env`
- Compose-Dateien
- Exportdateien
- Backup-Dateien
- Dokumentationen
- Screenshots

---

## Keine produktiven Klartext-Secrets in Compose-Dateien

Compose-Dateien enthalten langfristig nur:

- Variablennamen
- Referenzen
- Runtime-Struktur

Beispiel:

```yaml
DB_PASSWORD: ${DB_PASSWORD}
```

Nicht zulässig:

```yaml
DB_PASSWORD: meinPasswort123
```

---

## Recovery First

Secret-Strukturen müssen recoveryfähig bleiben.

Credential-Härtung darf keine Lockout-Situation erzeugen.

---

## Fachliche Domänentrennung

Secrets werden getrennt geführt für:

- shared
- mitglieder
- finanzen
- noten (später)

---

# Zielstruktur Runtime-Secrets

## Basisstruktur

```text
/volume1/docker/secrets/emc
├── shared
├── mitglieder
├── finanzen
└── noten
```

---

# Shared Infrastruktur

## Zielstruktur

```text
/volume1/docker/secrets/emc/shared
├── mariadb.env
├── mariadb-backup.env
├── monitoring.env
├── portainer.env
└── recovery.env
```

---

# Mitglieder Domäne

## Zielstruktur

```text
/volume1/docker/secrets/emc/mitglieder
├── backend-prod.env
├── backend-dev.env
├── frontend-prod.env
├── frontend-dev.env
├── access-prod.env
└── access-dev.env
```

---

# Finanzdomäne

## Zielstruktur

```text
/volume1/docker/secrets/emc/finanzen
├── access-prod.env
└── access-dev.env
```

---

# Spätere Notendomäne

## Reservierte Struktur

```text
/volume1/docker/secrets/emc/noten
```

---

# Zulässige Secret-Orte

## Erlaubt

| Ort                    | Bewertung                           |
| ---------------------- | ----------------------------------- |
| KeePassXC              | zentrale Credential Source of Truth |
| Runtime Secret Dateien | operativ zulässig                   |
| Access `dbconfig.ini`  | bewusst produktiv genutzt           |
| Uptime Kuma `kuma.db`  | technisch erforderlich              |

---

# Nicht zulässige Secret-Orte

## Nicht erlaubt

| Ort                          | Bewertung |
| ---------------------------- | --------- |
| Git Repositories             | verboten  |
| produktive Compose-Dateien   | verboten  |
| zufällige Home-Verzeichnisse | verboten  |
| Chatverläufe                 | verboten  |
| Screenshots                  | verboten  |
| Textdateien ohne Governance  | verboten  |

---

# Runtime Governance

## Compose Governance

Compose-Dateien beschreiben:

- Runtime-Struktur
- Netzwerke
- Volumes
- Referenzen

Nicht:

- produktive Passwortwerte

---

## Runtime-Secrets

Runtime-Secrets werden:

- lokal gespeichert
- nicht versioniert
- recoverybewertet
- domänengetrennt

geführt.

---

# Historische Altstrukturen

## Übergangsarchitektur

Aktuell existieren historische Runtime-Strukturen mit Klartext-Secrets.

Diese werden:

- inventarisiert
- bewertet
- kontrolliert migriert
- erst danach entfernt

---

# MariaDB Runtime Secrets

## Shared

### Beispiele

- Root Runtime
- Backup Runtime
- Monitoring Runtime

---

## Mitglieder

### Beispiele

- Backend PROD
- Backend DEV
- Access PROD
- Access DEV

---

## Finanzen

### Beispiele

- Access PROD
- Access DEV

---

# Recovery-Relevanz

## Kritische Secretbereiche

Besonders recoverykritisch:

- MariaDB Root
- NAS Zugriff
- WireGuard
- Fritz!Box
- Access Runtime
- Backend PROD Runtime
- `kuma.db`

---

# Credential Rotation

## Grundprinzip

Secrets werden niemals unkontrolliert oder parallel rotiert.

---

## Vor Rotation prüfen

- Recoverypfad
- Rollbackfähigkeit
- gekoppelte Credentials
- Runtime-Abhängigkeiten
- Compose-Abhängigkeiten

---

# Zielzustand

Langfristiger Zielzustand:

- klare Runtime-Secret-Struktur
- keine produktiven Klartext-Secrets im Git
- recoveryfähige Secret Governance
- fachlich getrennte Runtime-Secrets
- kontrollierte Rotation
- dokumentierte Recoverypfade

Die Weiterentwicklung erfolgt evolutionär und recovery-sicher.
