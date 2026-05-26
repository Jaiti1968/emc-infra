# access-restore.md

# Access Restore Runbook

Status: Phase 2 Skeleton

## Zweck

Wiederherstellung der Access-Artefakte und betriebsfähige lokale Windows-Nutzung.

---

## Architekturgrundsatz

Source of Truth:

```text
\\NAS-DH2300-C64C\apps\access
```

Lokale Windows-Pfade sind ausschließlich Arbeitskopien.

---

## Voraussetzungen

Benötigt:

- Windows System
- Microsoft Access
- MariaDB ODBC 3.2 Driver
- Zugriff auf NAS
- passende dbconfig-Dateien

---

## Relevante NAS Pfade

Mitglieder:

```text
\\NAS-DH2300-C64C\apps\access\emc_mitglieder\dev
\\NAS-DH2300-C64C\apps\access\emc_mitglieder\prod
```

Finanzen:

```text
\\NAS-DH2300-C64C\apps\access\emc_finanzen\dev
\\NAS-DH2300-C64C\apps\access\emc_finanzen\prod
```

---

## Relevante lokale Windows-Zielpfade

Lokale Windows-Arbeitskopiepfade sind geräteabhängig.

Beispiele aus dem aktuellen Betrieb:

### Desktop-PC

```text
D:\Chor\Access\emc_mitglieder\dev
D:\Chor\Access\emc_mitglieder\prod
D:\Chor\Access\emc_finanzen\dev
D:\Chor\Access\emc_finanzen\prod
```

### Laptop

```text
C:\Users\Joerg\Chor\Access\emc_mitglieder\dev
C:\Users\Joerg\Chor\Access\emc_mitglieder\prod
C:\Users\Joerg\Chor\Access\emc_finanzen\dev
C:\Users\Joerg\Chor\Access\emc_finanzen\prod
```

---

## ODBC Modell

Architektur:

DSN-less

Verwendeter Treiber:

```text
MariaDB ODBC 3.2 Driver
```

---

## Restore Ablauf

### 1. NAS Artefakte prüfen

- Access Dateien
- dbconfig
- Excel Dateien

---

### 2. lokale Arbeitskopie erzeugen

Passendes Gerät.

---

### 3. dbconfig bereitstellen

Umgebungsabhängig.

---

### 4. Access Start

Starttest.

---

### 5. Verbindung validieren

Pflicht:

- DB Verbindung
- Grundfunktion
- fachlicher Starttest

---

## Rollback

Lokale Arbeitskopie löschen / neu erzeugen.
