# Recovery Dependencies

## Zweck

Dieses Dokument beschreibt die Recovery-Abhängigkeiten, Recovery-Ketten und kritischen Kopplungen der EMC Infrastruktur.

Ziel:

- Recovery-Reihenfolgen nachvollziehbar dokumentieren
- Lockout-Risiken minimieren
- gekoppelte Credentials identifizieren
- sichere Credential-Rotation ermöglichen
- Break-Glass-Recovery absichern

Dieses Dokument enthält bewusst keine Secrets.

---

# Grundprinzip

Recoveryfähigkeit hat höhere Priorität als maximale Security-Härte.

Credential-Änderungen erfolgen ausschließlich kontrolliert und mit Recoverybewertung.

---

# Source of Truth

## Infrastruktur

```text
GitHub
└── emc-infra
```

---

## Credentials

```text
KeePass
```

---

## Security-Artefakte

```text
D:\Security
```

Laptop bleibt führendes System.

---

## Security-Replikation

```text
Laptop (Source of Truth)
        ⇅
     Syncthing
    ↙       ↘
 NAS       Desktop
```

---

# Zentrale Recovery-Kette

## Hauptkette

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

Diese Kette bildet den zentralen Remote-Recoverypfad.

---

# Shared Infrastruktur

## Fritz!Box

Kritikalität:

```text
kritisch
```

Funktion:

```text
Internet
WireGuard
Netzwerk
```

Nicht gleichzeitig ändern:

```text
Fritz!Box Passwort
WireGuard
DynDNS
```

---

## WireGuard

Kritikalität:

```text
kritisch
```

Funktion:

```text
Remotezugriff
```

Nicht gleichzeitig ändern:

```text
WireGuard
NAS SSH
Fritz!Box Zugriff
```

---

## NAS / SSH / sudo

Hauptzugang:

```text
JaitiNissi1968
```

Root Login:

```text
SSH Root deaktiviert
```

Nicht gleichzeitig ändern:

```text
SSH Zugang
WireGuard
Fritz!Box
```

---

## GitHub

Funktion:

```text
Infrastruktur Source of Truth
```

Nicht gleichzeitig ändern:

```text
Passwort
MFA
Recoverycodes
```

---

## Security-Artefakte / Syncthing

Primärquelle:

```text
D:\Security
```

NAS-Replik:

```text
/volume1/home/JaitiNissi1968/Security
```

Desktop-Replik:

```text
D:\Security
```

Architektur:

```text
Laptop (Source of Truth)
        ⇅
     Syncthing
    ↙       ↘
 NAS       Desktop
```

Grundsatz:

```text
Laptop = Source of Truth
NAS = Replik + Dateiversionierung
Desktop = zusätzliche Recovery-Ebene
```

GUI-Betrieb:

```text
GUI-Authentifizierung deaktiviert
127.0.0.1:8384 auf Windows
NAS GUI im Heimnetz erreichbar
```

Nicht gleichzeitig ändern:

```text
KeePass Struktur
Syncthing Konfiguration
Recovery-Dokumentation
```

---

# Docker Runtime

## Docker / Compose

Recovery-relevante Komponenten:

```text
Compose-Dateien
Volumes
Netzwerke
Runtime-Secrets
```

---

## Portainer

Funktion:

```text
Runtime-Verwaltung
```

Bewertung:

```text
operativ wichtig
nicht primärer Recoverypfad
```

---

## Uptime Kuma

Recovery-kritisch:

```text
kuma.db
```

Verlust bedeutet:

```text
Monitoringverlust
Alertingverlust
```

---

# MariaDB Recovery

## Break-Glass

### root@localhost

Funktion:

```text
letzter Recovery-Zugang
```

Eigenschaften:

```text
lokal
vollständige Rechte
```

Wichtige Änderung:

```text
root@% entfernt
```

---

## Primärer DBA

### JoergTitz

Funktion:

```text
tägliche Administration
```

Rolle:

```text
role_emc_admin
```

Nicht gleichzeitig ändern:

```text
JoergTitz
root@localhost
KeePass
```

---

## phpMyAdmin Administration

### emc_phpmyadmin_admin

Funktion:

```text
GUI Administration
```

Grund:

```text
phpMyAdmin Rollen-Inkompatibilität
```

---

# Mitglieder-Domäne

## Backend DEV

Benutzer:

```text
emc_mitglieder_dev_rw
```

Abhängigkeit:

```text
Compose
→ Runtime Secret
→ MariaDB
```

---

## Backend PROD

Benutzer:

```text
emc_mitglieder_prod_rw
```

Abhängigkeit:

```text
Compose
→ Runtime Secret
→ MariaDB
```

---

## Access Mitglieder DEV

Benutzer:

```text
emc_access_mitglieder_dev_rw
```

Abhängigkeit:

```text
dbconfig.ini
→ ODBC
→ MariaDB
```

---

## Access Mitglieder PROD

Benutzer:

```text
emc_access_mitglieder_prod_rw
```

Abhängigkeit:

```text
dbconfig.ini
→ ODBC
→ MariaDB
```

---

## Access Mitglieder PROD RO

Benutzer:

```text
emc_access_mitglieder_prod_ro
```

Verwendung:

```text
Read Only Auswertungen
```

---

# Finanzdomäne

## Access Finanzen DEV

Benutzer:

```text
emc_access_finanzen_dev_rw
```

Abhängigkeit:

```text
dbconfig.ini
→ ODBC
→ MariaDB
```

---

## Access Finanzen PROD

Benutzer:

```text
emc_access_finanzen_prod_rw
```

Abhängigkeit:

```text
dbconfig.ini
→ ODBC
→ MariaDB
```

---

# Recovery-Reihenfolge

## 1 Netzwerk

```text
Fritz!Box
Internet
WireGuard
```

---

## 2 Security

```text
KeePass
Security-Verzeichnis
Syncthing
Recovery-Dokumentation
```

---

## 3 NAS

```text
SSH
sudo
Storage
Docker
```

---

## 4 Datenbank

```text
MariaDB
Backup
Restore
```

---

## 5 Anwendungen

```text
Backend
Frontend
Access
Monitoring
```

---

## 6 Komfortsysteme

```text
Portainer
Uptime Kuma
```

---

# Single Points of Failure

| Bereich             | SPOF           |
| ------------------- | -------------- |
| NAS Administration  | JaitiNissi1968 |
| DB Recovery         | root@localhost |
| Netzwerk Recovery   | Fritz!Box      |
| Remote Recovery     | WireGuard      |
| Monitoring Recovery | kuma.db        |
| Governance          | GitHub         |
| Credential Recovery | KeePass        |

---

# Betriebsregeln

## Keine Blindrotation

Credentials niemals ohne Recoverybewertung ändern.

---

## Keine parallele Recovery-Änderung

Besonders kritisch:

```text
Fritz!Box
WireGuard
NAS SSH
root@localhost
JoergTitz
KeePass
```

---

## Recovery vor Cleanup

Altstrukturen erst entfernen wenn:

```text
Recovery dokumentiert
Runtime validiert
Rollback möglich
```

ist.

---

# Gesamtbewertung

Die EMC Infrastruktur besitzt:

- dokumentierte Recoverypfade
- Break-Glass-Modell
- Rollenmodell
- Credential Source of Truth
- Security-Replikation
- getestete Restorefähigkeit
- standardisierte Betriebsarchitektur
- redundante Security-Artefakte auf Laptop, NAS und Desktop
- Dateiversionierung über Syncthing `.stversions`

Die weitere Härtung erfolgt kontrolliert und recovery-sicher.

---

# Änderungslog

| Datum      | Änderung                                                                                                                  |
| ---------- | ------------------------------------------------------------------------------------------------------------------------- |
| 2026-05-26 | Initiale Version                                                                                                          |
| 2026-05-30 | Syncthing und Security-Artefakte ergänzt                                                                                  |
| 2026-06-01 | Rollenmodell, DBA-Modell und Phase-9-Recoveryarchitektur ergänzt                                                          |
| 2026-06-02 | Desktop-Recovery-Client ergänzt, Security Source of Truth nach D:\Security migriert, Recovery-Abhängigkeiten aktualisiert |
