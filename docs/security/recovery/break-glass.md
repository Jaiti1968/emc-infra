# EMC NAS Betriebsstandardisierung – Break-Glass Dokument

Status: Phase 9 abgeschlossen / Phase 8b nachgeführt
Ablageziel: `emc-infra/docs/security/recovery/break-glass.md`

Wichtig: Dieses Dokument enthält bewusst keine Klartextpasswörter.

---

# Zweck

Dieses Dokument beschreibt den Notfallzugriff auf die EMC NAS-, Docker-, Datenbank-, Security- und Access-Infrastruktur.

Break-Glass bedeutet:

> Zugriff und Wiederherstellung müssen auch dann möglich sein, wenn einzelne Systeme, Arbeitsplätze oder Teile der Infrastruktur ausgefallen sind.

---

# Grundprinzipien

- Recovery First.
- Keine Secrets im Git.
- KeePass ist Credential Source of Truth.
- GitHub `emc-infra` ist Infrastruktur Source of Truth.
- Runtime-, Access-, Administrations- und Recovery-Zugänge sind getrennt.
- Root-Zugänge sind ausschließlich Break-Glass-Zugänge.
- Recovery-Dokumentation darf nicht ausschließlich auf dem NAS liegen.

---

# Kritische Systeme

| System              | Zweck                                   | Kritikalität |
| ------------------- | --------------------------------------- | -----------: |
| UGREEN DH2300 NAS   | Docker Runtime / Daten                  |     kritisch |
| MariaDB             | Mitglieder- und Finanzdaten             |     kritisch |
| Access Master Share | Access Anwendungen                      |     kritisch |
| Fritz!Box           | Netzwerk / WireGuard                    |     kritisch |
| GitHub              | Infrastruktur-Recovery                  |     kritisch |
| Portainer           | Runtime-Verwaltung                      |         hoch |
| Uptime Kuma         | Monitoring                              |       mittel |
| Syncthing           | Security-Replikation                    |         hoch |
| Windows Laptop      | Primärer Arbeitsplatz / Source of Truth |         hoch |
| Windows Desktop     | Recovery-Client                         |     relevant |

---

# Infrastruktur Source of Truth

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

## NAS-Replik

```text
/volume1/home/JaitiNissi1968/Security
```

---

## Desktop-Replik

```text
D:\Security
```

---

# Wichtige Pfade

## NAS

```text
/volume1/docker
/volume1/docker/compose
/volume1/docker/docs
/volume1/docker/runbooks
/volume1/docker/recovery
/volume1/docker/secrets
/volume1/docker/backups
/volume1/docker/build
/volume1/docker/monitoring
/volume1/docker/volumes
```

---

## MariaDB Backups

```text
/volume1/docker/backups/mariadb
```

---

## Externes Backup

```text
/mnt/@usb/sdc1
```

---

## Access Master Share

```text
\\NAS-DH2300-C64C\apps\access
```

---

## Lokale Arbeitskopien

```text
D:\Chor\Access\emc_mitglieder\dev
D:\Chor\Access\emc_mitglieder\prod

D:\Chor\Access\emc_finanzen\dev
D:\Chor\Access\emc_finanzen\prod
```

---

# Break-Glass Zugänge

## Recovery Administrator

### root@localhost

Zweck:

```text
letzter Recovery-Zugang
```

Verwendung:

```text
NAS Shell
Docker Konsole
MariaDB Recovery
```

Eigenschaften:

```text
vollständige Rechte
lokal nutzbar
```

Wichtige Änderung:

```text
root@% wurde entfernt
```

Root ist nicht mehr über Netzwerk nutzbar.

---

## Primärer DBA

### JoergTitz

Zweck:

```text
tägliche Administration
Datenbankbetrieb
Recovery-Unterstützung
```

Rolle:

```text
role_emc_admin
```

Eigenschaften:

```text
administrativer Zugriff
kein GRANT OPTION
```

Standardzugang für Datenbankadministration.

---

## phpMyAdmin Administration

### emc_phpmyadmin_admin

Zweck:

```text
GUI Administration
```

Verwendung:

```text
phpMyAdmin
Benutzerverwaltung
Rollenverwaltung
```

Grund:

```text
phpMyAdmin 5.2.3 Rollen-Inkompatibilität
```

Nicht für Anwendungen verwenden.

---

# Minimal benötigte Zugänge

| Zweck                | Zugang                  |
| -------------------- | ----------------------- |
| NAS Verwaltung       | NAS Admin               |
| Shell / Docker       | SSH User JaitiNissi1968 |
| Netzwerk             | Fritz!Box Admin         |
| DB Recovery          | root@localhost          |
| DB Administration    | JoergTitz               |
| phpMyAdmin GUI       | emc_phpmyadmin_admin    |
| Git Recovery         | GitHub Account          |
| Runtime Recovery     | Portainer Admin         |
| Monitoring           | Uptime Kuma Admin       |
| Access Recovery      | Windows Arbeitsplatz    |
| Security Recovery    | KeePass                 |
| Security-Replikation | Syncthing               |

---

# Recovery-Kurzablauf

## NAS lebt, Anwendung gestört

1. NAS oder SSH Zugriff herstellen
2. Containerstatus prüfen
3. Compose-Konfiguration prüfen
4. Runtime-Secrets prüfen
5. Datenbankstatus prüfen
6. Monitoring prüfen
7. Funktionstest durchführen

---

## NAS vollständig ausgefallen

1. Ersatzsystem bereitstellen
2. Netzwerkzugriff herstellen
3. KeePass öffnen
4. GitHub Zugriff herstellen
5. emc-infra bereitstellen
6. Standardstruktur erzeugen
7. Secrets wiederherstellen
8. Docker installieren
9. Stacks wiederherstellen

Reihenfolge:

```text
1 MariaDB
2 Uptime Kuma
3 Portainer
4 Syncthing
5 Backend DEV
6 Backend PROD
7 Frontend DEV
8 Frontend PROD
9 phpMyAdmin
```

10. Datenbank-Restore durchführen
11. Access testen
12. Monitoring testen
13. Backup testen

---

# Recovery der Security-Artefakte

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

Betriebsmodell:

```text
Laptop = Source of Truth
NAS = Replik + Dateiversionierung
Desktop = zusätzliche Recovery-Ebene
```

Recovery-Möglichkeiten:

```text
Laptop → NAS
Laptop → Desktop
NAS → Laptop
Desktop → Laptop
NAS → Desktop
Desktop → NAS
```

Grundsätze:

- Laptop bleibt führend.
- Syncthing ersetzt kein Backup.
- NAS dient als zusätzliche Recovery-Ebene.
- Desktop dient als zusätzliche Recovery-Ebene.
- `.stversions` unterstützt Dateiwiederherstellung.
- KeePass liegt redundant auf allen drei Systemen vor.

---

# Nicht Bestandteil

- MFA-Einführung
- Docker Secrets Migration
- Reverse Proxy
- Netzwerksegmentierung
- zukünftige Security-Hardening-Maßnahmen

---

# Änderungslog

| Datum      | Änderung                                                                                                               |
| ---------- | ---------------------------------------------------------------------------------------------------------------------- |
| 2026-05-26 | Initiale Version Phase 2                                                                                               |
| 2026-05-30 | Syncthing und Security-Replikation ergänzt                                                                             |
| 2026-06-01 | Rollenmodell, DBA-Modell, phpMyAdmin-Admin und root@localhost aktualisiert                                             |
| 2026-06-02 | Desktop-Recovery-Client ergänzt, Security Source of Truth nach D:\Security migriert, Recovery-Architektur aktualisiert |
|            |                                                                                                                        |
