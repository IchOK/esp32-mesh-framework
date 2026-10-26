---
id: REQ-0011
title: "Zielgerichteter Knotenpuffer im Gateway für asynchrone Telegramme ab Node-RED"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

Asynchrone Telegramme von Node-RED an Knoten können auf nicht erreichbare
Knoten treffen. Ein gemeinsamer Zeitstrahl für alle Telegramme würde
verpuffen oder verworfen, solange nur ein einzelner Knoten fehlt. Gesucht ist
eine Pufferung, die **pro Zielknoten** entfällt, nicht global.

# Requirement

Kann ein **asynchrones** Telegramm von Node-RED an einen Knoten nicht
zugestellt werden (Knoten offline, erreichbarkeitsseitig getrennt — die
fehlende Empfangsquittung nach REQ-0012 ist der unmittelbare Beleg, auch vor
einer Offline-Erkennung), puffert
das Gateway es **nach Zielknoten getrennt**. Die Anforderungen im Einzelnen:

1. **Zielisolierung:** Die Warteschlange je Knoten ist unabhängig voneinander.
   Zustellbarkeit, Wiedergabe und Stau des einen Knoten steuern nicht die
   Zustellung an einem anderen.
2. **Zeitreihenfolge je Ziel:** Innerhalb einer Knoten-Warteschlange wird
   nach Entstehungszeitpunkt zugestellt.
3. **Wiederaufnahme:** Meldet sich Knoten B, werden nur seine Warteschlange
   geleert. Warteschlangen anderer Knoten (z. B. der dauerhaft fehlende
   Knoten A) bleiben ungekürzt stehen.
4. **Begrenzung je Knoten:** Die Warteschlangengröße ist **parametrisch**
   gefordert (FSPEC, T-012). Bei Überlauf werden die ältesten Telegramme der
   betroffenen Knoten-Warteschlange verworfen, mit sichtbarem
   Störungsstand wie in REQ-0004 (Ring-Semantik je Ziel, nicht global).

Synchron gekennzeigte Telegramme bleiben von dieser Anforderung ausgeschlossen
(REQ-0005).

# Rationale

Node-RED schickt Aufträge, die auf dem falschen Knoten nichts verpuffen
lassen, aber zu lange warten. Eine geteilte Schlange würde Knoten B bei
Blockade von A blockieren. Pro-Ziel-Warteschlange ist die kleine Variante von
MQ-Prinzip, ohne Broker, auf dem bestehenden Core2-Speicher tragbar.

# Verification

Integrationstest mit drei Konstellationen, Knoten A und B vorhanden:

1. A wird dauerhaft stromlos; Telegramme für A stapeln sich in deren
   Warteschlange, Telegramme für andere Knoten laufen ungestört.
2. B wird kurz stromlos, Telegramm für B, B wieder online: nur B-Warteschlange
   wird zugestellt.
3. A-Warteschlange bleibt nach B-Zustellung unverändert ungekürt, Display/UI
   zeigen A weiterhin als offline.

# Human approval
Approver: pending  
Approved version/date: pending
