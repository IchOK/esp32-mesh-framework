---
id: REQ-0004
title: "Asynchroner Datenpfad Knoten → Node-RED mit Pufferung"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

project.md §5: Node-RED kann ausfallen, während das Mesh weiterläuft.
Knotendaten sollen nicht verloren gehen.

# Requirement

Node-seitig aufgebaute Datenmeldungen werden als **asynchron** zugestellt:
erreicht die WebSocket-Verbindung zu Node-RED nicht, puffert das Gateway die
Telegramme in einem **Ringpuffer** dessen Größe und Haltezeit
**parametrisch** gefordert werden (konkrete Werte nach PoC-Messung, T-012).
Nach Verbindungswiederherstellung liefert das Gateway die gepufferten
Telegramme **in Reihenfolge ihres Entstehungszeitpunkts** nach. Asynchrone
Telegramme werden nicht erneut an den Knoten zurückgegeben, sondern nur
weitergeleitet.

# Rationale

Kernvermittlungsinstanz des Gateways (project.md §1). Ringpuffer statt unendlicher
Ablage, weil Speicher auf dem Core2 begrenzt ist und überalterte Daten keinen
Sinn mehr machen.

# Verification

Integrationstest mit Node-RED-Abschuss während laufender Knotenmeldungen:
Nach Reconnect erreichen alle Telegramme Node-RED in Reihenfolge, die älter
als der Pufferhorizont sind, ohne dass die Reihenfolge der verbliebenen
gestört wird.

# Human approval
Approver: pending  
Approved version/date: pending
