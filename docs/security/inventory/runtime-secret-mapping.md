# Runtime Secret Mapping

## Zweck

Dieses Dokument beschreibt die Zuordnung zwischen:

- Runtime-Komponenten
- Datenbank-Usern
- Runtime-Secrets
- Compose-Konfigurationen
- Secret-Speicherorten
- fachlichen Domänen
- Recovery-Abhängigkeiten

Keine produktiven Secretwerte in diesem Dokument.

---

# Architekturprinzipien

## Zielmodell

Produktive Secrets sollen langfristig:

- nicht in Git
- nicht in produktiven Compose-Dateien
- nicht in historischen Altstrukturen

liegen.

Ziel:

- Runtime-Secrets getrennt
- Compose clean
- zentrale Credential Governance
- recovery-sichere Struktur

---

# Shared Infrastruktur

## MariaDB Container

| Bereich               | Wert                             |
| --------------------- | -------------------------------- |
| Container             | `mariadb`                        |
| Secret Typ            | `MYSQL_ROOT_PASSWORD`            |
| aktueller Speicherort | Container Env + Compose          |
| Domäne                | shared                           |
| Kritikalität          | kritisch                         |
| Recovery-Relevanz     | hoch                             |
| Zielzustand           | Runtime Secret außerhalb Compose |

---

## MariaDB Backup

| Bereich               | Wert                       |
| --------------------- | -------------------------- |
| Container             | `mariadb-backup`           |
| DB User               | Backup Runtime User        |
| Secret Typ            | `MYSQL_USER`, `MYSQL_PASS` |
| aktueller Speicherort | Container Env + Compose    |
| Domäne                | shared                     |
| Kritikalität          | hoch                       |
| Recovery-Relevanz     | hoch                       |
| Zielzustand           | separates Runtime Secret   |

---

## Uptime Kuma

| Bereich               | Wert                       |
| --------------------- | -------------------------- |
| Container             | `uptime-kuma`              |
| Secret Typ            | Admin / Telegram           |
| aktueller Speicherort | vermutlich `kuma.db`       |
| Domäne                | shared                     |
| Kritikalität          | hoch                       |
| Recovery-Relevanz     | hoch                       |
| Hinweis               | `kuma.db` recoverykritisch |

---

## Portainer

| Bereich               | Wert             |
| --------------------- | ---------------- |
| Container             | `portainer`      |
| Secret Typ            | Admin Login      |
| aktueller Speicherort | Portainer Volume |
| Domäne                | shared           |
| Kritikalität          | hoch             |
| Recovery-Relevanz     | hoch             |

---

# Mitglieder Domäne

## Backend PROD

| Bereich               | Wert                                   |
| --------------------- | -------------------------------------- |
| Container             | `emc-mitglieder-backend-prod`          |
| DB User               | `emc_backend_prod`                     |
| Secret Typ            | `DB_URL`, `DB_USERNAME`, `DB_PASSWORD` |
| aktueller Speicherort | Container Env + Compose                |
| Datenbank             | `emc_mitglieder`                       |
| Kritikalität          | kritisch                               |
| Recovery-Relevanz     | hoch                                   |
| Zielzustand           | separates Runtime Secret               |

---

## Backend DEV

| Bereich               | Wert                                   |
| --------------------- | -------------------------------------- |
| Container             | `emc-mitglieder-backend-dev`           |
| DB User               | `emc_backend_dev_rw`                   |
| Secret Typ            | `DB_URL`, `DB_USERNAME`, `DB_PASSWORD` |
| aktueller Speicherort | Container Env + Compose                |
| Datenbank             | `emc_mitglieder_dev`                   |
| Kritikalität          | hoch                                   |
| Recovery-Relevanz     | mittel                                 |
| Zielzustand           | separates Runtime Secret               |

---

## Frontend PROD

| Bereich              | Wert                              |
| -------------------- | --------------------------------- |
| Container            | `emc-mitglieder-frontend-prod`    |
| Secret Typ           | aktuell keine produktiven Secrets |
| Runtime-Abhängigkeit | Backend API                       |
| Kritikalität         | mittel                            |
| Recovery-Relevanz    | mittel                            |

---

## Frontend DEV

| Bereich              | Wert                              |
| -------------------- | --------------------------------- |
| Container            | `emc-mitglieder-frontend-dev`     |
| Secret Typ           | aktuell keine produktiven Secrets |
| Runtime-Abhängigkeit | Backend DEV API                   |
| Kritikalität         | niedrig                           |
| Recovery-Relevanz    | niedrig                           |

---

## Access Mitglieder PROD

| Bereich               | Wert                           |
| --------------------- | ------------------------------ |
| Runtime               | Access Mitglieder PROD         |
| DB User               | `emc_mitglieder_rw`            |
| Secret Typ            | ODBC Runtime Credentials       |
| aktueller Speicherort | `dbconfig.ini`                 |
| Datenbank             | `emc_mitglieder`               |
| Kritikalität          | kritisch                       |
| Recovery-Relevanz     | hoch                           |
| Zielzustand           | dokumentierte Runtime-Struktur |

---

## Access Mitglieder DEV

| Bereich               | Wert                     |
| --------------------- | ------------------------ |
| Runtime               | Access Mitglieder DEV    |
| DB User               | `emc_mitglieder_dev_rw`  |
| Secret Typ            | ODBC Runtime Credentials |
| aktueller Speicherort | `dbconfig_dev.ini`       |
| Datenbank             | `emc_mitglieder_dev`     |
| Kritikalität          | hoch                     |
| Recovery-Relevanz     | mittel                   |

---

# Finanzdomäne

## Access Finanzen PROD

| Bereich               | Wert                           |
| --------------------- | ------------------------------ |
| Runtime               | Access Finanzen PROD           |
| DB User               | `emc_finanzen_rw`              |
| Secret Typ            | ODBC Runtime Credentials       |
| aktueller Speicherort | `dbconfig.ini`                 |
| Datenbank             | `emc_finanzen`                 |
| Kritikalität          | kritisch                       |
| Recovery-Relevanz     | hoch                           |
| Zielzustand           | dokumentierte Runtime-Struktur |

---

## Access Finanzen DEV

| Bereich               | Wert                     |
| --------------------- | ------------------------ |
| Runtime               | Access Finanzen DEV      |
| DB User               | `emc_finanzen_dev_rw`    |
| Secret Typ            | ODBC Runtime Credentials |
| aktueller Speicherort | `dbconfig_dev.ini`       |
| Datenbank             | `emc_finanzen_dev`       |
| Kritikalität          | hoch                     |
| Recovery-Relevanz     | mittel                   |

---

# Aktuelle Secret Speicherorte

## Operative Runtime

| Ort                        | Status | Bewertung                |
| -------------------------- | ------ | ------------------------ |
| Container Env              | aktiv  | Übergangslösung          |
| produktive Compose-Dateien | aktiv  | enthält Klartext-Secrets |
| Access `dbconfig.ini`      | aktiv  | produktiv kritisch       |
| `kuma.db`                  | aktiv  | Monitoring-kritisch      |

---

## Git-Arbeitsstruktur

| Ort                          | Status    | Bewertung                              |
| ---------------------------- | --------- | -------------------------------------- |
| lokale `.env` Dateien        | vorhanden | Zielmodell-nah                         |
| versionierte Compose-Dateien | vorhanden | derzeit teilweise mit Klartext-Secrets |

---

## Historische Altstrukturen

| Ort                        | Status    | Bewertung          |
| -------------------------- | --------- | ------------------ |
| alte Home-Compose-Struktur | vorhanden | Altlast            |
| Portainer Compose Exporte  | vorhanden | Recovery-Artefakte |
| Recovery Compose Snapshots | vorhanden | bewusst behalten   |

---

# Zielstruktur Runtime Secrets

## Shared

```text
/volume1/docker/secrets/emc/shared
```
