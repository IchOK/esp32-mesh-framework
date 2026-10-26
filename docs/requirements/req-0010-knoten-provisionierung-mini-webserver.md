---
id: REQ-0010
title: "Knoteneinrichtung über knoteneigenen Mini-Webserver im AP-Hilfsmodus"
version: 0.2.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

Neue oder falsch konfigurierte Knoten sollen ohne Werkzeug, ohne Gateway und
ohne Mesh-Betrieb einrichtbar sein, damit der Ausbau (project.md §4, „~10 Knoten
mit ausdrücklichem Ausbau“) ohne Router-Nähe oder Seriellen-Zwang erfolgt.

# Requirement

Jeder Knoten enthält einen **knoteneigenen Mini-Webserver** mit einer
Konfigurationsseite. Daran werden eingestellt:

- zu welchem **Mesh-Netz** der Knoten gehört (Netz-Kennung),
- der zugehörige **Schlüssel** (Mesh-Zugangsdaten, persistenter Speicher).

Der Knoten arbeitet als **Access Point** und stellt die Konfigurationsseite
bereit, wenn

1. noch **keine Mesh-Konfiguration** erfolgt ist, oder
2. der Knoten das eingetragene Mesh-Netz **länger als einen parametrisch
   definierten Zeitraum** (konkreter Wert im FSPEC, T-012) nicht sieht —
   unabhängig davon, ob er schon einmal verbunden war.

Im AP-Hilfsmodus gilt:

- Der Knoten unternimmt **in definierten Intervallen** (FSPEC) weiterhin den
  Versuch, sich mit dem konfigurierten Mesh-Netz zu verbinden; gelingen diese
  Versuche, verlässt er den AP-Hilfsmodus.
- Besteht eine **aktive Verbindung zum Webserver** (etwa laufende
  Konfigurationsanpassungen), wechselt der Knoten **nicht** in den
  Mesh-Betrieb, bis die Verbindung beendet ist.
- Nach gelungener Ersteinrichtung verbindet sich der Knoten mit dem Mesh.

# Rationale

Alternative Wege — seriell, Node-RED, Gateway-UI — setzen voraus, dass der
Knoten bereits erreichbar ist oder hantiert werden muss. AP-nahe Einrichtung
ist der einzige Weg, der ohne Vorkenntnis des Zielmeshs auskommt. Das
Bedrohungsmodell (project.md §6) erlaubt die AP-Reichweite als
Einrichtungs-Voraussetzung: Zugriff braucht physische Nähe im Heimgebiet.

# Verification

Drei Tests:

1. **Neuer Knoten:** Ohne Konfiguration erscheint innerhalb der definierten
   Zeit sein AP mit erreichbarer Konfigurationsseite; nach Eintrag von
   Netz-Kennung und Schlüssel joined der Knoten ins Mesh.
2. **Isolierter Knoten:** Ein korrekt konfigurierter, aber vom Mesh
   getrennter Knoten wechselt nach dem parametrischen Zeitraum in den
   AP-Hilfsmodus und bietet die Seite an.
3. **Erreichbarkeit im Betrieb:** Im Mesh-Betrieb ist die AP-Seite standardmäßig
   nicht offen.
4. **Intervall-Neustart:** Ist das Mesh während des AP-Hilfsmodus wieder
   sichtbar, verlässt der Knoten im nächsten Verbindungsintervall automatisch
   den AP-Modus.
5. **Webserver-Halt:** Ein Browser ist mit der Konfigurationsseite verbunden,
   während das Mesh wieder sichtbar wird — der Knoten bleibt bis zum Ende der
   Verbindung im AP-Hilfsmodus und joined erst danach.

# Human approval
Approver: pending  
Approved version/date: pending
