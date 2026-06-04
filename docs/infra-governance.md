# EMC Infrastruktur Governance

Status: gültig
Stand: 2026-06-03

---

# 1. Zweck

Dieses Dokument definiert die technischen Governance-Regeln für die Betriebsumgebung der EMC Mitgliederverwaltung.

Ziele:

- nachvollziehbarer Betrieb
- reproduzierbare Deployments
- kontrollierte Änderungen
- dokumentierte Recovery-Prozesse
- minimierte Betriebsrisiken
- klare Verantwortlichkeiten
- standardisierte Monitoring- und Security-Strukturen

Dieses Dokument bildet die übergeordnete Governance für alle produktiven und Entwicklungsumgebungen der EMC Mitgliederverwaltung.

---

# 2. Source of Truth

## 2.1 Grundsatz

Das Git-Repository

```text
emc-infra
```

ist die technische Source of Truth für die Infrastruktur.

Dort werden gepflegt:

- Docker Compose Dateien
- Betriebsdokumentation
- Recovery-Dokumentation
- Security-Dokumentation
- Inventare
- Governance-Dokumente

---

## 2.2 Änderungen

Infrastrukturänderungen werden grundsätzlich zuerst im Repository dokumentiert.

Ausnahmen:

- Break-Glass Situationen
- akute Störungsbehebung

Diese Änderungen sind zeitnah nachzudokumentieren.

---

## 2.3 NAS

Das NAS ist Laufzeitumgebung.

Das NAS ist nicht die primäre Dokumentationsquelle.

---

# 3. Docker Governance

## 3.1 Compose Standard

Alle produktiven Services werden über Docker Compose betrieben.

Einzelne manuelle Docker-Container sind nicht zulässig.

---

## 3.2 Container-Namen

Containernamen sind explizit festzulegen.

Beispiele:

```text
emc-mitglieder-backend-prod
emc-mitglieder-backend-dev
emc-mitglieder-frontend-prod
emc-mitglieder-frontend-dev
mariadb
phpmyadmin
portainer
uptime-kuma
syncthing
```

---

## 3.3 Netzwerke

Produktive Container werden ausschließlich über definierte Docker-Netzwerke verbunden.

Standardnetz:

```text
emc_net
```

---

## 3.4 Volumes

Persistente Daten dürfen niemals ausschließlich im Container liegen.

Alle produktiven Daten werden über persistente Volumes oder Bind Mounts gespeichert.

---

# 4. Deployment Governance

## 4.1 Frontend

Frontend-Deployments erfolgen über:

```text
GitHub Actions
Build
Artefaktbereitstellung
Deployment auf NAS
```

---

## 4.2 Backend

Backend-Deployments erfolgen über:

```text
GitHub Actions
Build
Docker Image
Deployment auf NAS
```

---

## 4.3 Rollback

Rollback muss jederzeit möglich sein.

Vor produktiven Änderungen sind vorhandene Artefakte aufzubewahren.

---

# 5. Backup Governance

## 5.1 Datenbank

MariaDB wird regelmäßig gesichert.

Sicherungen müssen:

- erfolgreich erstellt werden
- lesbar sein
- aktuell sein

---

## 5.2 Recovery

Backup ohne getestete Wiederherstellung gilt nicht als verifiziert.

Restore-Tests sind Bestandteil der Betriebsdokumentation.

---

## 5.3 USB-Offsite-Sicherung

Die zusätzliche USB-Sicherung ist Bestandteil der Recovery-Strategie.

---

# 6. Security Governance

## 6.1 Passwortmanagement

Alle produktiven Zugangsdaten werden in KeePass verwaltet.

Klartextpasswörter dürfen nicht dokumentiert werden.

---

## 6.2 Passwortstandard

Passwörter:

```text
mindestens 26 Zeichen
empfohlen 28 Zeichen
```

---

## 6.3 Rollenmodell

MariaDB-Zugriffe erfolgen über dedizierte Benutzerrollen.

Grundsätze:

- Least Privilege
- Trennung von Runtime und Administration
- Trennung von DEV und PROD
- Trennung von Access und Backend

---

## 6.4 Root

MariaDB Root ist Break-Glass-Zugang.

Root wird nicht für den Regelbetrieb verwendet.

---

# 7. Monitoring Governance

## 7.1 Ziel

Monitoring dient nicht primär der Containerüberwachung.

Monitoring dient der frühzeitigen Erkennung von:

- Betriebsstörungen
- Datenverlust-Risiken
- Recovery-Risiken
- Synchronisationsproblemen
- Infrastrukturfehlern

---

## 7.2 Monitoring-Prinzip

Fachliche Prüfungen sind technischen Prüfungen vorzuziehen.

Beispiel:

Nicht nur:

```text
Port 3306 erreichbar
```

sondern:

```text
MariaDB Login erfolgreich
```

---

## 7.3 Monitoring-Struktur

Die Uptime-Kuma-Monitore werden nach Betriebsrelevanz gruppiert.

### Anwendungen

```text
APP Backend DEV
APP Backend PROD
APP Frontend DEV
APP Frontend PROD
```

### Daten & Recovery

```text
DATA DB Backup Healthcheck
DATA MariaDB Login
DATA Syncthing NAS
```

### Infrastruktur

```text
INFRA MariaDB
INFRA mariadb-backup
INFRA phpMyAdmin
INFRA Portainer
```

---

## 7.4 Recovery-Relevante Monitore

Besondere Bedeutung besitzen:

```text
DATA DB Backup Healthcheck
DATA MariaDB Login
DATA Syncthing NAS
```

Diese Monitore überwachen unmittelbar:

- Datenverfügbarkeit
- Backupfähigkeit
- Wiederherstellbarkeit
- Replikation kritischer Sicherheitsartefakte

---

## 7.5 Neue Dienste

Jeder neue produktive Dienst muss vor Inbetriebnahme bewertet werden hinsichtlich:

- Monitoring
- Backup
- Recovery
- Security
- Dokumentation

---

# 8. Dokumentations Governance

## 8.1 Pflichtdokumentation

Folgende Bereiche sind aktuell zu halten:

- Stack Inventory
- Credential Inventory
- Runtime Secret Mapping
- Recovery Dokumentation
- Governance Dokumentation

---

## 8.2 Nachvollziehbarkeit

Technische Entscheidungen sind nachvollziehbar zu dokumentieren.

---

## 8.3 Ergebnisdokumente

Größere Infrastrukturmaßnahmen werden durch eigene Ergebnisdokumente dokumentiert.

Diese dienen als Projekt- und Entscheidungsnachweis.

---

# 9. Änderungsmanagement

## 9.1 Grundsatz

Änderungen erfolgen kontrolliert.

Vor Änderungen sind zu prüfen:

- Auswirkungen
- Abhängigkeiten
- Recovery-Möglichkeiten

---

## 9.2 Produktionssysteme

Änderungen an PROD erfolgen bewusst und nachvollziehbar.

---

## 9.3 Nachdokumentation

Jede dauerhafte Infrastrukturänderung wird in EMC-INFRA dokumentiert.

---

# 10. Gültigkeit

Dieses Dokument gilt für die gesamte Infrastruktur der EMC Mitgliederverwaltung auf dem UGREEN NAS einschließlich:

- DEV
- PROD
- MariaDB
- Frontend
- Backend
- Backup
- Monitoring
- Recovery
- Syncthing
- Security-Struktur
- zukünftige Erweiterungen
