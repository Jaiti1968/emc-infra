# EMC NAS Betriebsstandardisierung – Access Recovery Runbook

Status: Phase 2 / Break-Glass Foundation  
Ablageziel: `emc-infra/docs/security/access-recovery.md`  
Wichtig: Dieses Dokument enthält bewusst keine Klartextpasswörter.

---

## Zweck

Dieses Dokument beschreibt den Recovery-Ansatz für die produktiv relevante Access-Welt im EMC Betrieb.

Access ist weiterhin aktiver Bestandteil der Zielarchitektur:

- Finanzen: Access-first
- Mitglieder: Service-first, aber Access-supporting für Restfunktionen, Reporting und Fallback

---

## Grundmodell

```text
NAS Master-Artefakte
→ lokale Windows Arbeitskopie
→ Access Start lokal
→ DSN-less MariaDB ODBC Verbindung
```

Der NAS-Bestand ist der Master.
Lokale Windows-Kopien sind Arbeitskopien und kein primäres Backupziel.

---

## Relevante NAS Master-Pfade

```text
\\NAS-DH2300-C64C\apps\access\emc_mitglieder\dev
\\NAS-DH2300-C64C\apps\access\emc_mitglieder\prod
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

### Wichtiger Architekturhinweis

Diese lokalen Windows-Pfade sind **nicht Source of Truth**.

Verbindliche Source of Truth für Access-Artefakte ist:

```text
\\NAS-DH2300-C64C\apps\access
```

Die lokalen Pfade sind ausschließlich geräteabhängige Arbeitskopien für:

- produktiven Betrieb
- lokale Access-Nutzung
- Recovery / Break-Glass Wiederherstellung auf einem beliebigen Windows-System

Konsequenz:

Recovery ist **nicht an ein bestimmtes Laufwerk oder Gerät gebunden**, solange verfügbar sind:

- Microsoft Access
- MariaDB ODBC 3.2 Driver
- Zugriff auf die NAS Source of Truth
- passende dbconfig-Dateien

---

## Technische Voraussetzungen Windows

| Komponente          | Status / Vorgabe                           |
| ------------------- | ------------------------------------------ |
| Microsoft Access    | erforderlich                               |
| MariaDB ODBC Driver | MariaDB ODBC 3.2 Driver                    |
| Verbindungstyp      | DSN-less                                   |
| Netzwerkzugriff NAS | erforderlich                               |
| Zugriff MariaDB     | `192.168.178.63:3306` aktuell erforderlich |

---

## DSN-less Hinweis

Die Access-Verbindung arbeitet DSN-less.

Vorteil:

- kein Windows-System-DSN muss rekonstruiert werden
- weniger Abhängigkeit vom lokalen Windows ODBC Manager
- Recovery erfolgt primär über Access-Datei + dbconfig

Wichtig:

- MariaDB ODBC 3.2 Driver muss installiert sein.
- dbconfig-Dateien müssen korrekt vorhanden sein.
- Credentials gehören nicht in Git.

---

## Recovery Access-Artefakte

Bei Restore müssen je Umgebung geprüft werden:

- Access-Datei (`.accdb`)
- passende `dbconfig_*.ini`
- lokale `dbconfig.ini`
- bei Finanzen zusätzlich Excel-Artefakte, insbesondere `EMCFinanzen.xlsx` sofern vorhanden

---

## Recovery Ablauf – Access Master vom Backup wiederherstellen

1. Backup-Quelle identifizieren.
2. Zielpfad unter `\\NAS-DH2300-C64C\apps\access` prüfen.
3. Betroffene Umgebung bestimmen:
   - Mitglieder DEV
   - Mitglieder PROD
   - Finanzen DEV
   - Finanzen PROD
4. Access-Datei wiederherstellen.
5. dbconfig-Dateien wiederherstellen.
6. Excel-Artefakte wiederherstellen, falls betroffen.
7. Dateigrößen und Änderungsdatum plausibilisieren.
8. Lokale Windows-Arbeitskopie neu erzeugen.
9. Access lokal starten.
10. Verbindung zur MariaDB prüfen.
11. Fachlichen Starttest durchführen.

---

## Recovery Ablauf – Lokale Windows Arbeitskopie neu erzeugen

1. Zielordner unter `D:\Chor\Access\...` prüfen oder neu anlegen.
2. Aktuelle Master-Artefakte vom NAS kopieren.
3. Passende `dbconfig_*.ini` als lokale `dbconfig.ini` bereitstellen.
4. Access-Datei lokal öffnen.
5. Verbindungstest durchführen.
6. Fachliche Maske / Startfunktion prüfen.

---

## Fachlicher Minimaltest

Nach Restore mindestens prüfen:

- Access-Datei öffnet ohne Fehler.
- MariaDB Verbindung funktioniert.
- Startmaske / Hauptfunktion öffnet.
- Finanzen: Excel-Artefakt vorhanden, falls benötigt.
- Mitglieder: relevante Restfunktionen / Berichte sind erreichbar.

---

## Bekannte Recovery-Abhängigkeiten

| Abhängigkeit            | Bedeutung                                     |
| ----------------------- | --------------------------------------------- |
| MariaDB erreichbar      | Access benötigt direkten DB-Zugriff           |
| Port 3306 erreichbar    | aktuell fachlich erforderlich                 |
| dbconfig korrekt        | Umgebung / DB / User werden darüber gesteuert |
| ODBC Driver installiert | MariaDB Verbindung notwendig                  |
| NAS Share verfügbar     | Master-Artefakte liegen auf NAS               |

---

## Nicht Bestandteil dieser Phase

- Entscheidung zur Ablösung von Access-Berichten
- Reduktion MariaDB Host-Port 3306
- Credential Rotation
- Umbau auf neues Secret-Modell

---

## Änderungslog

| Datum      | Änderung                                               |
| ---------- | ------------------------------------------------------ |
| 2026-05-26 | Initiales Access Recovery Runbook für Phase 2 erstellt |
