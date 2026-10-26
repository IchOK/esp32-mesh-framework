---
id: REQ-0023
title: "LAN-Begrenzung: keine Cloud- oder Internetanbindung"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: []
---

# Motivation

project.md §6 (Non-Goals, „Keine Cloud-/Internet-Anbindung“): Die Vernetzung
endet im eigenen LAN. Als REQ formuliert ist die Grenze messbar, statt nur
ein Vorsatz zu sein.

# Requirement

1. Keine Firmware-Komponente (Gateway, Knoten) baut **Verbindungen über das
   LAN hinaus** auf — kein DNS auf externe Namen, keine NTP-Pfade, keine
   Update- oder Telemetrie-Ziele jenseits des Netzes.
2. Bezugsmöglichkeiten beschränken sich auf: Mesh-Strecke, Gateway-HTTP/
   WebSocket im LAN, Node-RED im LAN.

# Rationale

Anforderung, kein Wunsch: Ein einziges „Telefon-zu-Hause"-Feature (etwa ein
eingeschleuster Update-Kanal) würde die Non-Goal-Grenze aufbrechen. Als
Verbot formuliert, hält es spätere Komfortideen auf den richtigen Weg über
project.md §6.

# Verification

Traffic-Prüfung im Integrationstest: Gateway- und Knoten-Firmware zeigen im
 Packet-Setup nur LAN-Ziele; Negativversuch externer Adressen schlägt.

# Human approval
Approver: pending  
Approved version/date: pending
