# EMC Infra

Infrastruktur-Repository für die Docker-/NAS-Betriebsstandardisierung der EMC Mitgliederverwaltung.

## Zweck

Dieses Repository ist die Source of Truth für die versionierten Docker-Compose-Dateien und die zugehörige Betriebsdokumentation.

## Betriebsmodell

- GitHub Repository `emc-infra` = Source of Truth
- Branch `master` = stabiler Stand
- Windows-Arbeitsplatz = Authoring, Änderung, Commit, Push
- NAS `/volume1/docker/compose` = operative Deploy-Kopie
- Portainer = Deploymenthilfe, nicht Primärquelle

## Wichtige Grundsätze

- Keine produktiven UI-only Änderungen in Portainer
- Keine Secrets im Git
- Änderungen zuerst im Git, danach Übernahme auf das NAS
- Recovery First
- Keine Blindbereinigung
- Kontrollierte Migration statt Big Bang

## Infrastrukturstand

Umgesetzte Betriebsstandards:

- Governance / Source of Truth
- Backup / Restore / Break-Glass
- Filesystem-Standardisierung
- Credential Management
- Frontend Deployment Standardisierung
- Netzwerkstandardisierung
- Security Hardening
- Security-Verzeichnis Synchronisation

## Security-Verzeichnis Synchronisation

Seit Phase 8 wird die lokale Security-Struktur per Syncthing auf das NAS repliziert.

Führendes System:

```text
C:\Users\Joerg\Documents\Security
```

NAS-Replik:

```text
/volume1/home/JaitiNissi1968/Security
```

Betriebsstandard:

```text
Laptop = Source of Truth
NAS = Replik
Desktop = zusätzlicher Client (optional)
```

Wichtige Hinweise:

- KeePassXC bleibt Credential Source of Truth.
- Syncthing ersetzt kein Backup.
- Die NAS-Replik wird zusätzlich durch das bestehende Backup-Konzept geschützt.
- Syncthing-Versionierung (.stversions) ist aktiviert.

## Struktur

```text
compose/
  mariadb/
  mariadb-backup/
  phpmyadmin/
  uptime-kuma/
  portainer/
  syncthing/
  emc-mitglieder-backend-dev/
  emc-mitglieder-backend-prod/
  emc-mitglieder-frontend-dev/
  emc-mitglieder-frontend-prod/

docs/
  infra-governance.md
  stack-inventory.md
  docker-network-standard.md
  credential-inventory.md

runbooks/
  portainer-governance.md
  deployment-backend.md
  deployment-frontend.md
  restore-runbook.md
  disaster-recovery.md
```

## Dokumentationsgrundsatz

Dokumentation und produktiver Runtime-Stand sollen jederzeit möglichst deckungsgleich sein.

Abweichungen zwischen:

```text
EMC-INFRA
```

und

```text
NAS Runtime
```

sind zeitnah zu korrigieren.

Das Repository dient gleichzeitig als technische Dokumentation, Recovery-Grundlage und Betriebsreferenz.
