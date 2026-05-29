# EMC NAS Betriebsstandardisierung – Credential Inventory

Status: Phase 2 / Break-Glass Foundation  
Ablageziel: `emc-infra/docs/security/credential-inventory.md`  
Wichtig: Dieses Dokument enthält bewusst keine Klartextpasswörter.

---

## Zweck

Dieses Dokument beschreibt, welche Zugänge für Betrieb, Recovery und Break-Glass relevant sind und wo die eigentlichen Zugangsdaten verwaltet werden.

Ziel ist schnelle Orientierung im Notfall, ohne Secrets im Git oder in Markdown-Dateien abzulegen.

---

## Grundsätze

- Keine Klartextpasswörter in Git.
- Keine Klartextpasswörter in diesem Dokument.
- Passwörter liegen aktuell physisch auf vorhandenen Zugangszetteln.
- Zielzustand: KeePassXC als strukturierte Primärquelle.
- Root- / Admin-Zugänge sind Break-Glass-Zugänge und nicht für Normalbetrieb gedacht.
- Bei späterer Credential-Rotation ist dieses Inventory zu aktualisieren.

---

## Credential-Quellen

| Quelle                        | Status                 | Bemerkung                         |
| ----------------------------- | ---------------------- | --------------------------------- |
| Physische Zugangszettel       | aktuell vorhanden      | Übergangsquelle                   |
| KeePassXC                     | noch nicht vorhanden   | Zielzustand Phase 2 / Folgearbeit |
| Git                           | keine Secrets          | nur Referenzdokumentation         |
| NAS `/volume1/docker/secrets` | vorbereitet            | keine Git-Versionierung           |
| `.env` Dateien                | vorhanden / aufzubauen | außerhalb Git                     |

---

## Kritische Zugänge

| System / Dienst   | Zugang / Rolle            | Kritikalität | Aktuelle Credential-Quelle           | Ziel-Credential-Quelle    | Bemerkung                            |
| ----------------- | ------------------------- | -----------: | ------------------------------------ | ------------------------- | ------------------------------------ |
| NAS UGREEN DH2300 | NAS Admin                 |     kritisch | physischer Zettel                    | KeePassXC                 | benötigt für Web-UI / Administration |
| NAS SSH           | SSH User `JaitiNissi1968` |     kritisch | physischer Zettel                    | KeePassXC                 | sudo-fähiger Betriebszugang          |
| Fritz!Box         | Router Admin              |     kritisch | physischer Zettel                    | KeePassXC                 | Netzwerk / WireGuard / LAN Recovery  |
| WireGuard         | VPN Zugang                |     kritisch | physischer Zettel / Fritz!Box        | KeePassXC + Export        | Remote Adminzugriff                  |
| MariaDB           | `root`                    |     kritisch | physischer Zettel                    | KeePassXC                 | Break-Glass, nicht Normalbetrieb     |
| MariaDB           | fachliche DB-User         |         hoch | physischer Zettel / dbconfig         | KeePassXC                 | Access / Backend relevant            |
| GitHub            | GitHub Account            |     kritisch | physischer Zettel                    | KeePassXC                 | Recovery für Repositories            |
| Portainer         | Admin                     |  mittel-hoch | physischer Zettel                    | KeePassXC                 | operative Runtime-Verwaltung         |
| Uptime Kuma       | Admin                     |       mittel | physischer Zettel                    | KeePassXC                 | Monitoring / Alerting                |
| Windows PC        | Windows Admin             |     relevant | physischer Zettel / Windows Konto    | KeePassXC                 | Access / ODBC / lokale Recovery      |
| Access DB Configs | DSN-less DB Zugang        |     kritisch | dbconfig-Dateien / physischer Zettel | KeePassXC + Secret Backup | keine Klartextablage im Git          |

---

## MFA / 2FA Status

| System      | MFA aktiv?                  | Recovery Codes vorhanden? | Bemerkung                        |
| ----------- | --------------------------- | ------------------------- | -------------------------------- |
| GitHub      | nein / derzeit nur Passwort | nein                      | später Security-Hardening prüfen |
| NAS         | nein / nicht bekannt        | nein                      | aktuell kein MFA-Recovery-Thema  |
| Fritz!Box   | nein / nicht bekannt        | nein                      | aktuell kein MFA-Recovery-Thema  |
| Portainer   | nein / nicht bekannt        | nein                      | aktuell kein MFA-Recovery-Thema  |
| Uptime Kuma | nein / nicht bekannt        | nein                      | aktuell kein MFA-Recovery-Thema  |

---

## Zielzustand KeePassXC

Empfohlene Gruppenstruktur:

```text
EMC
├── Infrastruktur
│   ├── NAS
│   ├── SSH
│   ├── FritzBox
│   └── WireGuard
├── Docker
│   ├── Portainer
│   └── Uptime Kuma
├── Datenbanken
│   ├── MariaDB Root
│   ├── Mitglieder
│   └── Finanzen
├── Git
│   └── GitHub
├── Access
│   ├── Mitglieder DEV
│   ├── Mitglieder PROD
│   ├── Finanzen DEV
│   └── Finanzen PROD
└── Recovery
    ├── Break-Glass
    └── Recovery Codes
```

---

## Offene Punkte

- KeePassXC installieren und Vault anlegen.
- Physische Zugangszettel strukturiert in KeePassXC überführen.
- KeePassXC Backup-Strategie festlegen.
- GitHub MFA später separat im Security-Hardening prüfen.
- Credential Rotation ist bewusst nicht Bestandteil von Phase 2.

---

## Änderungslog

| Datum      | Änderung                                                                                                                                                                                                                            |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2026-05-26 | Initiales Credential Inventory für Phase 2 erstellt                                                                                                                                                                                 |
| 2026-05-29 | MariaDB Root Passwort gemäß Runtime Secret Standard rotiert und validiert. Bootstrap-Altlasten `testdb` und `user@%` entfernt. Root bleibt Break-Glass / Recovery-Zugang. Backup nutzt weiterhin dedizierten Benutzer `emc_backup`. |
