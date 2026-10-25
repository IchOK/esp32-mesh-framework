---
id: ADR-0002
title: "Gateway-Redundanz durch passives Master-Standby"
status: Draft
version: 0.2.0
date: 2026-10-25
---

# Context

Das Projekt legt Non-Goal 2 (§6) fest: keine automatische Redundanz, nur ein
Gateway. Eine Gateway-Havarie zieht jedoch alle Nicht-Mesh-Dienste inaktiv:
Web-UI, Node-RED-Anbindung, Update-Verteilung, Status-Puffer. Die Analyse hat
ergeben, dass ESP-Mesh-Lite als Peer-Mesh die **Grundvoraussetzung** für
Redundanz bereits mitbringt: Jeder Knoten ist gleichberechtigter
IP-Teilnehmer, und jedes weitere Mesh-Gerät hat automatisch Zugang zu allen
Knoten. Entscheidend ist daher nur die Rolle oberhalb des Meshs.

Drei Modelle waren betrachtet:

1. **Manueller Standy** – geringster Aufwand, aber Datenlücke bis zum händischen
   Tausch.
2. **Passives Master-Standby** – der Standy übernimmt automatisch, ohne aktive
   Koprenz (gewählt).
3. **Active/Active** – permanente Doppel-Dienste, braucht Leader-Wahl und
   Status-Replikation unter Last – für den Heimkontext unverhältnismäßig.

# Decision

**Das Redundanzmodell ist „passives Master-Standby“ mit den folgenden
Normpunkten:**

1. **Master-Erkennbarkeit:** Der aktive Gateway gibt sich gegenüber dem
   Mesh-Netz als Gateway zu erkennen (Mesh-Funktion als ganz normaler
   Mesh-Teilnehmer) und gegenüber Node-RED als aktives Gateway.
2. **Standy-Kennung:** Der Standy ist für das Mesh ein **normaler Knoten** –
   er stellt keine Mesh-fremde Gateway-Funktion bereit – und meldet sich
   gegenüber Node-RED explizit als **Standby-Gateway** (Rolle sichtbar, kein
   Datenfluss).
3. **Klare Datenhoheit:** Mesh-Knoten senden ihre Gateway-Daten ausschließlich
   an den Master.
4. **Inaktiver Puffer:** Der Node-RED-Ringpuffer des Standby ist, solange er
   Standy ist, inaktiv.
5. **Wechselseitige Aufsicht:** Master und Standy überwachen sich gegenseitig
   über Heartbeats (Mesh-IP-Erreichbarkeit, Node-RED-Verbindung, lokale
   Lebenszeichen).
6. **Node-RED-Gesprächskreis:** Node-RED führt datenführende Gespräche nur mit
   dem Master; die Standy-Verbindung ist reine Rollen- und
   Aufsichts-/Steuerungsfläche.

# Alternatives

| Modell | Ergebnis | Grund |
|---|---|---|
| Kein Failover (Non-Goal 2 Ursprung) | ❌ | Lücke bis zum händischen Tausch. |
| **Passives Master-Standby** | ✅ | Automatisch, ohne Koprenz, minimaler Zustand. |
| Active/Active | ❌ | Doppelte Sync-Telegramme, Status-Replikation unter Last, Leader-Wahl – unverhältnismäßig. |
| Manueller Standby | ❌ | Ist in passivem Modell automatisch enthalten. |

# Consequences

**Positiv:**

- Failover ohne doppelte Befehlsquellen – nur ein Knoten kann Sync-Telegramme
  und Update-Kampagnen führen.
- Die Mesh-Ebene bleibt von Redundanzlogik unberührt; Knoten-Firmware braucht
  keine Zweifach-Zuordnung.
- ESP-Mesh-Lite-Lizenz bleibt erhalten, da nur der Standby als zusätzlicher
  Mesh-Teilnehmer fungiert.

**Zu beherrschen:**

- **Rollen-Ambiguität:** Master und Standby wissen voneinander; die Übernahme darf nur
  bei eindeutiger Bedingung starten (s.u.).
- **Pufferverlust beim Master-Tod:** Da der Standby-Puffer leer ist, fallen
  nicht zugestellte, ungehaltene Daten des Masters weg – nur der aktuelle
  Knotenbestand wird übernommen, soweit synchronisiert.
- **Image-Bestand:** Die SD des Standby muss denselben Image-Stand haben; die
  Update-Verwaltung muss diese Frage klären (Spiegelung, gemeinsame Quelle, oder
  Verzicht auf Automatik nach Ausfall).

**Normative Regeln:**

- **Rollen-Kennzeichnung** in jeder Mesh- und Node-RED-Funktion, keine implizite
  Rolle – Knoten, die „auch“ als Gateway dienen.
- **Fence gegen Doppelempfang:** Nur der aktuelle Master darf Sync-Telegramme
  beantworten, Knoten-Daten entgegennehmen und Update-Aufträge vergeben.
- **Übernahme-Bedingung:** Standy und Node-RED müssen sich einig sein, dass der
  Master ausgefallen ist, bevor Rollenwechsel. Ist die Sicht uneinheitlich
  (Beispiel: Standy kann den Master erreichen, Node-RED nicht), übernimmt der
  Standy nicht – er geht in Status „beobachten“ und wartet.
- **Node-RED-Seitig** wird der Rollenwechsel über die Standy-Bekanntheit oder
  eine neue Master-Bekanntheit erkannt und die Datenverbindung umgestellt.

# Verification / Open questions

- [ ] PoC: zwei Geräte im Mesh, Heartbeat-Ausfall, Übernahme durch
      Standy, Node-RED-Anschlusswechsel. **Status:** steht aus — wird nur relevant,
      wenn Redundanz zum Umfang wird (M8, nach Freigabe dieses ADR).
- [x] Synchronisierungsumfang festlegen (Node-Tabelle, Images, Update-Stand)
      und Übertragungsweg (Mesh-IP, wann). **Entschieden 2026-10-25: kein
      Laufzeit-Sync.** Node-Sichten bauen sich beim Standby aus den eigenen
      Mesh-Heartbeats auf; laufende Update-Kampagnen werden bei Übernahme nicht
      fortgesetzt, sondern müssen neu angestoßen werden.
- [x] Übernahmelaufzeit als Zielwert definieren (Rückfallwert, z. B. < 30 s).
      **Entschlossen 2026-10-25:** Zielwert **< 30 s**; Herzschlagintervall 5 s,
      Ausfallsicht nach 3 ausbleibenden Herzschlägen (~15 s), plus Umschaltzeit.
- [x] Bildersynchronisation der SD-Inhalte klären. **Entschlossen 2026-10-25:**
      **manuelle Spiegelung** beim Aufstellen (Images zu Installationszeiten auf
      beide SD-Karten), kein Sync-Mechanismus zur Laufzeit.
- [x] Verhalten bei Netz-Hälfte: was ist „Ausfall“, wenn nur die Node-RED
      Sicht fehlschlägt. **Geklärt aus den normative Regeln:** nur übereinstimmende
      Ausfallsicht (Standby und Node-RED) führt zu Übernahme; eine Sicht allein
      bleibt Status „beobachten“ mit sichtbarer Meldung — keine Übernahme, kein
      Doppeldienst.

# Change history

| Version | Date | Change |
|---|---|---|
| 0.2.0 | 2026-10-25 | offene Punkte geklärt (projektverantwortlich entschieden): kein Laufzeit-Sync, Übernahme < 30 s (5-s-Heartbeat, 3 Ausbleiben), manuelle Image-Spiegelung; Netz-Hälfte aus normative Regeln abgeleitet geklärt. PoC bleibt bei M8-Aufgriff. |
| 0.1.0 | 2026-10-25 | Erster Entwurf mit Modellvergleich und normativen Regeln. |

# Human approval
Approver: pending  
Approved version/date: pending
