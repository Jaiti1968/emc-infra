# break-glass-quick-recovery.md

# Break-Glass Quick Recovery

Status: Phase 2 Skeleton

## Zweck

Schneller Incident-Zugriff.

---

## Prioritäten

1. Ruhe bewahren
2. Incident klassifizieren
3. Zugriff sichern
4. Recovery-Weg wählen

---

## Schnellcheck

### Zugriff

Vorhanden?

- NAS
- SSH
- Router
- WireGuard
- GitHub
- DB Admin

---

### Recovery Artefakte

Vorhanden?

- Credential Inventory
- Break-Glass Dokument
- Backup
- Runbooks

---

## Incident Typen

### Anwendung kaputt

→ Compose Restore

---

### DB Problem

→ DB Restore

---

### Access Problem

→ Access Restore

---

### NAS Totalausfall

→ Bare Metal Recovery

---

## Eskalationsregel

Keine ungeprüften Schnellschüsse auf Produktivsystem.
