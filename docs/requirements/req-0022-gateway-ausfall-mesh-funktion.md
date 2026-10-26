---
id: REQ-0022
title: "Gateway-Ausfall: Mesh bleibt betriebsfähig, Wurzelheilung ohne Gateway"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

project.md §2 (Ziel „Mesh ohne Gateway betriebsfähig“), §3 (Anwendungsfall 5)
und REQ-0002/REQ-0003: Der Gateway-Ausfall ist der Worst Case des Pfads —
das Netz soll trotzdem weitersammeln und sich ohne Gateway sortieren.

# Requirement

Fällt der Gateway aus, gilt:

1. **Knoten↔Knoten-Datentausch** (REQ-0002) bleibt in Reichweite verfügbar —
   Mesh-Richtungs- und Applikationsschicht hängen nicht am Gateway.
2. Betrifft der Ausfall eine **Root-/Elternrolle**, heilen betroffene
   Kindknoten ohne manuellen Eingriff auf verbleibende Nachbarn (REQ-0003).
3. **Node-RED** verliert nur die Gateway-Brücke — die Gateway-seitige
   Pufferung entfällt naturgemäß; die Knoten führen ihre zuletzt bekannte
   Zeit weiter, bis das Referenzflag nach dem Time-Sync-Ausbleibefenster
   greift (REQ-0009).

# Rationale

Genau dieses Verhalten ist der Unterschied zwischen einem Mesh mit
Single-Point-of-Failure und einem, dessen Kernziel (§2) auch im Störungsfall
trägt. ADR-0002 (Redundanz) will diese Lücke später verkleinern; dieses REQ
fordert die Grundfunktion unabhängig davon.

# Verification

PoC: Gateway stromlos, zwei bis drei Knoten tauschen weiter Daten per
Gateway-freiem Pfad; Root = Gateway → verbleibende Knoten finden eine neue
Wurzel innerhalb des parametrischen Repair-Fensters.

# Human approval
Approver: pending  
Approved version/date: pending
