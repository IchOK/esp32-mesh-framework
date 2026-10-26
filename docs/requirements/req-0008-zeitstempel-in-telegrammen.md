---
id: REQ-0008
title: "Zeitstempel in jedem Telegramm"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

Die Klärung von 2026-10-25 (Zeitbasis) und die Offline-Anzeige (REQ-0007)
brauchen durchgängig bewertbare Zeitbezüge.

# Requirement

Alle Telegramme zwischen Knoten, Gateway und Node-RED führen einen
**Zeitstempel** mit. Der abzusendende Bezugszeitpunkt ist pro Telegrammtyp im
Nachrichtenlexikon festzulegen (Sende- oder Empfangszeit, FSPEC-Feinschliff).
Ohne etablierte Zeitreferenz (REQ-0009) ist der Zeitstempel mit „nicht
referenziert“ zu kennzeichnen, damit keine Scheingenauigkeit entsteht.

# Rationale

Ohne Zeitstempel sind Reihenfolgenachlieferung (REQ-0004), Alteranzeige und
Puffer-Logik nicht beweisbar.

# Verification

FSPEC-Abnahme: Für jede Telegrammform ist der Zeitstempel im Format geprüft;
Beispielzüge ohne referenzierte Uhr führen das Flag.

# Human approval
Approver: pending  
Approved version/date: pending
