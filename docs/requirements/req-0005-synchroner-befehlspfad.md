---
id: REQ-0005
title: "Synchroner Telegrammpfad mit Rückmeldung des Empfängers"
version: 0.2.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

project.md §5 für die Node-RED-Steuerung, aber die Klärung vom 2026-10-26 hat
die Einordnung verallgemeinert: Synchron/Asynchron ist eine Eigenschaft des
**Telegrammtyps**, nicht der Node-RED-Schiene. Auch zwischen Knoten werden
beide Klassen gesendet.

# Requirement

Synchrone Telegramme ziehen eine **direkte Aktion auf Empfängerseite** nach
sich (Beispiel Schaltbefehl) und erwarten eine **Rückmeldung des Empfängers**:

1. Das Telegramm wird zugestellt und der Sender wartet auf die Antwort.
2. Kann nicht zugestellt werden (Empfänger nicht erreichbar), erhält der
   Sender unverzüglich eine Fehler-Rückmeldung.
3. Bleibt die Rückmeldung aus, entscheidet **der Absender**, wie weiterfahren
   ist (Erneutversuch, Aufgabe, Alternativweg). Ein zentrales Timeout- oder
   Retry-Verhalten des Systems für Synchron-Telegramme gibt es nicht.
4. Durchleitende Stellen (Gateway) puffern, wiederholen und liefern
   Synchron-Telegramme nicht — nur asynchrone Telegramme fallen unter
   REQ-0004 und REQ-0011.

Asynchrone Telegramme (Beispiel zyklische Meldungen, Parameteranpassungen)
werden ohne Rückmergewartung gesendet. Die Klassifikation gilt für jede
Beziehung: Knoten↔Knoten, Knoten↔Gateway und Gateway↔Node-RED.

# Rationale

Steuerbefehle brauchen Wirkungsquittung, sonst ist der Sender blind. Über
das Ausbleiben einer Antwort zentral vorzubestimmen (Gateway-Automatik)
verlagert Entscheidungslogik an die falsche Stelle: nur der Absender kennt
den Kontext des Auftrags.

# Verification

M2-Integrationstest: Sync-Befehl an erreichbaren Knoten → Erfolg/Ergebnis
aus der Empfängeraktion. Sync-Befehl an abwesenden Knoten →
Fehler-Rückmeldung an den Absender, ohne Gateway-Retry und ohne Nachlieferung.
Ein synchrones Knoten↔Knoten-Telegramm verhält sich identisch: Antwort bei
Erreichbarkeit, Fehlermeldung oder Ausbleiben der Antwort sonst.

# Human approval
Approver: pending  
Approved version/date: pending
