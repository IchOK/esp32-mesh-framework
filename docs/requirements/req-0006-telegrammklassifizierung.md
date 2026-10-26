---
id: REQ-0006
title: "Telegrammklassifizierung nach Typ im Nachrichtenlexikon"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

Die Klärung von 2026-10-25 (T-013) hat entschieden: statt einer Grundregel
nach Richtung wird jeder Telegrammtyp einzeln klassifiziert.

# Requirement

Jede im System definierte Telegrammform ist im Nachrichtenlexikon **Typ für
Typ** als synchron oder asynchron zu klassifizieren. Ein Telegramm, für das
keine Klassifikation gesetzt ist, darf nicht implementiert werden; eine
nachträglich geänderte Klassifikation ist als Änderung des Lexikons zu
dokumentieren (Trace auf REQ-0004/REQ-0005).

# Rationale

Ohne Typ-Kanon entstehen an jeder Schnittstelle Interpretationsspielräume.
Die Lexikon-Pflege macht Klassifikationsänderungen sichtbar, statt sie im
Code verschwinden zu lassen.

# Verification

FSPEC-Abnahme: Das Nachrichtenlexikon enthält für jede Telegrammform einen
sync/asyn-Eintrag; Stichprobe der Implementierungen bestätigt die Klassifikation.

# Human approval
Approver: pending  
Approved version/date: pending
