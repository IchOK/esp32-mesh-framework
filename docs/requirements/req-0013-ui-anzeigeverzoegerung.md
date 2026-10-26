---
id: REQ-0013
title: "Anzeigeverzögerung der Web-UI unter 5 Sekunden"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: []
---

# Motivation

project.md §2 (Ziel „Knoteninformationen per Web“): Daten eines Knoten müssen im Browser schnell sichtbar sein, damit die UI als Leitstand brauchbar ist.

# Requirement

Die Anzeigeverzögerung für **online gemeldete Knoten** — Zeit von der
Datenannahme im Gateway bis zur sichtbaren Wertaktualisierung in der Web-UI —
liegt unter **5 Sekunden** (project.md §2, Zielgröße). Der Grenzwert ist
**parametrisch** geführt (konkrete Nachjustierung nur gegen dieselbe
Messmethode, T-012); die Messmethode (Messstrecke, Start-/Endpunkt) ist im
FSPEC zu schärfen.

# Rationale

5 s ist als Projektziel gesetzt, nicht als Technologieversprechen des
Mesh-Pfads. Als messbare Größe formuliert, bleibt die Anforderung überprüfbar
und die Sharfing-Frage (UI-seitig vs. Gesamtstrecke) sauber dem FSPEC
zugeordnet.

# Verification

Integrationstest: Zeitdifferenz zwischen Sendezeitstempel des Knotens und
DOM-Aktualisierung der UI über eine definierte Versuchsserie an einem
online gemeldeten Knoten; Median und Ausreißer werden protokolliert.

# Human approval
Approver: pending  
Approved version/date: pending
