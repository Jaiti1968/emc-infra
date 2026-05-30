# Recovery Dependencies

## Zweck

Dieses Dokument beschreibt die Recovery-Abhängigkeiten, Recovery-Ketten und kritischen Kopplungen der EMC Infrastruktur.

Ziel:

- Recovery-Reihenfolgen nachvollziehbar dokumentieren
- Lockout-Risiken minimieren
- gekoppelte Credentials identifizieren
- sichere Credential Rotation ermöglichen
- Break-Glass-Recovery absichern

Keine produktiven Secretwerte in diesem Dokument.

---

# Grundprinzip

Recoveryfähigkeit hat höhere Priorität als maximale Security-Härte.

Credential-Änderungen erfolgen nur kontrolliert und mit Recoverybewertung.

---

# Zentrale Recovery-Kette

## Aktuelle Hauptkette

```text
Internet
→ Fritz!Box
→ WireGuard
→ Heimnetz
→ NAS
→ SSH / sudo
→ Docker Runtime
→ MariaDB
→ Anwendungen
```

Diese Kette bildet den zentralen Remote-Recoverypfad der EMC Infrastruktur.

---

# Shared Infrastruktur

## Fritz!Box

| Bereich           | Bewertung                         |
| ----------------- | --------------------------------- |
| Kritikalität      | kritisch                          |
| Recovery-Relevanz | kritisch                          |
| Funktion          | Internet-Gateway / WireGuard Host |
| Abhängigkeiten    | Internetzugang                    |
| Risiken           | Verlust Remotezugriff             |
| Aktueller Status  | Konfigurationsbackup dokumentiert |

### Kritische Kopplungen

Nicht gleichzeitig ändern:

- Fritz!Box Admin Passwort
- WireGuard-Konfiguration
- DynDNS-/MyFritz-Zugänge

---

## WireGuard

| Bereich           | Bewertung                           |
| ----------------- | ----------------------------------- |
| Kritikalität      | kritisch                            |
| Recovery-Relevanz | kritisch                            |
| Funktion          | Remotezugriff Heimnetz              |
| Abhängigkeiten    | Fritz!Box                           |
| Risiken           | vollständiger Verlust Fernzugriff   |
| Aktueller Status  | dokumentierter Recovery-Bestandteil |

### Kritische Kopplungen

Nicht gleichzeitig ändern:

- WireGuard
- NAS SSH Zugang
- Fritz!Box Zugriff

---

## NAS / SSH / sudo

| Bereich           | Bewertung                       |
| ----------------- | ------------------------------- |
| Kritikalität      | kritisch                        |
| Recovery-Relevanz | kritisch                        |
| Funktion          | zentrale Betriebsadministration |
| Hauptzugang       | `JaitiNissi1968`                |
| Root Zugriff      | via sudo                        |
| SSH Root Login    | deaktiviert                     |

### Kritische Kopplungen

Nicht gleichzeitig ändern:

- SSH Zugang
- WireGuard
- Fritz!Box Zugriff

---

## GitHub

| Bereich           | Bewertung                                 |
| ----------------- | ----------------------------------------- |
| Kritikalität      | hoch                                      |
| Recovery-Relevanz | hoch                                      |
| Funktion          | Source of Truth                           |
| Risiken           | Verlust Infrastruktur-Dokumentation       |
| Aktueller Status  | Repository Bestandteil der Recovery-Kette |

### Kritische Kopplungen

Nicht gleichzeitig ändern:

- Passwort
- 2FA
- Recoverycodes

---

## Security-Artefakte / Syncthing

| Bereich           | Bewertung                          |
| ----------------- | ---------------------------------- |
| Kritikalität      | hoch                               |
| Recovery-Relevanz | hoch                               |
| Funktion          | Credential- und Recovery-Artefakte |
| Primärquelle      | Laptop                             |
| Replik            | NAS                                |
| Synchronisation   | Syncthing                          |

### Recovery-Pfade

Primär:

```text
C:\Users\Joerg\Documents\Security
```

NAS-Replik:

```text
/volume1/home/JaitiNissi1968/Security
```

### Kritische Kopplungen

Nicht gleichzeitig ändern:

- KeePassXC Struktur
- Syncthing Konfiguration
- Recovery-Dokumentation

### Grundsatz

```text
Laptop = Source of Truth
NAS = Replik
Desktop = zusätzlicher Client
```

---

# Docker Runtime

## Docker / Compose

| Bereich           | Bewertung         |
| ----------------- | ----------------- |
| Kritikalität      | hoch              |
| Recovery-Relevanz | hoch              |
| Funktion          | Runtime-Steuerung |
| Abhängigkeiten    | NAS, Storage      |
| Risiken           | Runtime-Ausfall   |

### Recovery-Relevante Komponenten

- Compose-Dateien
- Runtime-Secrets
- Volumes
- Netzwerke
- Portainer

---

## Portainer

| Bereich           | Bewertung                              |
| ----------------- | -------------------------------------- |
| Kritikalität      | hoch                                   |
| Recovery-Relevanz | mittel                                 |
| Funktion          | Runtime-Verwaltung                     |
| Risiken           | Verlust komfortabler Runtime-Steuerung |

### Hinweis

Portainer ist operativ wichtig, aber nicht primärer Recoverypfad.

Compose Source of Truth bleibt wichtiger.

---

## Uptime Kuma

| Bereich                 | Bewertung             |
| ----------------------- | --------------------- |
| Kritikalität            | hoch                  |
| Recovery-Relevanz       | hoch                  |
| Funktion                | Monitoring / Alerting |
| Recoverykritische Datei | `kuma.db`             |

### Risiken

Verlust von:

- Monitoring-Konfiguration
- Telegram Alerts
- Runtime-Überwachung

---

# MariaDB Recovery

## MariaDB Root

| Bereich           | Bewertung                |
| ----------------- | ------------------------ |
| Kritikalität      | kritisch                 |
| Recovery-Relevanz | kritisch                 |
| Funktion          | DB Break-Glass           |
| Risiken           | vollständiger DB-Lockout |

### Kritische Kopplungen

Nicht gleichzeitig ändern:

- MariaDB Root
- Backend PROD Runtime
- Access PROD Runtime

---

## Persönlicher Admin User

| Bereich           | Bewertung               |
| ----------------- | ----------------------- |
| User              | `JoergTitz`             |
| Kritikalität      | kritisch                |
| Recovery-Relevanz | hoch                    |
| Funktion          | tägliche Administration |

### Risiken

- Verlust regulärer Administration
- erschwerte Recovery
- Abhängigkeit von Root

---

# Mitglieder Domäne

## Backend PROD

| Bereich           | Bewertung          |
| ----------------- | ------------------ |
| Kritikalität      | kritisch           |
| Recovery-Relevanz | hoch               |
| Datenbank         | `emc_mitglieder`   |
| Runtime User      | `emc_backend_prod` |

### Abhängigkeiten

```text
Compose
→ Runtime Secret
→ DB User
→ MariaDB
→ Docker Runtime
```

### Kritische Kopplungen

Nicht gleichzeitig ändern:

- DB Passwort
- Compose Runtime
- Runtime Secret Struktur

---

## Access Mitglieder PROD

| Bereich           | Bewertung           |
| ----------------- | ------------------- |
| Kritikalität      | kritisch            |
| Recovery-Relevanz | hoch                |
| Runtime           | Access              |
| DB User           | `emc_mitglieder_rw` |

### Abhängigkeiten

```text
dbconfig.ini
→ ODBC
→ Netzwerk
→ MariaDB
```

### Risiken

- sofortiger Runtime-Ausfall
- Access Loginfehler
- Produktivstillstand

---

# Finanzdomäne

## Access Finanzen PROD

| Bereich           | Bewertung         |
| ----------------- | ----------------- |
| Kritikalität      | kritisch          |
| Recovery-Relevanz | hoch              |
| Runtime           | Access            |
| DB User           | `emc_finanzen_rw` |

### Abhängigkeiten

```text
dbconfig.ini
→ ODBC
→ Netzwerk
→ MariaDB
```

### Risiken

- produktiver Finanzausfall
- Access Runtimefehler
- DB Loginprobleme

---

# Recovery-Reihenfolge

## Empfohlene Reihenfolge bei Komplettrecovery

### 1. Netzwerk

- Fritz!Box
- Internet
- WireGuard

---

### 2. Security-Artefakte

- KeePassXC
- Security-Verzeichnis
- Syncthing
- Recovery-Dokumentation

---

### 3. NAS

- SSH
- sudo
- Storage
- Docker Runtime

---

### 4. Datenbank

- MariaDB
- DB Runtime
- Restorefähigkeit

---

### 5. Runtime

- Backend
- Frontend
- Access
- Monitoring

---

### 6. Komfortsysteme

- Portainer
- Uptime Kuma
- Telegram Alerts

---

# Single Points of Failure

| Bereich             | SPOF             |
| ------------------- | ---------------- |
| NAS Administration  | `JaitiNissi1968` |
| DB Recovery         | MariaDB Root     |
| Netzwerk Recovery   | Fritz!Box        |
| Remote Recovery     | WireGuard        |
| Monitoring Recovery | `kuma.db`        |
| Governance          | GitHub Zugriff   |
| Credential Recovery | KeePassXC        |

---

# Wichtige Betriebsregeln

## Keine Blindrotation

Credentials niemals ohne Recoverybewertung ändern.

---

## Keine parallele Recovery-Änderung

Besonders kritisch:

- Fritz!Box
- WireGuard
- NAS SSH
- MariaDB Root
- Backend PROD
- Access PROD
- KeePassXC Struktur

---

## Recovery vor Cleanup

Historische Runtime- und Compose-Strukturen erst entfernen, wenn:

- Recovery dokumentiert
- Runtime validiert
- Secret-Struktur standardisiert
- Rollback möglich

ist.

---

# Gesamtbewertung

Die EMC Infrastruktur besitzt:

- getestete Restorefähigkeit
- dokumentierte Recoverypfade
- Security-Artefakt-Replikation
- strukturierte Runtime
- kontrollierte Betriebsarchitektur
- dokumentierte Break-Glass-Prozesse

Wesentliche Risiken bestehen aktuell primär in:

- zentralisierten Recovery-Abhängigkeiten
- verbleibenden Single Points of Failure
- historisch gewachsenen Runtime-Strukturen

Die weitere Härtung erfolgt evolutionär und recovery-sicher.

---

# Änderungslog

| Datum      | Änderung                                                                            |
| ---------- | ----------------------------------------------------------------------------------- |
| 2026-05-26 | Initiale Recovery-Dependency-Dokumentation                                          |
| 2026-05-30 | Phase-8-Erweiterung: Security-Artefakte und Syncthing in Recovery-Kette aufgenommen |
