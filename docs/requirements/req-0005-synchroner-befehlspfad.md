---
id: REQ-0005
title: "Synchroner Befehlspfad Node-RED → Knoten mit sofortiger Rückmeldung"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

project.md §5: Node-RED steuert Knoten mit direkter Rückmeldung (Ziel
„Node-RED steuert Knoten“).

# Requirement

Von Node-RED als synchron gekennzeichnete Telegramme leitet das Gateway
unverzüglich an den Zielknoten weiter und gibt dessen Rückmeldung (Erfolg,
Ergebnis oder Fehler) sofort an Node-RED zurück. Ist der Knoten nicht
erreichbar, antwortet das Gateway unverzüglich mit einer Fehler-Rückmeldung.
Synchron-Telegramme werden nicht gepuffert, nicht erneut versucht und nicht
nachgeliefert.

# Rationale

Steuerbefehle brauchen Wirkungsquittung, sonst ist Node-RED blind. Das
Gegenteil (Puffern) würde doppelte Wirkung riskieren, wenn der Knoten die
Verbindung nur verliert, statt auszufallen.

# Verification

M2-Integrationstest: Sync-Befehl an erreichbaren Knoten → Erfolg innerhalb
des Sync-Antworttimeouts (parametrisch, T-012). Sync-Befehl an abwesenden
Knoten → Fehlerantwort innerhalb derselben Zeit; danach kein Retry und keine
Nachlieferung.

# Human approval
Approver: pending  
Approved version/date: pending
