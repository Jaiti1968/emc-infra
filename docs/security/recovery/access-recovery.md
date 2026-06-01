# EMC NAS Betriebsstandardisierung – Access Recovery Runbook

Status: Phase 9 abgeschlossen
Ablageziel: `emc-infra/docs/security/recovery/access-recovery.md`

Wichtig: Dieses Dokument enthält bewusst keine Klartextpasswörter.

---

# Zweck

Dieses Dokument beschreibt Recovery, Wiederherstellung und Betrieb der produktiv relevanten Access-Welt.

Access bleibt Bestandteil der Zielarchitektur:

```text
Finanzen
=
Access First

Mitglieder
=
Service First
Access Supporting
```

Verwendet für:

- Restfunktionen
- Fallback-Betrieb
- Reporting
- Fachliche Spezialfunktionen

---

# Architekturmodell

## Source of Truth

```text
NAS
└── \\NAS-DH2300-C64C\apps\access
```

---

## Arbeitskopien

```text
NAS Master
        ↓
Lokale Windows Arbeitskopie
        ↓
Access lokal
        ↓
MariaDB
```

Die lokale Arbeitskopie ist ausdrücklich nicht Source of Truth.

---

# Master-Pfade NAS

```text
\\NAS-DH2300-C64C\apps\access\emc_mitglieder\dev
\\NAS-DH2300-C64C\apps\access\emc_mitglieder\prod

\\NAS-DH2300-C64C\apps\access\emc_finanzen\dev
\\NAS-DH2300-C64C\apps\access\emc_finanzen\prod
```

---

# Lokale Arbeitskopien

## Desktop

```text
D:\Chor\Access\emc_mitglieder\dev
D:\Chor\Access\emc_mitglieder\prod

D:\Chor\Access\emc_finanzen\dev
D:\Chor\Access\emc_finanzen\prod
```

---

## Laptop

```text
C:\Users\Joerg\Chor\Access\emc_mitglieder\dev
C:\Users\Joerg\Chor\Access\emc_mitglieder\prod

C:\Users\Joerg\Chor\Access\emc_finanzen\dev
C:\Users\Joerg\Chor\Access\emc_finanzen\prod
```

---

# Technische Voraussetzungen

| Komponente          | Vorgabe                 |
| ------------------- | ----------------------- |
| Microsoft Access    | erforderlich            |
| MariaDB ODBC Driver | MariaDB ODBC 3.2 Driver |
| Verbindung          | DSN-less                |
| Netzwerkzugriff NAS | erforderlich            |
| MariaDB Zugriff     | Port 3306               |

---

# Datenbankarchitektur

## DSN-less Betrieb

Die Access-Anwendungen verwenden keine Windows-DSN-Konfiguration.

Verbindungsaufbau erfolgt ausschließlich über:

```text
dbconfig.ini
```

---

## Verbindungsaufbau

Verwendete Funktion:

```text
BuildMariaDbConnectString()
```

Die Verbindung wird dynamisch aus der lokalen:

```text
dbconfig.ini
```

erzeugt.

Beispielinhalt:

```text
APP_ENV=DEV

DRIVER={MariaDB ODBC 3.2 Driver}
SERVER=192.168.178.63
PORT=3306
DATABASE=...
UID=...
PWD=...
```

---

# Recovery-Vorteile

Durch DSN-less Betrieb:

```text
kein System-DSN
keine Registry-Abhängigkeit
keine ODBC-Konfiguration notwendig
```

Recovery benötigt lediglich:

```text
Access Datei
dbconfig.ini
MariaDB ODBC Driver
```

---

# Benutzerrotation

Architekturziel:

```text
Credential-Wechsel
ohne Neuverknüpfung
```

möglich.

Dies wurde in Phase 9 erfolgreich validiert.

---

# Automatischer Relink

## Mitglieder

Verwendet:

```text
BuildMariaDbConnectString()
CheckMariaDBConnection()
```

Erweiterung Phase 9:

```text
Automatischer Relink
vor Verbindungsprüfung
```

Vorteil:

```text
Benutzerwechsel
Passwortwechsel
Credential Rotation
```

ohne manuelle Tabellenpflege.

---

## Finanzen

Verwendet:

```text
BuildMariaDbConnectString()
RebuildFinanzenDevLinks_SavePwd()
```

Phase-9-Erkenntnis:

```text
dbAttachSavePWD
nicht erforderlich
```

ODBC-Verbindung wird vollständig aus der Konfigurationsdatei aufgebaut.

---

# Produktive Datenbankbenutzer

## Mitglieder DEV

```text
emc_access_mitglieder_dev_rw
```

---

## Mitglieder PROD RW

```text
emc_access_mitglieder_prod_rw
```

---

## Mitglieder PROD RO

```text
emc_access_mitglieder_prod_ro
```

---

## Finanzen DEV

```text
emc_access_finanzen_dev_rw
```

---

## Finanzen PROD

```text
emc_access_finanzen_prod_rw
```

---

# Recovery Access-Artefakte

Je Umgebung prüfen:

```text
.accdb Datei
dbconfig.ini
dbconfig_*.ini
Batch-Dateien
Hilfsdateien
```

Zusätzlich bei Finanzen:

```text
EMCFinanzen.xlsx
```

falls vorhanden.

---

# Recovery Master-Artefakte

1. Backup identifizieren
2. NAS-Zielpfad bestimmen
3. Dateien wiederherstellen
4. dbconfig-Dateien wiederherstellen
5. Dateigrößen prüfen
6. Änderungsdatum prüfen
7. Arbeitskopie neu erzeugen
8. Access starten
9. Verbindung testen
10. Fachtest durchführen

---

# Recovery Arbeitskopie

1. Lokalen Zielordner anlegen
2. Aktuelle NAS-Artefakte kopieren
3. Richtige dbconfig bereitstellen
4. Access starten
5. Verbindung prüfen
6. Fachtest durchführen

---

# Minimaltest

Nach Recovery mindestens prüfen:

```text
Access startet
Datenbankverbindung funktioniert
Startmaske funktioniert
Formulare funktionieren
Berichte erreichbar
```

---

# Bekannte Abhängigkeiten

| Abhängigkeit            | Bedeutung            |
| ----------------------- | -------------------- |
| MariaDB erreichbar      | Datenzugriff         |
| Port 3306 erreichbar    | Datenzugriff         |
| dbconfig.ini korrekt    | Verbindungsparameter |
| ODBC Driver installiert | Datenbanktreiber     |
| NAS Share verfügbar     | Master-Artefakte     |

---

# Validierte Plattformen

Erfolgreich getestet:

```text
Laptop
Desktop
```

für:

```text
Mitglieder DEV
Mitglieder PROD
Finanzen DEV
Finanzen PROD
```

---

# Nicht Bestandteil

- Ablösung von Access-Berichten
- Port-Reduktion MariaDB
- zukünftige Secret-Migration
- zukünftige Access-Ablösung

---

# Änderungslog

| Datum      | Änderung                                                                           |
| ---------- | ---------------------------------------------------------------------------------- |
| 2026-05-26 | Initiale Version Phase 2                                                           |
| 2026-06-01 | DSN-less Recovery-Konzept, Benutzerrotation und Access-Härtung aus Phase 9 ergänzt |
