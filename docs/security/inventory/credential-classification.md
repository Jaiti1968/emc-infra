# Credential Classification

## Zweck

Dieses Dokument klassifiziert die bekannten Credentials, Secret-Orte und Recovery-relevanten Zugänge der EMC Infrastruktur.

Ziel ist nicht die Speicherung von Passwortwerten, sondern die Bewertung von:

- Kritikalität
- Nutzung
- Recovery-Relevanz
- fachlicher Domäne
- späterer Sanierungsreihenfolge

Keine produktiven Secrets in diesem Dokument.

---

## Klassifizierungsmodell

| Bewertung | Bedeutung                                                      |
| --------- | -------------------------------------------------------------- |
| kritisch  | Verlust oder Fehlkonfiguration gefährdet Betrieb oder Recovery |
| hoch      | produktiv oder operativ wichtig                                |
| mittel    | technisch relevant, aber ersetzbar                             |
| niedrig   | leicht reproduzierbar oder geringe Auswirkung                  |

---

## Statusmodell

| Status            | Bedeutung                                               |
| ----------------- | ------------------------------------------------------- |
| produktiv aktiv   | wird im produktiven Betrieb benötigt                    |
| technisch aktiv   | wird von Systemen, DEV, Monitoring oder Tests genutzt   |
| recovery-kritisch | wird für Wiederherstellung oder Notfallzugriff benötigt |
| Altlast-Kandidat  | vermutlich historisch, später prüfen                    |
| später rotieren   | Passwortwechsel in Folgephase                           |
| später entfernen  | erst nach Prüfung löschen                               |

---

## Shared / Infrastruktur

| Credential / Bereich      | Nutzung                                    | Bewertung | Status                              | Hinweis                       |
| ------------------------- | ------------------------------------------ | --------- | ----------------------------------- | ----------------------------- |
| NAS User `JaitiNissi1968` | SSH, sudo, Docker, Betrieb                 | kritisch  | produktiv aktiv / recovery-kritisch | zentraler operativer Admin    |
| Linux `root` via sudo     | Root-Rechte über `JaitiNissi1968`          | kritisch  | recovery-kritisch                   | kein direkter SSH-Root-Login  |
| Fritz!Box Admin           | Internet-Gateway, WireGuard, Remotezugriff | kritisch  | recovery-kritisch                   | Config-Backup fehlt           |
| WireGuard                 | Remotezugriff ins Heimnetz                 | kritisch  | produktiv aktiv / recovery-kritisch | Export fehlt                  |
| GitHub Account            | Source of Truth / Repositories             | kritisch  | recovery-kritisch                   | 2FA später vorbereiten        |
| Portainer Admin           | Docker Runtime-Verwaltung                  | hoch      | technisch aktiv                     | operative Deploymenthilfe     |
| Uptime Kuma Admin / DB    | Monitoring, Telegram Alerts                | hoch      | technisch aktiv                     | `kuma.db` recovery-relevant   |
| Telegram Bot Token        | Uptime Kuma Benachrichtigung               | hoch      | technisch aktiv                     | liegt vermutlich in `kuma.db` |

---

## MariaDB Administration

| Credential       | Nutzung                      | Bewertung | Status                      | Hinweis                      |
| ---------------- | ---------------------------- | --------- | --------------------------- | ---------------------------- |
| `root@localhost` | lokaler DB Break-Glass Admin | kritisch  | recovery-kritisch           | beibehalten, später rotieren |
| `root@%`         | netzwerkweiter DB Root       | kritisch  | produktiv/recovery-relevant | später härten                |
| `JoergTitz@%`    | persönlicher MariaDB Admin   | kritisch  | administrativ aktiv         | Rolle `role_emc_admin`       |
| `role_emc_admin` | Admin-Rolle                  | kritisch  | technisch aktiv             | besitzt `ALL PRIVILEGES`     |

---

## Mitglieder Domäne

| Credential               | Nutzung                              | Bewertung | Status          | Hinweis                                 |
| ------------------------ | ------------------------------------ | --------- | --------------- | --------------------------------------- |
| `emc_backend_prod`       | Backend PROD auf `emc_mitglieder`    | kritisch  | produktiv aktiv | RW ohne DDL                             |
| `emc_backend_dev_rw`     | Backend DEV auf `emc_mitglieder_dev` | hoch      | technisch aktiv | RW ohne DDL                             |
| `emc_backend_test_rw`    | TEST auf `emc_mitglieder_test`       | mittel    | technisch aktiv | aktuell `ALL PRIVILEGES`, später prüfen |
| `emc_mitglieder_rw`      | Access Mitglieder PROD               | kritisch  | produktiv aktiv | Rolle `role_emc_mitglieder_rw`          |
| `emc_mitglieder_dev_rw`  | Access Mitglieder DEV                | hoch      | technisch aktiv | direkt berechtigt                       |
| `emc_mitglieder_ro`      | Mitglieder ReadOnly                  | mittel    | aktiv/unklar    | später Nutzung prüfen                   |
| `role_emc_mitglieder_rw` | Mitglieder RW-Rolle                  | hoch      | technisch aktiv | SELECT/INSERT/UPDATE/DELETE             |
| `role_emc_mitglieder_ro` | Mitglieder RO-Rolle                  | mittel    | technisch aktiv | SELECT                                  |

---

## Finanzen Domäne

| Credential             | Nutzung              | Bewertung | Status          | Hinweis               |
| ---------------------- | -------------------- | --------- | --------------- | --------------------- |
| `emc_finanzen_rw`      | Access Finanzen PROD | kritisch  | produktiv aktiv | Access-first          |
| `emc_finanzen_dev_rw`  | Access Finanzen DEV  | hoch      | technisch aktiv | DEV Runtime           |
| `emc_finanzen_ro`      | Finanzen ReadOnly    | mittel    | aktiv/unklar    | später Nutzung prüfen |
| `role_emc_finanzen_rw` | Finanzen RW-Rolle    | hoch      | technisch aktiv | später prüfen         |
| `role_emc_finanzen_ro` | Finanzen RO-Rolle    | mittel    | technisch aktiv | später prüfen         |

---

## Runtime Secret Orte

| Ort                            | Nutzung                                | Bewertung | Status          | Hinweis                    |
| ------------------------------ | -------------------------------------- | --------- | --------------- | -------------------------- |
| Container Env `mariadb`        | `MYSQL_ROOT_PASSWORD`                  | kritisch  | aktiv           | aktuell Runtime-Secret-Ort |
| Container Env `mariadb-backup` | `MYSQL_USER`, `MYSQL_PASS`             | hoch      | aktiv           | Backup-Credential          |
| Container Env Backend PROD     | `DB_URL`, `DB_USERNAME`, `DB_PASSWORD` | kritisch  | aktiv           | Mitglieder PROD            |
| Container Env Backend DEV      | `DB_URL`, `DB_USERNAME`, `DB_PASSWORD` | hoch      | aktiv           | Mitglieder DEV             |
| Access `dbconfig.ini`          | ODBC DB-Zugriff                        | kritisch  | produktiv aktiv | Mitglieder/Finanzen        |
| Uptime Kuma `kuma.db`          | Monitoring + Telegram                  | hoch      | aktiv           | recovery-relevant          |

---

## Altlast-Kandidaten

| Objekt                              | Nutzung                             | Bewertung | Status             | Hinweis                      |
| ----------------------------------- | ----------------------------------- | --------- | ------------------ | ---------------------------- |
| `user@%`                            | `testdb`                            | mittel    | Altlast-Kandidat   | Bootstrap-/Compose-Altlast   |
| `testdb`                            | historische Testdatenbank           | niedrig   | Altlast-Kandidat   | später entfernen             |
| `emc_backend_app`                   | vermutlich alter Backend-PROD-User  | mittel    | Altlast-Kandidat   | vor Löschung prüfen          |
| `emc_backend_dev`                   | vermutlich alter Backend-DEV-User   | mittel    | Altlast-Kandidat   | vor Löschung prüfen          |
| alte Home-Compose-Dateien           | historische Runtime-Struktur        | mittel    | Altlast-Kandidat   | enthält teils Secrets        |
| Klartext-Secrets in Compose-Dateien | historische/operative Übergangslage | hoch      | Sanierungskandidat | später aus Compose entfernen |

---

## Verbotene Paralleländerungen

Folgende Credentials dürfen nicht gleichzeitig geändert werden:

| Gruppe           | Nicht parallel ändern                                 |
| ---------------- | ----------------------------------------------------- |
| Remote Recovery  | Fritz!Box, WireGuard, NAS SSH                         |
| DB Recovery      | MariaDB root, `JoergTitz`, Backend PROD               |
| Produktivbetrieb | Backend PROD, Access PROD                             |
| Governance       | GitHub Passwort, 2FA, Recoverycodes                   |
| Runtime          | Compose-Struktur, Runtime-Secrets, Produktiv-Redeploy |

---

## Sanierungsreihenfolge

Empfohlene Reihenfolge für spätere Phasen:

1. KeePassXC-Struktur aufbauen
2. Fritz!Box- und WireGuard-Recovery sichern
3. GitHub-Recovery vorbereiten
4. Runtime-Secrets aus Compose-Dateien lösen
5. DEV-/TEST-Credentials rotieren
6. Monitoring-/Tool-Credentials rotieren
7. PROD-Credentials kontrolliert rotieren
8. MariaDB-Admin-Credentials rotieren
9. Altlasten entfernen
10. MariaDB-Usermodell final konsolidieren

---

## Abschlussbewertung

Die Credential-Landschaft ist funktional und bereits teilweise gut strukturiert.

Wesentliche Risiken liegen nicht in akuten Betriebsfehlern, sondern in:

- zentralisierten Recovery-Abhängigkeiten
- Klartext-Secrets in Compose-/Runtime-Dateien
- fehlenden Exporten für Fritz!Box/WireGuard
- fehlender finaler Credential Source of Truth
- historisch gewachsenen Altusern

Die weitere Sanierung erfolgt kontrolliert, recovery-sicher und domänengetrennt.
