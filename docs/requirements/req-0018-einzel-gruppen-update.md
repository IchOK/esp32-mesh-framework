---
id: REQ-0018
title: "Einzel- und Gruppen-Update aus Web-UI und Node-RED mit bewusster Auslösung"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0004]
---

# Motivation

project.md §3 (Anwendungsfall: Datei im Webinterface wählen, Knoten
aktualisieren) und §5 (Einzel- und Gruppen-Update über beide WS-Clients).

# Requirement

1. Ein Update wird wahl für **einen Knoten** oder **mehrere/alle Knoten
   gemeinsam** angestoßen — Adressierung nach Knotenliste oder als
   Gruppenaufruf.
2. Anstoßbar ist beides aus der **Web-UI** (nach Basic-Auth, REQ-0024) und
   über die **Node-RED-Schiene** (vertrauenswürdige Quelle, keine zusätzliche
   Anmeldung).
3. Ein Update startet **erst nach bewusster Bestätigung** des anstoßenden
   Platzes — kein automatischer Lauf über eine abgelegte Image-Datei.

# Rationale

Ohne Bestätigung würde eine auf die SD gelegte Datei zum stillen
Update-Auftrag; mit Bestätigung behält der Betreiber die Kontrolle, und die
Schwelle zur Gruppenwirkung ist markiert.

# Verification

Integrationstest: Gruppenupdate über UI und einzeln über Node-RED, jeweils
mit Bestätigungsprotokoll; abgelehnte Bestätigung startet kein Update.

# Human approval
Approver: pending  
Approved version/date: pending
