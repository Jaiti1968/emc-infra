# Infra Governance

## Zweck

Dieses Dokument definiert die verbindlichen Governance-Regeln für die technische Betriebsinfrastruktur der EMC Mitgliederverwaltung.

Es gilt für:

- Docker Compose
- NAS-Betriebsartefakte
- Git-basierte Infrastrukturverwaltung
- Portainer-Nutzung
- operative Infrastrukturänderungen

---

## Source of Truth

Verbindlicher Standard:

```text
GitHub Repository emc-infra
```

ist die primäre Source of Truth.

Nicht Source of Truth:

- Portainer-interne Stack-Konfiguration
- spontane UI-Konfigurationen
- historische Compose-Dateien
- undokumentierte lokale Änderungen

---

## Betriebsmodell

Standard:

```text
Windows Arbeitsumgebung
    ↓
Git Änderung
    ↓
Commit
    ↓
Push GitHub
    ↓
Übernahme auf NAS
    ↓
Deployment
```

### Rollen

**GitHub**

Architektonisch verbindlicher Sollstand.

**Windows-Arbeitsplatz**

Authoring, Änderung, Branching, Commit, Push.

**NAS**

Operative Deploy-Kopie:

```text
/volume1/docker/compose
```

**Portainer**

Deploymenthilfe, nicht Primärquelle.

---

## Grundprinzipien

### Recovery First

Vor produktiven Änderungen gilt:

- Wiederherstellbarkeit sicherstellen
- Recovery-Pfade dokumentieren
- keine destruktiven Änderungen ohne Rückweg

---

### Keine Blindbereinigung

Nicht erlaubt:

- spontane Löschung unbekannter Container
- spontane Löschung unbekannter Volumes
- spontane Löschung unbekannter Docker-Netze
- spontane Löschung unbekannter Dateien

Altlasten werden kontrolliert bewertet.

---

### Kontrollierte Migration

Nicht erlaubt:

Big-Bang-Umbauten ohne Absicherung.

Standard:

- kleine nachvollziehbare Änderungen
- klarer Rollback
- dokumentierter Zustand

---

## Secrets Governance

Secrets dürfen nicht im Git versioniert werden.

Nicht erlaubt:

- Passwörter in Compose-Dateien
- Credentials in Markdown-Dokumentation
- Secrets in Commit-History

Aktuell erlaubter Zwischenstandard:

lokale `.env` Dateien außerhalb Git.

Beispiele:

```text
compose/mariadb/.env
compose/mariadb-backup/.env
compose/emc-mitglieder-backend-dev/.env
compose/emc-mitglieder-backend-prod/.env
```

Docker-Secrets-Migration ist spätere Phase.

---

## Branch Governance

Standardbranch:

```text
master
```

### Änderungsmodell

**Kleine Infrastrukturänderungen**

Direkt auf `master` möglich.

**Größere Änderungen**

Feature-Branch empfohlen.

Beispiele:

```text
feature/backup-standardization
feature/frontend-image-migration
feature/network-segmentation
feature/credential-hardening
```

---

## Change Governance

Jede Infrastrukturänderung soll enthalten:

- Zweck
- Risiko
- Rollback
- technische Änderung
- Dokumentationsanpassung

---

## Deployment Governance

Standard:

Git zuerst.

Nicht erlaubt:

Portainer-UI-Änderungen ohne Rückführung ins Git.

Zulässiger Ablauf:

```text
Compose ändern
→ Git Commit
→ Push
→ NAS aktualisieren
→ Deployment
```

---

## NAS Filesystem Standard

Produktive Betriebsartefakte liegen unter:

```text
/volume1/docker
```

Zielstruktur:

```text
/volume1/docker
├── compose
├── docs
├── runbooks
├── recovery
├── secrets
├── backups
└── build
```

Nicht Standard:

private Home-Verzeichnisse als dauerhafte Produktivbasis.

Historische Altstrukturen können temporär bestehen, bis kontrollierte Migration erfolgt.

---

## Dokumentationspflicht

Dokumentationsrelevant:

- neue Stacks
- Stack-Änderungen
- Portänderungen
- Netzwerkanpassungen
- Backup-Änderungen
- Recovery-Prozesse
- Credential-Standards
- Betriebsentscheidungen

---

## Ausnahmen

Temporäre Ausnahmen sind nur bewusst und dokumentiert zulässig.

Beispiele:

- Incident Recovery
- Produktionsstörung
- Break-Glass-Szenarien

Nachgelagerte Dokumentation ist Pflicht.

---

## Gültigkeit

Dieses Dokument definiert den verbindlichen Governance-Standard für die EMC NAS-/Docker-Betriebsumgebung ab Phase 1 der Betriebsstandardisierung.
