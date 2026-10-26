---
id: REQ-0007
title: "Heartbeat-basierte Offline-Erkennung mit Zeitstempel-Anzeige"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

project.md §5: Ohne sichtbare Meldung, ob ein Knoten noch dabei ist, sind
Ausfälle nur an veralteten Werten zu erkennen.

# Requirement

Für jeden registrierten Knoten führt das Gateway einen
Heartbeat-/Zeitstempel-Timeout. Bleibt die Meldung länger als der
**parametrisch** geforderte Schwellenwert (konkrete Werte im FSPEC, T-012)
aus, gilt der Knoten als **offline**. Die zuletzt empfangenen Werte bleiben
mit Zeitstempel (REQ-0008) für UI und Node-RED sichtbar.

# Rationale

Das Gateway ist der einzige Sammler; eine eigene Knoten-Fernprüfung (Ping)
wärte die Mesh-Heilung (REQ-0003) nur doppelhaft. Herzschlagsicht mit Timeout
ist die übliche, geringtrauschige Form.

# Verification

PoC-Test: Ein Knoten wird stromlos. Innerhalb des Schwellwerts+Toleranz
markiert UI und Node-RED ihn als offline, der letzte Wert bleibt sichtbar.
Strom wieder an → Knoten gilt ohne Neustart anderer Knoten wieder als online.

# Human approval
Approver: pending  
Approved version/date: pending
