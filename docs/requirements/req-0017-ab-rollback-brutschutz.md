---
id: REQ-0017
title: "A/B-Partitionen mit Bootloader-Verify: kein Brick durch Update, automatischer Rollback"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001, ADR-0004]
---

# Motivation

project.md §5 (Ziel Firmware-Update, „Abbruch ohne Brick“): Ein Update darf
den Knoten nicht unbrauchbar machen, egal ob das neue Image fehlerhaft ist
oder der Abbruch mitten in der Übertragung fällt.

# Requirement

1. Jeder Knoten führt **zwei Update-Partitionen (A/B)**; ein neues Image wird
   in die Gegenpartition geschrieben, aktiviert wird es erst nach vollständiger
   Übernahme.
2. Ein als fehlerhaft erkannter Boot (Bootloader-Verify, anlaufende
   Anwendungs-Konfirmation) führt zum **automatischen Rücksprung auf die
   bisher laufende Version** ohne manuellen Eingriff.
3. Ein **abgebrochenes Update** (Verbindungsabruch, Spannung, Kraft) hinterlässt
   genau einen der zwei lauffähigen Stände — nie einen Zwischenstand.
4. Die Partitionierung ist je Chipplattform (S2/WROOM/C3/C6) unter der
   Flash-Grenze T-005 nachzuweisen.

# Rationale

OTA ohne A/B ist ein Risiko, das nur der Entwicklerstrang selbst behoben
hatte. A/B + Verify ist Standard-OTA-Verhalten und als Bedingung der
A-Abhängigkeit zu ADR-0004 ohne Extra-Mechanik zu haben.

# Verification

Integrationstest (je Zielplattform repräsentativ): defektes Image gespielt
→ Knoten bootet beim zweiten Versuch zurück auf die Altversion und meldet
den Rollback; Update-Abbbruch mittendrin → Knoten bleibt beim Stand,
Update neu anschließbar.

# Human approval
Approver: pending  
Approved version/date: pending
