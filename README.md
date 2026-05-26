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

## Struktur

```text
compose/
  mariadb/
  mariadb-backup/
  phpmyadmin/
  uptime-kuma/
  portainer/
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
