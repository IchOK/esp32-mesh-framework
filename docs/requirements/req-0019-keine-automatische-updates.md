---
id: REQ-0019
title: "Update-Erkennbarkeit: kein Update ohne Anstoß von außen"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0004]
---

# Motivation

project.md §6 (Non-Goals, „keine automatische Update-/Prüflauflogik“) und §5:
Updates sollen nur auf Zuruf erfolgen, nicht durch Eigenregie der Knoten oder
des Gateways.

# Requirement

Keine Systemkomponente führt **unaufgeforderte** Firmware-Wechsels aus:

1. Knoten suchen **nicht selbstständig** nach neuen Images (kein
   Hintergrund-Check, kein zeitgesteuertes Ziehen).
2. Das Gateway verteilt abgelegte Images **nicht automatisch** — es stellt sie
   nur bereit, sobald ein Anstoß nach REQ-0018 vorliegt.
3. Die einzige Ausnahme ist der **Rollback** nach REQ-0017, der als
   Sicherungsschritt des aktiven Updates gilt.

# Rationale

Unaufgeforderte Update-Läufe sind im Wartungsmodus nicht vorhersehbar und
würden die Akzeptanzlinie „Update nur nach Bestätigung“ unterlaufen. Die
Abgrenzung macht die Non-Grenze der Non-Goal-Grenze prüfbar.

# Verification

Langlauftest: Netz mit frisch abgelegtem Image auf der SD, keine Anstöße —
Beobachtung über die definierte Zeitspanne, kein Knoten wechselt die Version
(Protokoll-/Versionsvergleich).

# Human approval
Approver: pending  
Approved version/date: pending
