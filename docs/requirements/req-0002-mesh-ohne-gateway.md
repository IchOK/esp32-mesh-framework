---
id: REQ-0002
title: "Betriebsfähigkeit des Mesh ohne Gateway"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

project.md §2, Ziel „Mesh ohne Gateway betriebsfähig“: Der Datenaustausch der
Knoten darf nicht vom Gateway als Pflichtteil abhängen.

# Requirement

Ob das Gateway dem Meshverbund entzogen wird (ausgeschaltet, getauscht oder
nur getrennt) – alle verbliebenen Knoten müssen weiterhin untereinander Daten
austauschen können. Der Datenaustausch zwischen Knoten darf zu keinem Zeitpunkt
vom Vorhandensein oder Erreichen des Gateways abhängen.

# Rationale

ADR-0001 hat die Gateway-Unabhängigkeit als K.-o.-Kriterium gegen die
Alternativen geprüft. Mesh-Lite heilt Root-Ausfälle selbst; das Gateway ist als
Root nur Vermittler nach außen, nicht Dreh- und Angelpunkt.

# Verification

Zwei Knoten tauschen Nachrichten (Messwert senden, Antwort), während das
Gateway stromlos ist. Nach dem Einschalten des Gateways bleibt der Austausch
stabil, und die Gateway-Anbindung wächst ohne Neustart der Knoten hinzu.

# Human approval
Approver: pending  
Approved version/date: pending
