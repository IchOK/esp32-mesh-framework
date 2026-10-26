---
id: REQ-0003
title: "Selbstorganisation und Selbstheilung des Mesh"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

Ein Heimnetz-Mesh mit Ausbau auf ~10+ Knoten muss ohne manuelle Verkabelung-
oder Konfiguration zurechtkommen (project.md §4) und muss auf Knotenausfälle
stabil reagieren.

# Requirement

Der Meshaufbau erfolgt ohne manuelle Eingriffe: Rootwahl, Elternwahl und
Schichtzuordnung werden vom Netzwerk selbst getroffen. Fällt ein Knoten aus,
reorganisieren sich betroffene Kinder ohne Neustart. Die Zeit bis zur
Reorganisation (Repair) ist als **parametrischer Grenzwert** zu fordern, dessen
konkreter Wert auf gemessener Feldbasis im FSPEC gesetzt wird (T-012); die
Herstellerkante < 50 s (Toolchain-Check Befund 6) dient als Anker.

# Rationale

Keine eigene Steuerungslogik für Join/Reconnect in der Knoten Firmware.
Mesh-Lite dokumentiert Self-Healing für Root- und Parent-Ausfall; der Wert
muss lokal verifiziert werden, da er von Aufbau und Störungen abhängt.

# Verification

PoC (T-007): Root während laufendem Betrieb vom Strom nehmen. Die verbliebenen
Knoten schließen sich innerhalb des Grenzwerts zu einem zusammenhängenden Netz
erneu, ohne manuellen Eingriff.

# Human approval
Approver: pending  
Approved version/date: pending
