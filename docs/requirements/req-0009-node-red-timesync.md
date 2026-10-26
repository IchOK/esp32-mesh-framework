---
id: REQ-0009
title: "Node-RED-getriebener Time-Sync über das Mesh"
version: 0.1.0
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

Node-RED sendet über das Gateway ein besonderes **Time-Sync-Telegramm** an
alle Knoten (Einzel- und Gruppenempfang, FSPEC). Der Knoten setzt seine Uhr
und führt sie danach selbst weiter. Fällt die Node-RED-Beziehung aus, arbeitet
der Knoten mit zuletzt gesetzter Zeit weiter; Telegramme aus diesem Bereich
sind als „nicht referenziert“ zu kennzeichnen.

# Rationale

NTP-Anfragen durch alle Knoten über die Mesh-Strecke (geprüfte Alternative)
verbrauchen Wiederholtraffic und laufen ohne Gateway leer. Ein zentraler
Zeitgeber pro Netzwerk entspricht dem Single-Gateway-Prinzip (project.md §6).
Die Uhrweiterführung selbst ist als T-025 (RTC/Stetigkeit) im FSPEC.

# Verification

Integrationstest: Zeitversatz zwischen Gateway, Knoten und Node-RED unter dem
im FSPEC definierten Toleranzwert. Nach Kappen der Node-RED-Beziehung zeigen
Telegramme der Knoten das Referenzflag, nach Wiederbindung nicht mehr.

# Human approval
Approver: pending  
Approved version/date: pending
