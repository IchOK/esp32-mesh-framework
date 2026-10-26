---
id: REQ-0014
title: "Statische UI-Auslieferung von SD und lokale Statusanzeige am Core2-Display"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: []
---

# Motivation

project.md §4/§5: Webserver liefert die statische Oberfläche von der SD-Card
und das Core2-Display zeigt Status/Störung lokal — die UI soll ohne
Node-RED-Beteiligung und ohne Firmware-Zwangsupdates warten lassen, und das
Fehlerbild ohne geöffnete Clients ablesbar sein.

# Requirement

1. Die Web-Benutzeroberfläche des Gateways wird **statisch von der SD-Card**
   ausgeliefert; UI-Inhalte werden ohne Firmware-Update gewartet.
2. Das **Core2-Display** zeigt mindestens: Erreichbarkeit des Gateway-Systems
   (Mesh-Laufzeit), Anzahl bekannter Knoten (online/offline), aktuelle
   Störungen (z. B. Node-RED nicht verbunden, Knoten offline, Pufferereignis).
3. Die Display-Anzeige funktioniert **lokal und unabhängig** davon, ob
   Webbrowser oder Node-RED verbunden sind.

# Rationale

SD-Auslieferung hält den Flash-Speicher frei und macht UI-Pflege zum
File-Deploy statt Firmware-Release. Die lokale Anzeige ist die einzige
Vor-Ort-Fernablesung, wenn die Leitstände gerade weg sind.

# Verification

1. UI-Dateien auf der SD gegen Version X_2 getauscht, Gateway ohne Neustart
   der Firmware → neue UI ohne Update-Verteilung.
2. Display-Test: Node-RED gekappt, ein Knoten offline → Störungszeile sichtbar,
   ohne dass Client UI oder Node-RED verbunden sind.

# Human approval
Approver: pending  
Approved version/date: pending
