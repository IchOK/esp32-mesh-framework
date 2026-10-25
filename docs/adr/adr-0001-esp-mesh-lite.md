---
id: ADR-0001
title: "Mesh-Funkstrecke: Einsatz von ESP-Mesh-Lite"
status: Approved
version: 0.2.0
date: 2026-10-25
---

# Context

Das Gateway (M5Core2, M5Stack Core2) vermittelt zwischen Heim-WLAN/Node-RED und einem
ESP-Mesh-Netzwerk mit Start ~10 Knoten, ausdrücklich mit Ausbau. Zwei Anforderungen
sind K.-o.-Kriterien:

1. **Multi-Hop / Weiterleitung:** Nicht alle Knoten erreichen direkt das Gateway.
   Das Mesh muss Daten über benachbarte Knoten weiterleiten.
2. **Betrieb ohne Gateway:** Der Datenaustausch zwischen Knoten muss auch laufen,
   wenn das Gateway nicht verbunden oder ausgefallen ist.

Weitere Randbedingungen:

- Firmware-Update von Knoten über das Mesh ist Zielgröße (see §2/§7 der
  Projektbeschreibung); die Funkstrecke muss Update-fähige Bandbreite (~1 MB
  pro Knoten) liefern können.
- Node-RED-Anbindung und Gateway-Betrieb sollen mit Standard-Mitteln (IP, Sockets)
  angebunden werden.
- Der Gateway ist kein Teil der Knotenpfade im Stand-alone-Betrieb.

# Decision

**Wir setzen ESP-Mesh-Lite als Mesh-Funkstrecke und Routing ein.**

ESP-Mesh-Lite ist ein aktives, von Espressif gepflegtes ESP-IDF-Component
(SoftAP + Station, IP pro Knoten, konfigurierbare Hop-Tiefe). Es funktioniert als
lokales Mesh ohne Root, Bridge oder Internet, der Gateway wird als normaler IP-Peer
angesprochen.

Diese Entscheidung wird für die Mesh-Funkstrecke zur bindenden Festlegung, sobald
die Freigabe des ADR-Textes durch den Projektverantwortlichen erteilt ist.

# Alternatives

| Option | Ergebnis | Begründung |
|---|---|---|
| **ESP-Mesh-Lite (gewählt)** | ✅ | Erfüllt Multi-Hop + Gateway-Unabhängigkeit, IP pro Knoten, aktive Pflege. |
| Eigenes Routing auf ESP-NOW | ❌ | Volle Kontrolle, aber Routing, ARQ, Relais-Regie und Peer-Handling (~20 Peers pro Node) wären eigene Firmware-Leistung. |
| ESP-WIFI-MESH (klassisch) | ❌ | Erfüllt die Anforderungen, aber von Espressif nicht weiterentwickelt und in neueren IDF-Versionen nur passiv gepflegt – unkalkulierbares Langzeitrisiko. |
| LoRa-Mesh (z. B. Meshtastic) | ❌ | Beste Reichweite, aber Bandbreite (kBit/s) reicht für Firmware-Update-Anforderung nicht. |
| Zigbee | ❌ | Sehr stabiles Mesh, erfordert aber ein separates Funkmodul pro Node – Bruch mit der Basis ESP-Plattform. |
| WiFi-Mesh (Repeater/Basis) | ❌ | Kein echtes Multi-Hop, nur Reichweitenverlängerung. |

# Consequences

**Positiv:**

- Beide Kernanforderungen (Multi-Hop, Betrieb ohne Gateway) sind ohne eigenen
  Routing-Code erfüllt.
- IP-Schicht bis zum Knoten: Gateway, Webserver und Node-RED-Software nutzen
  Standard-Sockets (TCP/UDP/MQTT).
- Firmware-OTA über das Mesh ist bei WiFi-Bandbreite realistisch.

**Negativ / zu managen:**

- ESP-Mesh-Lite ist jünger und schwächer im Feld erprobt als der Vorgänger;
  Doku teilweise im Umzug (meshlite.espressif.com).
- Abhängigkeit von einem weiteren IDF-Component; Versionsbindung muss sauber
  gepflegt werden.
- WiFi-Mesh und Heim-WLAN teilen sich dasselbe 2.4-GHz-Spektrum – Koexistenz,
  Kanalwahl und Störungen müssen getestet werden.
- Weiche, batteriebetriebene Knoten übernehmen ausdrücklich keine Relaisfunktion
  (Projektgrenze, project.md §6) – der Konflikt zwischen Tiefschlaf und aktiver
  SoftAP-Durchleitung tritt damit im Regelbetrieb nicht auf; Battery-Knoten sind
  reine Endpunkte.
- Firmware-Updates laufen immer vom Gateway aus angestoßen (project.md §6,
  §5); ein Update-Pfad ohne erreichbares Gateway ist kein Erfordernis und wird
  nicht betrachtet.

# Verification / Open questions

- [ ] Proof of Concept mit 2–3 Knoten + Gateway: Routing, Hop-Tiefe, Latenz,
      Durchsatz, Verhalten beim Gateway-Ausfall.
- [ ] Firmware-OTA über Mesh mit realistischer Firmware-Größe testen (Abbruch,
      Wiederanlauf).
- [ ] Kompatibilität der geplanten IDF-Version mit der Component-Registry klären.
- [ ] Relais-Anordnung im Zielgebäude planen (Ausbau auf >10 Knoten).

# Change history

| Version | Date | Change |
|---|---|---|
| 0.2.0 | 2026-10-25 | zwei negative Folgen entschärft per Projektentscheidung: Akku-Knoten arbeiten nicht als Relais (kein SoftAP-/Tiefschlaf-Konflikt); Update ohne Gateway ist kein Erfordernis. |
| 0.1.0 | 2026-10-25 | Erster ADR-Entwurf mit Bewertung der Alternativen. |

# Human approval
Approver: Projektverantwortlicher (explizite Freigabe im Gespräch, 2026-10-25)  
Approved version/date: 0.2.0 / 2026-10-25
