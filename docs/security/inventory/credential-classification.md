# Runtime Secret Mapping

Status: Phase 9 Datenbank Rollenmodell / Access-Härtung

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

```text id="1z4v7u"
KeePass
=
Credential Source of Truth
```

---

## Infrastruktur Source of Truth

```text id="2c3u3j"
GitHub
└── emc-infra
```

---

## Betriebsrollen

```text id="w06qxr"
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

---

## Uptime Kuma

| Bereich           | Wert          |
| ----------------- | ------------- |
| Container         | `uptime-kuma` |
| Secret Typ        | Admin Login   |
| Kritikalität      | hoch          |
| Recovery-Relevanz | hoch          |

Recovery-kritische Daten:

```text id="l0f23p"
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
| Datenbank         | `emc_mitglieder`                   |
| Secret Typ        | DB_URL / DB_USERNAME / DB_PASSWORD |
| Kritikalität      | kritisch                           |
| Recovery-Relevanz | hoch                               |
| Credential Source | KeePass                            |

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

```text id="ukv0ll"
Backend DEV API
```

---

## Frontend PROD

Keine produktiven Secrets.

Abhängigkeit:

```text id="x9f5vb"
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

---

## PROD RW

| Bereich           | Wert                            |
| ----------------- | ------------------------------- |
| Anwendung         | Access Mitglieder PROD          |
| DB User           | `emc_access_mitglieder_prod_rw` |
| Datenbank         | `emc_mitglieder`                |
| Secret Typ        | ODBC Credential                 |
| Speicherort       | `dbconfig.ini`                  |
| Credential Source | KeePass                         |

---

## PROD RO

| Bereich           | Wert                            |
| ----------------- | ------------------------------- |
| Anwendung         | Access Mitglieder PROD          |
| DB User           | `emc_access_mitglieder_prod_ro` |
| Datenbank         | `emc_mitglieder`                |
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

---

## PROD

| Bereich           | Wert                          |
| ----------------- | ----------------------------- |
| Anwendung         | Access Finanzen PROD          |
| DB User           | `emc_access_finanzen_prod_rw` |
| Datenbank         | `emc_finanzen`                |
| Secret Typ        | ODBC Credential               |
| Speicherort       | `dbconfig.ini`                |
| Credential Source | KeePass                       |

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

```text id="7jql4j"
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

```text id="pr6bsz"
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

| Speicherort           | Status    |
| --------------------- | --------- |
| lokale `.env` Dateien | produktiv |
| Access `dbconfig.ini` | produktiv |
| `kuma.db`             | produktiv |
| KeePass               | produktiv |

---

## Nicht zulässig

Nicht erlaubt:

```text id="ey1dhg"
Passwörter in Git
Passwörter in Markdown
Passwörter in Commit-History
```

---

# Recovery-Abhängigkeiten

Credential-Änderungen müssen folgende Systeme berücksichtigen:

```text id="bq57yx"
MariaDB
Backup
Backend DEV
Backend PROD
Access Mitglieder
Access Finanzen
phpMyAdmin
Recovery
```

---

# Zielzustand

Aktueller Zielzustand:

```text id="nt9n3z"
Runtime sauber getrennt
Access sauber getrennt
Administration sauber getrennt
Recovery sauber getrennt
```

Credential Source:

```text id="z17zfr"
KeePass
```

Recovery Source:

```text id="79k2ha"
Recovery Dokumentation
```

Governance Source:

```text id="i8ukvn"
GitHub emc-infra
```

---

# Änderungslog

| Datum      | Änderung                                                              |
| ---------- | --------------------------------------------------------------------- |
| 2026-05-27 | Initiale Secret-Mapping-Dokumentation                                 |
| 2026-06-01 | Vollständige Überarbeitung nach Phase 9 Rollenmodell / Access-Härtung |
