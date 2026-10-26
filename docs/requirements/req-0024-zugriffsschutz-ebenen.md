---
id: REQ-0024
title: "Zugangs- und Schutzebene: Basic Auth nur Web, Telegramme ohne Zugriffsschutz"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: []
---

# Motivation

project.md §4/§5 und die Kanon-Entscheidung T-011 (2026-10-25): der
Vertrauensansatz des Heimnetzes — der Mensch steuert über die Web-UI,
Node-RED und Knoten laufen innerhalb einer vertrauenswürdigen LAN-Hülle.

# Requirement

1. Die **Web-UI** des Gateways ist ausschließlich nach **Basic Auth**
   erreichbar (Nutzername/Passwort, Ablage auf dem Gateway).
2. Die **Telegrammebene** (Mesh-Protokoll, WebSocket-Zuspielung an UI und
   Node-RED, Gateway-HTTP-Imageservice) führt **keinen Zugriffsschutz** —
   wer die Netzschiene erreicht, darf Telegramme senden und empfangen.
3. Als Integritätsschicht dienen **Prüfsummen** (Telegramme, Images);
   Image-Signierung, Secure Boot und TLS sind **bewusst nicht gesetzt** und
   keine Bedingung.
4. (Damit ist auch die UI-Req R-UI-3 abgedeckt — Basic Auth hat nur die
   Webseite.)

# Rationale

Ein Heimnetz ohne Cloud, nur eine Barriere: die Mensch-UI. Zusätzliche
Protokoll-Auth (TLS-Mutual, Token) würde der Bedrohungsannahme „LAN ist
vertrauenswürdig" hinterherlaufen und die Gerätekomplexität erhöhen.

# Verification

Test: Web-Zugriff ohne Auth → abgewiesen, mit korrekt → Zugang; Telegramm
von einem zweiten Host ohne jegliche Anmeldung → Annahme und Wirkung;
Image mit fehlerhafter Prüfsumme → Abweisung.

# Human approval
Approver: pending  
Approved version/date: pending
