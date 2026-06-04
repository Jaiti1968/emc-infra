# Runtime Secret Mapping

Status: Phase 11 Datenbank Naming Migration

## Zweck

Dieses Dokument beschreibt die Zuordnung zwischen:

- Runtime-Komponenten
- Datenbankbenutzern
- Runtime-Secrets
- Compose-Konfigurationen
- Secret-Speicherorten
- fachlichen Domänen
- Recovery-Abhängigkeiten

Dieses Dokument enthält bewusst keine produktiven Secretwerte.

---

# Grundprinzipien

## Source of Truth

```text
KeePass
=
Credential Source of Truth
```

---

## Infrastruktur Source of Truth

```text
GitHub
└── emc-infra
```

---

## Betriebsrollen

```text
Runtime
Access
Administration
Recovery
```

werden strikt getrennt.

---

# Shared Infrastruktur

## MariaDB

| Bereich           | Wert            |
| ----------------- | --------------- |
| Container         | `mariadb`       |
| Datenbank         | shared          |
| Secret Typ        | Root Credential |
| Kritikalität      | kritisch        |
| Recovery-Relevanz | kritisch        |
| Credential Source | KeePass         |

Bemerkungen:

- Root nur noch lokal nutzbar
- `root@localhost`
- `root@%` entfernt
- Datenbank-Namensstandard seit Phase 11:

```text
<fachbereich>_<umgebung>
```

Produktive Datenbanken:

```text
emc_mitglieder_prod
emc_finanzen_prod
```

Entwicklungsdatenbanken:

```text
emc_mitglieder_dev
emc_finanzen_dev
```

---

## MariaDB Backup

| Bereich           | Wert              |
| ----------------- | ----------------- |
| Container         | `mariadb-backup`  |
| DB User           | `emc_backup`      |
| Secret Typ        | Backup Credential |
| Kritikalität      | hoch              |
| Recovery-Relevanz | hoch              |
| Credential Source | KeePass           |

Verwendete Datenbanken:

```text
emc_mitglieder_prod
emc_mitglieder_dev
emc_finanzen_prod
emc_finanzen_dev
```

---

## Uptime Kuma

| Bereich           | Wert          |
| ----------------- | ------------- |
| Container         | `uptime-kuma` |
| Secret Typ        | Admin Login   |
| Kritikalität      | hoch          |
| Recovery-Relevanz | hoch          |

Recovery-kritische Daten:

```text
kuma.db
```

---

## Portainer

| Bereich           | Wert        |
| ----------------- | ----------- |
| Container         | `portainer` |
| Secret Typ        | Admin Login |
| Kritikalität      | hoch        |
| Recovery-Relevanz | hoch        |

---

# Mitgliederverwaltung

## Backend DEV

| Bereich           | Wert                               |
| ----------------- | ---------------------------------- |
| Container         | `emc-mitglieder-backend-dev`       |
| DB User           | `emc_mitglieder_dev_rw`            |
| Datenbank         | `emc_mitglieder_dev`               |
| Secret Typ        | DB_URL / DB_USERNAME / DB_PASSWORD |
| Kritikalität      | hoch                               |
| Recovery-Relevanz | mittel                             |
| Credential Source | KeePass                            |

---

## Backend PROD

| Bereich           | Wert                               |
| ----------------- | ---------------------------------- |
| Container         | `emc-mitglieder-backend-prod`      |
| DB User           | `emc_mitglieder_prod_rw`           |
| Datenbank         | `emc_mitglieder_prod`              |
| Secret Typ        | DB_URL / DB_USERNAME / DB_PASSWORD |
| Kritikalität      | kritisch                           |
| Recovery-Relevanz | hoch                               |
| Credential Source | KeePass                            |

Runtime-Konfiguration:

```text
DB_URL=jdbc:mariadb://mariadb:3306/emc_mitglieder_prod
DB_USERNAME=emc_mitglieder_prod_rw
```

Validiert:

```text
Phase 11 Datenbank Naming Migration
```

---

## Backend Integration Tests

| Bereich           | Wert                  |
| ----------------- | --------------------- |
| Verwendung        | Integrationstests     |
| DB User           | `emc_backend_test_rw` |
| Datenbank         | Testumgebung          |
| Credential Source | KeePass               |

---

## Frontend DEV

Keine produktiven Secrets.

Abhängigkeit:

```text
Backend DEV API
```

---

## Frontend PROD

Keine produktiven Secrets.

Abhängigkeit:

```text
Backend PROD API
```

---

# Access Mitglieder

## DEV

| Bereich           | Wert                           |
| ----------------- | ------------------------------ |
| Anwendung         | Access Mitglieder DEV          |
| DB User           | `emc_access_mitglieder_dev_rw` |
| Datenbank         | `emc_mitglieder_dev`           |
| Secret Typ        | ODBC Credential                |
| Speicherort       | `dbconfig.ini`                 |
| Credential Source | KeePass                        |

NAS Source of Truth:

```text
/volume1/apps/access/emc_mitglieder/dev/dbconfig.ini
```

---

## PROD RW

| Bereich           | Wert                            |
| ----------------- | ------------------------------- |
| Anwendung         | Access Mitglieder PROD          |
| DB User           | `emc_access_mitglieder_prod_rw` |
| Datenbank         | `emc_mitglieder_prod`           |
| Secret Typ        | ODBC Credential                 |
| Speicherort       | `dbconfig_prod.ini`             |
| Credential Source | KeePass                         |

NAS Source of Truth:

```text
/volume1/apps/access/emc_mitglieder/prod/dbconfig_prod.ini
```

---

## PROD RO

| Bereich           | Wert                            |
| ----------------- | ------------------------------- |
| Anwendung         | Access Mitglieder PROD          |
| DB User           | `emc_access_mitglieder_prod_ro` |
| Datenbank         | `emc_mitglieder_prod`           |
| Verwendung        | Read Only                       |
| Credential Source | KeePass                         |

---

# Access Finanzen

## DEV

| Bereich           | Wert                         |
| ----------------- | ---------------------------- |
| Anwendung         | Access Finanzen DEV          |
| DB User           | `emc_access_finanzen_dev_rw` |
| Datenbank         | `emc_finanzen_dev`           |
| Secret Typ        | ODBC Credential              |
| Speicherort       | `dbconfig.ini`               |
| Credential Source | KeePass                      |

NAS Source of Truth:

```text
/volume1/apps/access/emc_finanzen/dev/dbconfig.ini
```

---

## PROD

| Bereich           | Wert                          |
| ----------------- | ----------------------------- |
| Anwendung         | Access Finanzen PROD          |
| DB User           | `emc_access_finanzen_prod_rw` |
| Datenbank         | `emc_finanzen_prod`           |
| Secret Typ        | ODBC Credential               |
| Speicherort       | `dbconfig_prod.ini`           |
| Credential Source | KeePass                       |

NAS Source of Truth:

```text
/volume1/apps/access/emc_finanzen/prod/dbconfig_prod.ini
```

---

# Administration

## Primärer DBA

| Bereich           | Wert                    |
| ----------------- | ----------------------- |
| Benutzer          | `JoergTitz`             |
| Rolle             | `role_emc_admin`        |
| Verwendung        | tägliche Administration |
| Credential Source | KeePass                 |

Eigenschaften:

```text
kein GRANT OPTION
kein Superuser
```

---

## phpMyAdmin Administrator

| Bereich           | Wert                   |
| ----------------- | ---------------------- |
| Benutzer          | `emc_phpmyadmin_admin` |
| Verwendung        | phpMyAdmin GUI         |
| Credential Source | KeePass                |

Grund:

```text
phpMyAdmin 5.2.3 Rollen-Inkompatibilität
```

---

# Recovery

## Break-Glass

| Bereich           | Wert                |
| ----------------- | ------------------- |
| Benutzer          | `root@localhost`    |
| Verwendung        | Recovery            |
| Rechte            | vollständige Rechte |
| Credential Source | KeePass             |

---

# Aktuelle Secret Speicherorte

## Produktive Runtime

| Speicherort                | Status    |
| -------------------------- | --------- |
| lokale `.env` Dateien      | produktiv |
| Access `dbconfig.ini`      | produktiv |
| Access `dbconfig_prod.ini` | produktiv |
| `kuma.db`                  | produktiv |
| KeePass                    | produktiv |

---

## Nicht zulässig

Nicht erlaubt:

```text
Passwörter in Git
Passwörter in Markdown
Passwörter in Commit-History
```

---

# Recovery-Abhängigkeiten

Credential-Änderungen müssen folgende Systeme berücksichtigen:

```text
MariaDB
Backup
Backend DEV
Backend PROD
Access Mitglieder
Access Finanzen
phpMyAdmin
Recovery
Monitoring
```

---

# Zielzustand

Aktueller Zielzustand:

```text
Runtime sauber getrennt
Access sauber getrennt
Administration sauber getrennt
Recovery sauber getrennt
```

Credential Source:

```text
KeePass
```

Recovery Source:

```text
Recovery Dokumentation
```

Governance Source:

```text
GitHub emc-infra
```

---

# Änderungslog

| Datum      | Änderung                                                                                                                                 |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| 2026-05-27 | Initiale Secret-Mapping-Dokumentation                                                                                                    |
| 2026-06-01 | Vollständige Überarbeitung nach Phase 9 Rollenmodell / Access-Härtung                                                                    |
| 2026-06-04 | Datenbank Naming Migration auf \_prod abgeschlossen, Backend PROD, Access PROD, Backup und Monitoring auf neue Datenbanknamen umgestellt |

```

```
