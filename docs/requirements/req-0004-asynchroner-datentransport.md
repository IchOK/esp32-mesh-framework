---
id: REQ-0004
title: "Asynchroner Datenpfad Knoten → Node-RED mit Pufferung"
version: 0.2.0
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
weitergeleitet. **Ist der Ringpuffer voll, werden die ältesten Telegramme
zugunsten neuerer verworfen (Ring-Semantik).** Der Verlust ist als Störung
sichtbar zu machen (Core2-Display sowie Hinweis an UI und Node-RED), damit
die Nachlücken-Lücke nicht verschleiert wird.

# Rationale

Kernvermittlungsinstanz des Gateways (project.md §1). Ringpuffer statt unendlicher
Ablage, weil Speicher auf dem Core2 begrenzt ist und überalterte Daten keinen
Sinn mehr machen. Verwerfen mit sichtbarem Störungsstand statt stiller Datenentzug,
damit die Diskrepanz "lückenlos angekommen" vs. "Lücke" nicht erst durch die
Anwendung selbst auffällt.

# Verification

Integrationstest mit Node-RED-Abschuss während laufender Knotenmeldungen:
Nach Reconnect erreichen alle Telegramme, die jünger als der Pufferhorizont
sind, Node-RED in Reihenfolge, ohne dass die Reihenfolge der verbliebenen
gestört wird. Wird der Puffer im Test gezielt überrollt, zeigen Display und
UI/Node-RED einen Störungsstand, und nur die überzählten ältesten Telegramme
fehlen.

# Human approval
Approver: pending  
Approved version/date: pending
