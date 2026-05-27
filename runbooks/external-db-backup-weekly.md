# Weekly External DB Backup

Status: Phase 2  
Zweck: Wöchentliche manuelle Sicherung der aktuellen MariaDB-Backups auf das externe USB-Backup-Medium.

---

## Ziel

Dieses Runbook beschreibt den wöchentlichen manuellen External-Backup-Lauf für die EMC MariaDB-Datenbanken.

Gesichert werden die aktuellen Backup-Artefakte für:

- `emc_mitglieder`
- `emc_mitglieder_dev`
- `emc_finanzen`
- `emc_finanzen_dev`

Die Sicherung erfolgt auf das externe USB-Medium unter:

```text
/mnt/@usb/sdc1/emc-backup/mariadb
```

---

## Erinnerung / Turnus

Standard:

- wöchentlich manuell
- empfohlen: Sonntag

Zusätzlich überwacht Uptime Kuma, ob das externe Backup älter als 8 Tage ist.

---

## Ablauf

### 1. USB-Medium verbinden

USB-Festplatte am NAS anschließen.

Prüfen:

```bash
df -h
```

Erwarteter Mountpoint:

```text
/mnt/@usb/sdc1
```

---

### 2. External DB Backup ausführen

```bash
sudo /volume1/docker/monitoring/scripts/external-db-backup.sh
```

Erwartung:

Für alle vier Datenbanken erscheint:

```text
[OK] ... external backup copied and verified
```

---

### 3. Backup Healthcheck ausführen

```bash
/volume1/docker/monitoring/scripts/backup-healthcheck.sh
```

Erwartung:

- lokale DB-Backups sind vorhanden
- lokale DB-Backups sind gzip-lesbar
- lokale DB-Backups sind frisch
- externe DB-Backups sind vorhanden
- externe DB-Backups sind gzip-lesbar
- externe DB-Backups sind frisch
- Uptime Kuma Push erfolgreich

---

### 4. Schreibcache flushen

```bash
sync
```

---

### 5. USB-Medium sauber auswerfen

USB-Medium über die NAS-Weboberfläche auswerfen:

```text
Externe Geräte → USB-Medium → Auswerfen
```

Erst danach die USB-Festplatte abziehen.

---

## Erfolgskriterien

Der Lauf gilt als erfolgreich, wenn:

- `external-db-backup.sh` für alle vier DBs `[OK]` meldet
- `backup-healthcheck.sh` keine `[FAIL]` Meldung erzeugt
- Uptime Kuma grün bleibt
- USB-Medium sauber ausgeworfen wurde

---

## Fehlerfall

Bei Fehlern:

1. Ausgabe des Scripts prüfen
2. USB-Mount prüfen:

```bash
df -h
```

3. Zielverzeichnis prüfen:

```bash
ls -lah /mnt/@usb/sdc1/emc-backup/mariadb
```

4. Healthcheck erneut ausführen:

```bash
/volume1/docker/monitoring/scripts/backup-healthcheck.sh
```

Keine produktiven Datenbanken überschreiben.

---

## Hinweise

Die `latest.*.sql.gz` Dateien im lokalen Backup-Verzeichnis sind Symlinks.

Für das externe Backup werden deshalb bewusst die echten timestamped Backup-Dateien kopiert, nicht die Symlinks.

Beispiel:

```text
202605270300.emc_mitglieder.sql.gz
```

---
