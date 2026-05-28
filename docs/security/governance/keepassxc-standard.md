# KeePassXC Standard

## Zweck

Dieses Dokument definiert die verbindlichen Standards für Aufbau, Strukturierung und Governance der zentralen KeePassXC Credential-Datenbank der EMC Infrastruktur.

Ziel:

- zentrale Credential Source of Truth
- nachvollziehbare Verantwortlichkeiten
- recoveryfähige Credential-Verwaltung
- kontrollierte Credential Rotation
- Vermeidung von Shadow-Secrets
- langfristig wartbare Betriebsstruktur

Keine produktiven Secretwerte in diesem Dokument.

---

# Grundprinzipien

## KeePassXC als Source of Truth

KeePassXC bildet langfristig die zentrale Credential Source of Truth der EMC Infrastruktur.

Dokumentiert werden insbesondere:

- Zugangsdaten
- Recoveryinformationen
- Verantwortlichkeiten
- Runtime-Zuordnungen
- Rotationshistorie
- Recoverypfade

---

## Recovery First

Die KeePassXC-Struktur muss recoveryfähig bleiben.

Credential-Härtung darf keine Lockout-Situation erzeugen.

---

## Fachliche Domänentrennung

Credentials werden getrennt strukturiert für:

- shared
- mitglieder
- finanzen
- noten (später)

---

# Zielstruktur KeePassXC

## Hauptstruktur

```text
EMC Infrastructure
├── Shared
├── Mitglieder
├── Finanzen
├── Noten
├── Recovery
└── Archive
```

---

# Shared Infrastruktur

## Typische Einträge

### NAS

- SSH Zugang
- sudo Recovery
- lokale Recoveryinformationen

---

### Fritz!Box

- Admin Zugriff
- Recoveryinformationen
- Exportstatus

---

### WireGuard

- Konfigurationen
- Recoveryinformationen
- Clientübersicht

---

### GitHub

- Hauptaccount
- 2FA Informationen
- Recoverycodes
- Repository Governance

---

### Docker / Runtime

- Portainer
- Uptime Kuma
- Monitoring
- Runtime Recovery

---

### MariaDB

- Root Recovery
- persönliche Admin-Zugänge
- Backup Runtime

---

# Mitglieder Domäne

## Typische Einträge

### Backend

- PROD Runtime
- DEV Runtime
- DB User
- Runtime-Secrets

---

### Frontend

- PROD Runtime
- DEV Runtime
- API-Konfiguration

---

### Access

- PROD Runtime
- DEV Runtime
- ODBC Runtime
- dbconfig-Zuordnungen

---

### Datenbank

- RW User
- RO User
- Rollenmodell
- Reportingzugänge

---

# Finanzdomäne

## Typische Einträge

### Access

- PROD Runtime
- DEV Runtime
- ODBC Runtime
- dbconfig-Zuordnungen

---

### Datenbank

- RW User
- RO User
- Reportingzugänge

---

# Recovery Bereich

## Kritische Recovery-Einträge

### Netzwerk Recovery

- Fritz!Box
- WireGuard
- DynDNS
- MyFritz

---

### Infrastruktur Recovery

- NAS Zugriff
- SSH
- sudo
- MariaDB Root

---

### Governance Recovery

- GitHub
- 2FA
- Recoverycodes

---

# Naming Standards

## Zielprinzip

Einträge sollen:

- eindeutig
- fachlich zuordenbar
- recoveryverständlich

benannt sein.

---

## Beispiele

### Shared

```text
Shared / NAS / SSH Admin
Shared / MariaDB / Root
Shared / GitHub / Primary Account
```

---

### Mitglieder

```text
Mitglieder / Backend / PROD
Mitglieder / Access / PROD
Mitglieder / MariaDB / RW
```

---

### Finanzen

```text
Finanzen / Access / PROD
Finanzen / MariaDB / RW
```

---

# Rotationsdokumentation

## Ziel

Credential-Änderungen sollen nachvollziehbar bleiben.

---

## Pro Eintrag dokumentieren

- letzter Wechsel
- Verantwortlichkeit
- Recoveryrelevanz
- gekoppelte Systeme
- Rotationshinweise

---

# Nicht zulässige Nutzung

## Verboten

KeePassXC ersetzt nicht:

- Runtime-Secrets
- Compose-Dateien
- Backup-Strukturen
- Recovery-Dokumentation

---

# Backup Governance

## KeePassXC Recovery

Die KeePassXC-Datenbank ist selbst recoverykritisch.

Deshalb erforderlich:

- Backupstrategie
- Recoveryfähigkeit
- kontrollierter Zugriff
- dokumentierte Speicherorte

---

# Zielzustand

Langfristiger Zielzustand:

- zentrale Credential Source of Truth
- recoveryfähige Credentialverwaltung
- fachlich getrennte Struktur
- kontrollierte Rotation
- nachvollziehbare Verantwortlichkeiten
- dokumentierte Recoverypfade

Die Weiterentwicklung erfolgt evolutionär und recovery-sicher.
