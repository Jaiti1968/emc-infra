# db-restore.md

# DB Restore Runbook

Status: Phase 2 Skeleton

## Zweck

Dieses Runbook beschreibt die Wiederherstellung von MariaDB-Datenbanken im EMC NAS Betriebsstandard.

Abgedeckte Szenarien:

- Einzelne Datenbank wiederherstellen
- Restore-Test in isolierter Ziel-DB
- vollständige DB-Wiederherstellung nach Datenverlust

Nicht abgedeckt:

- Point-in-Time Recovery
- Replikation
- HA / Failover

---

## Schutzgüter

Produktiv kritisch:

- emc_finanzen
- emc_mitglieder

Zusätzlich gesichert:

- emc_finanzen_dev
- emc_mitglieder_dev

Nicht Bestandteil:

- emc_mitglieder_test
- testdb

---

## Voraussetzungen

Benötigt:

- NAS Admin / SSH Zugriff
- Docker Zugriff
- MariaDB Root oder administrativer DB User
- Backup-Artefakte
- Break-Glass Zugang

---

## Backup-Artefakte

Standard:

```text
latest.emc_finanzen.sql.gz
latest.emc_finanzen_dev.sql.gz
latest.emc_mitglieder.sql.gz
latest.emc_mitglieder_dev.sql.gz
```

Historischer Backup-Pfad (Stand Phase 2):

```text
/volume1/home/JaitiNissi1968/docker/backups/mariadb
```

Zielstandard ggf. später abweichend.

---

## Restore-Szenarien

### 1. Restore-Test in isolierte Testdatenbank

Ziel:

Backup-Validierung ohne Produktivrisiko.

Schritte:

- Test-Zieldatenbank anlegen
- Dump entpacken
- Import durchführen
- Login testen
- Tabellen prüfen
- Test-Zieldatenbank löschen

---

### 2. Einzel-DB Restore

Beispiele:

- emc_mitglieder
- emc_finanzen

Schritte:

- Incident bewerten
- korrektes Backup identifizieren
- Produktivwirkung prüfen
- Restore durchführen
- Anwendung validieren

---

### 3. Full DB Recovery

Szenario:

MariaDB Datenverlust / Volume-Verlust

Schritte:

- MariaDB betriebsfähig herstellen
- Produktivdatenbanken wiederherstellen
- Validierung

---

## Validierung

Pflicht:

- DB Login erfolgreich
- Tabellen vorhanden
- Anwendung erreichbar
- fachlicher Grundtest erfolgreich

---

## Rollback

Abhängig vom Szenario:

- Test-Restore → Test-DB löschen
- Produktiv-Restore → dokumentierte Eskalation

---

## Referenzen

- Break-Glass
- Bare Metal Recovery
- Compose Stack Restore
