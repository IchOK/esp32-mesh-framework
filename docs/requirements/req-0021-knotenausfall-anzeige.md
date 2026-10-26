---
id: REQ-0021
title: "Knotenausfall: Offline-Markierung, Zeitstempel-Werte, Störung auf dem Display"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

project.md §5 (Betriebsfall „Knoten nicht erreichbar“, §165 ff.) und §192:
Ausfälle müssen dreifach sichtbar sein — UI, Node-RED und lokal am Display,
ohne dass letzte Werte verschwinden.

# Requirement

Erkennt das Gateway einen Knoten nach dem parametrischen Timeout-Fenster
(REQ-0007) als ausgefallen, gilt:

1. Der Knoten wird UI und Node-RED gemeinsam als **offline** gezeigt, die
   letzten Werte bleiben **mit Zeitstempel** erhalten (REQ-0008, REQ-0015).
2. Das Gateway-Display meldet die Störung „Knoten offline“ mit Knotenkennung.
3. Die Zustellerkennung bleibt bis zur Wiederkehr unverändert — mit dem
   nächsten Telegramm des Knotens gilt er wieder als online.

# Rationale

Ein Ausfall, der nur eine der drei Sicht-Ebenen erreicht, wird je nach
Blickwinkel unterschiedlich gehalten — mit allen drei Ebenen ist das Bild
konsistent, auch wenn der Betreiber gerade keine UI offen hat.

# Verification

Integrationstest: Ein Knoten bleibt stumm. Nach dem Timeout-Fenster:
Offline-Eintrag (UI/Node-RED, identisch), Zeitstempel konservativ,
Displayzeile aktiv; Telegramm zurück → alles wieder online.

# Human approval
Approver: pending  
Approved version/date: pending
