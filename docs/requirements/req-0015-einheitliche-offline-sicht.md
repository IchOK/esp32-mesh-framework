---
id: REQ-0015
title: "Gleiche Sicht auf Offline-Knoten für Browser und Node-RED"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

project.md §5 und REQ-0007: Knoten werden über parametrische Timeouts als
offline geführt, letzte Werte bleiben sichtbar. Da Browser und Node-RED
dieselbe WebSocket-Quelle teilen (project.md §4), darf sich die Sicht auf
diesem Stand nicht in zwei Wahrheiten trennen.

# Requirement

1. Erkannt ausgefallene Knoten werden UI **und** Node-RED identisch als
   **offline** dargestellt, jeweils mit **Zeitstempel des letzten erreichten
   Telegramms** (REQ-0007/REQ-0008).
2. Die Zustandsänderung online→offline wird beiden über dieselbe
   WebSocket-Schiene in derselben Meldungsinformation zugestellt; beide haben
   damit denselben Knotenstand, solange ihre Leitungen stehen.

# Rationale

Divergente Sichten (Browser „offline“, Node-RED „veraltet aber gut“ — oder
umgekehrt) sind im Betrieb schlimmer als keine Anzeige: die Leitstandhaltung
würde auf zwei widersprüchlichen Bildern laufen.

# Verification

Integrationstest: Ein Knoten sendet nicht mehr. Zeitpunkt beider Clients
(Protokollaufzeichnung) zeigen dieselbe Offline-Markierung und denselben
Zeitstempel; die UI-Badges und Node-RED-Meldungen sind identisch.

# Human approval
Approver: pending  
Approved version/date: pending
