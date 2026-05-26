# Portainer Governance

## Zweck

Dieses Dokument definiert die verbindlichen Nutzungsregeln für Portainer in der EMC Betriebsumgebung.

Portainer ist operative Deploymenthilfe.

Portainer ist nicht die primäre Source of Truth.

---

## Grundsatz

Verbindlicher Standard:

```text
GitHub emc-infra
    ↓
NAS /volume1/docker/compose
    ↓
Portainer Deployment
```

Nicht zulässig:

```text
Portainer UI Änderung
→ produktiv laufen lassen
→ keine Git-Rückführung
```

---

## Erlaubte Nutzung

### Monitoring / Sichtprüfung

Erlaubt:

- Containerstatus prüfen
- Healthchecks prüfen
- Logs ansehen
- Ressourcenverbrauch ansehen
- Netzwerke ansehen
- Volumes ansehen
- Container-Details ansehen

---

### Operative Runtime-Aktionen

Erlaubt:

- Container Restart
- Stack Restart
- Container Stop/Start bei Incident Recovery
- Loginspektion
- Health-Verifikation nach Deployment

Voraussetzung:

keine strukturelle Konfigurationsänderung.

---

### Deployment

Erlaubt:

Deployment bestehender versionierter Compose-Konfiguration.

Voraussetzung:

Compose stammt aus dokumentierter Source of Truth.

---

## Verbotene Nutzung

### UI-only Konfigurationsänderungen

Nicht erlaubt:

- Compose im Portainer UI editieren
- Environment Variablen im UI ändern
- Ports im UI ändern
- Volumes im UI ändern
- Netzwerke im UI ändern
- Container-Parameter im UI ändern
- Container manuell neu definieren

---

### Shadow Configuration

Nicht erlaubt:

Portainer-Konfigurationen, die nicht im Git existieren.

---

### Ad-hoc Infrastrukturaufbau

Nicht erlaubt:

- spontane neue produktive Stacks
- spontane Test-Stapels im Produktivsystem
- unbekannte Images produktiv starten

Ausnahme:

bewusste dokumentierte Ausnahme.

---

## Incident / Break-Glass

Temporär erlaubt:

wenn Produktionsstörung sofortige Reaktion verlangt:

- Restart
- Stop/Start
- temporäre Recovery-Maßnahmen

Pflicht danach:

- Dokumentation
- Rückführung in Git
- Bereinigung temporärer Sonderzustände

---

## Portainer Rolle

Portainer dient für:

- Sichtbarkeit
- Runtime-Steuerung
- kontrolliertes Deployment

Portainer dient nicht für:

- Architekturentscheidungen
- dauerhafte Konfigurationspflege
- Konfigurationsquelle

---

## Gültigkeit

Verbindlicher Standard ab Phase 1.
