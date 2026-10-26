---
id: REQ-0001
title: "Multi-Hop-Erreichbarkeit der Gateway-Knotenverbindung"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

Knoten liegen außerhalb der unmittelbaren Gateway-Reichweite (project.md §2,
Ziel „Reichweitenverlängerung“). Die Kernanforderung aus ADR-0001: Daten müssen
über benachbarte Knoten weitergeleitet werden.

# Requirement

Das Gateway muss mit jedem Knoten des Meshnetzes über IP-Erreichbarkeit verbunden
sein, auch wenn der Knoten nicht direkt mit dem Gateway funken kann. Die
Weiterleitung erfolgt über höchstens `MESH_MAX_HOPS` (parametrisch, FSPEC)
Zwischenknoten; Datenverkehr zwischen Knoten und Gateway muss dabei ohne
korrupte Duplikate ankommen.

# Rationale

ESP-Mesh-Lite (ADR-0001) liefert Multi-Hop als IP-Schicht bis zum Knoten, ohne
eigenen Routing-Code. Ein REQ auf IP-Ebene hält die Gateway-Firmware
durchreichbar für alle höheren Protokolle (WebSocket, HTTP, OTA).

# Verification

Testaufbau mit Gateway + ≥ 3 Knoten, davon einer nur über zwei Hops erreichbar:
Ein Wert des äußeren Knoten erscheint ohne manuellen Eingriff auf der
Node-RED-Instanz. Latenz und Hop-Tiefe werden protokolliert.

# Human approval
Approver: pending  
Approved version/date: pending
