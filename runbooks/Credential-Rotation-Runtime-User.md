# Runbook – Credential Rotation (Runtime User)

## Zweck

Kontrollierte Rotation technischer Runtime-Credentials ohne unnötige Betriebsrisiken.

Dieses Runbook gilt für:

- Backend Runtime User
- Backup Runtime User
- vergleichbare technische Service-Accounts

Nicht direkt gedacht für:

- MariaDB root
- Fritz!Box
- WireGuard
- interaktive Benutzerkonten

---

# Voraussetzungen

Vor Rotation müssen erfüllt sein:

- Recovery funktionsfähig
- aktuelles Backup vorhanden
- KeePass gepflegt
- `.env` Runtime Standard aktiv
- Compose Source of Truth vorhanden
- Redeploy-Prozess validiert

---

# Passwortstandard

Empfohlener Standard für Runtime-Credentials:

- Länge: 28 Zeichen
- Groß-/Kleinbuchstaben
- Zahlen
- keine Sonderzeichen
- Speicherung ausschließlich in KeePass

Begründung:

Pragmatischer Betriebsstandard zur Vermeidung von:

- `.env` Problemen
- Shell-Quote-Problemen
- Docker-Compose Parsing-Problemen
- SQL-Escaping-Problemen

---

# Standardablauf

## 1. Neues Passwort erzeugen

In KeePassXC:

- neues Passwort generieren
- Eintrag aktualisieren
- KeePass speichern

Empfehlung:

```text
28 Zeichen
ohne Sonderzeichen
```

---

## 2. `.env` vorbereiten

Beispiel:

```bash
cd /volume1/docker/compose/emc-mitglieder-backend-dev
sudo nano .env
```

Wert aktualisieren:

```text
DB_PASSWORD=NEUES_PASSWORT
```

Noch kein Redeploy.

---

## 3. MariaDB User Passwort ändern

Interaktiv:

```bash
docker exec -it mariadb mariadb -uroot -p
```

Dann:

```sql
ALTER USER 'USERNAME'@'%' IDENTIFIED BY 'NEUES_PASSWORT';
FLUSH PRIVILEGES;
EXIT;
```

---

## 4. `.env` testen

```bash
sudo bash -c '
set -a
source ./.env
set +a
docker exec mariadb mariadb \
  -u USERNAME \
  -p"$DB_PASSWORD" \
  -e "SELECT DATABASE() AS db_connection_ok;"
'
```

Erwartung:

```text
db_connection_ok
NULL
```

Kein:

```text
ERROR 1045
```

---

## 5. Runtime Redeploy

Beispiel Backend DEV:

```bash
cd /volume1/docker/compose/emc-mitglieder-backend-dev
sudo docker compose up -d --force-recreate
```

---

## 6. Runtime prüfen

Logs prüfen:

```bash
docker logs --tail 120 CONTAINERNAME
```

Wichtige Prüfungen:

- keine DB Login Fehler
- Spring Boot gestartet
- Hikari Connection erfolgreich
- Container healthy

---

## 7. HTTP Funktionstest

Beispiel:

```bash
docker exec CONTAINERNAME \
  curl -i http://localhost:8080/api/auth/me
```

Erwartung:

```text
401 Unauthorized
```

ist ohne Login korrekt.

---

# KeePass Dokumentation

Im Notizfeld ergänzen:

- Rotationsdatum
- betroffener Service
- erfolgreicher Redeploy
- erfolgreicher Runtime-Test
- Besonderheiten / Learnings

---

# Recovery bei Fehlern

## Wenn Runtime nicht startet

Prüfen:

- `.env` Syntax
- Passwort identisch in MariaDB und `.env`
- Sonderzeichenprobleme
- Compose Config

---

## `.env` Syntax testen

```bash
sudo bash -c '
set -a
source ./.env
set +a
echo "ENV OK"
'
```

---

## Typischer Fehler

```text
unexpected EOF while looking for matching
```

Ursache:

problematische Sonderzeichen oder Quotes.

Empfohlene Lösung:

neues Passwort ohne Sonderzeichen.

---

# Pilotrotation

Erfolgreich praktisch getestet:

```text
emc_backend_dev_rw
2026-05-28
```

Validiert:

- MariaDB Passwortrotation
- `.env` Runtime
- Docker Compose Redeploy
- Spring Boot Runtime
- HTTP-Test
- KeePass Governance

---

# Betriebsregel

Nach Änderungen immer:

```text
STRG + S
→ git diff
→ git status
```

---

# Gesamtbewertung

Credential Rotation praktisch erfolgreich standardisiert.
