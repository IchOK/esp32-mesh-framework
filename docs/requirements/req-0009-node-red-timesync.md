---
id: REQ-0009
title: "Node-RED-getriebener Time-Sync über das Mesh"
version: 0.2.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

Die Klärung vom 2026-10-25: Node-RED (Raspberry Pi / Mini-PC mit permanenter
Zeitquelle) ist die Uhrvertrauensinstanz, nicht das Gateway.

# Requirement

Node-RED sendet über das Gateway ein besonderes **Time-Sync-Telegramm per
Broadcast** an alle Knoten; die Knoten setzen ihre Uhr und führen sie danach
selbst weiter. Die **Flag-Setzung „nicht referenziert“** erfolgt nicht sofort
mit dem Ausfall der Node-RED-Beziehung, sondern erst, wenn innerhalb eines
**längeren parametrischen Zeitraums** (konkreter Wert im FSPEC, T-012) kein
Time-Sync mehr eingetroffen ist. Bis dahin arbeitet der Knoten mit zuletzt
gesetzter Zeit ohne Flag weiter. Die Uhrweiterführung selbst ist als T-025
(RTC/Stetigkeit) im FSPEC.

# Rationale

NTP-Anfragen durch alle Knoten über die Mesh-Strecke (geprüfte Alternative)
verbrauchen Wiederholtraffic und laufen ohne Gateway leer. Ein zentraler
Zeitgeber pro Netzwerk entspricht dem Single-Gateway-Prinzip (project.md §6).
Die Uhrweiterführung selbst ist als T-025 (RTC/Stetigkeit) im FSPEC.

# Verification

Integrationstest: Ein Time-Sync per Broadcast erreicht alle Knoten (Einzel-
und Gruppenzustellung ist nicht erforderlich); Zeitversatz zwischen Gateway,
Knoten und Node-RED unter dem im FSPEC definierten Toleranzwert. Nach Kappen
der Node-RED-Beziehung bleibt das Referenzflag innerhalb des parametrischen
Zeitraums leer und setzt erst danach; nach Wiederbindung und empfangenem
Broadcast ist es wieder leer.

# Human approval
Approver: pending  
Approved version/date: pending
