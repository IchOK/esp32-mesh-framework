---
id: REQ-0012
title: "Empfangsquittung für asynchrone Telegramme in Richtung Knoten"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

Die Offline-Erkennung (REQ-0007) ist eine Herzschlag-Sicht mit
parametrischem Zeitfenster. Zwischen „noch nicht als offline erkannt“ und
„faktisch nicht mehr verfügbar“ fehlt dem Gateway der Zustellnachweis — ohne
ihn puffert oder verwirft es Telegramme mangels Wissen.

# Requirement

Der Empfang jedes **asynchronen** Telegramms in Richtung Knoten (Gateway →
Knoten, einschließlich über das Gateway geleiteter Node-RED-Aufträge) ist
durch eine **Empfangsquittung** des Knotens zu bestätigen.

1. **Quittung ≠ Antwort:** Die Empfangsquittung bestätigt nur den Zugang.
   Inhaltliche oder Wirkungsrückmeldung bleibt Sache der Synchron-Klasse
   (REQ-0005).
2. **Entscheidung ohne Offline-Status:** Bleibt die Quittung innerhalb eines
   **parametrischen** Zeitfensters (konkreter Wert im FSPEC, T-012) aus, gilt
   das Telegramm als **nicht zugestellt** — ohne dass der Knoten zuvor als
   offline gelten muss.
3. **Kopplung an die Pufferung:** Ein so als nicht zugestellt erkanntes
   Telegramm wird nach REQ-0011 **pro Zielknoten** gepuffert; die
   Zustellentscheidung und die Offline-Meldung (REQ-0007) bleiben getrennte
   Ereignisse.

# Rationale

Ein TCP-Transport allein quittiert Verbindungsabbau, nicht die Zustelllage
eines Netzknotens, der nach Verbindungsaufbau verstummt. Die Quittungsschicht
liefert den unmittelbaren Beleg für die Zustellentscheidung, bevor die
Heartbeat-basierte Offline-Logik überhaupt greift.

# Verification

Integrationstest:

1. Erreichbarer Knoten quittet ein asynchrones Telegramm innerhalb des
   Zeitfensters; Node-RED/UI sehen eine zustellbestätigte Zustellung.
2. Knoten wird kurz nach dem Verbindungsaufbau stromlos: Quittung bleibt aus,
   Telegramm steht ungekürzt in seiner Warteschlange nach REQ-0011, der Knoten
   wird nicht früher als im REQ-0007-Fenster als offline markiert.
3. Quittung und synchrones Ergebnis (REQ-0005) werden im Protokoll unterschieden.

# Human approval
Approver: pending  
Approved version/date: pending
