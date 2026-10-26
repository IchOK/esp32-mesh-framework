---
id: REQ-0020
title: "Node-RED-Ausfall: Gateway, Mesh und UI laufen unverändert weiter"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

project.md §5 (Betriebsfall „Node-RED nicht erreichbar“) und §3: Node-RED ist
die Leitstand-, aber nicht die Lebensader-Komponente. Ihr Ausfall darf weder
Mesh noch Gateway noch UI stilllegen.

# Requirement

Fällt die WebSocket-Verbindung zu Node-RED aus, gilt unbeschadet der
Einzelforderungen (REQ-0004, REQ-0005):

1. **Mesh und Gateway** arbeiten unverändert weiter — Datensammlung,
   Weiterleitung zwischen Knoten, Offline-Erkennung.
2. Die **Web-UI** bleibt vollständig nutzbar.
3. **Asynchrone** Knotendaten laufen im Gateway-Puffer (REQ-0004) und werden
   bei Wiederbindung in Reihenfolge nachgeliefert.
4. **Synchrone** Befehle von Node-RED sind ohne Leitung nicht stellbar — sie
   werden nicht gepuffert (REQ-0005, Kanon-Zusage 2026-10-26).

# Rationale

Die Architektur trennt Leitstand (Node-RED) von Datendrehscheibe (Gateway).
Wäre der Gateway-Ausfallmodus an Node-RED gekoppelt, würde ein einzelner
Ausfallpunkt das Heimnetz lahmziehen.

# Verification

Integrationstest: Node-RED-Host vom Netz. Mesh-Betrieb, UI und
Knoten-Offline-Erkennung bleiben beobachtet aktiv; bei Wiederbindung
Einspielung des Puffers nach Zeitstempel.

# Human approval
Approver: pending  
Approved version/date: pending
