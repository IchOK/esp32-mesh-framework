---
id: PROJECT-0001
title: "ESP32 Mesh Framework"
version: 0.28.0
template_version: 0.1.0
created_at: 2026-10-25
modified_at: 2026-10-25
author: ""
modified_by: ""
status: Draft
approved_by: null
approved_at: null
supersedes: null
references:
  requirements: []
  functional_specs: []
  adrs: [ADR-0001, ADR-0002, ADR-0003, ADR-0004]
review_required: []
---

# Project Description

**Positioning:** This document conveys project context and development goals.
It does not replace approved requirements, functional specifications, or ADRs.
Statements about concrete components reflect the current planning state, not
immutable rules.

**Führung als Living Context (Entscheidung 2026-10-25):** Dieses Dokument wird
laufend weitergepflegt und nicht auf einem Referenzstand eingefroren; REQs und
FSPECs referenzieren die jeweils aktuelle Fassung als Kontext. Verbindliche
Aussagen zu Akzeptanz und Verhalten stehen ausschließlich in REQ/FSPEC/ADR.

> Structure and content guidance: [`../docs/templates/project.md`](../docs/templates/project.md).

## 1. Purpose and Vision

**Projektbezeichnung:** „ESP32 Mesh Framework“ — Umbenennung aus „M5Core2 Mesh
Gateway“ (2026-10-25); der Hardwarebezug ist austauschbar, der Kern ist das
Mesh-Framework.

Ein Gateway vermittelt zwischen dem privaten Heim-WLAN und einem
ESP-Mesh-Netzwerk. Es nimmt Daten der Mesh-Knoten (ESP32) entgegen und
überträgt sie zu Node-RED, wo sie mit lokalen Geräten und Cloud-Anbindungen
verknüpft werden. Umgekehrt leitet es Befehle und Aufträge von Node-RED an
die Knoten weiter. Das Gateway ist die Grundlage für spätere ESP-Projekte und
soll neue Projekte ermöglichen.

Über einen eigenen Webserver am Gateway können Informationen zu den Mesh-Nodes abgefragt
und die Firmware der Nodes aktualisiert werden.

Das Mesh muss die Daten der Knoten weiterleiten, die nicht direkt die Empfangsreichweite
des Gateway erreichen, und es muss zusätzlich ohne Gateway lauffähig sein, damit die
Knoten auch untereinander Daten austauschen können.

Knoten sind eigenständige Elemente: Sie senden aktiv Daten an das Gateway und andere
Knoten und fragen dort Daten an.

Entwicklungsansatz: aufeinander aufbauende Einzelfunktionen.

## 2. Goals and Success Criteria at Project Level

| Ziel | Messgröße | Status |
|---|---|---|
| Knotendaten erreichen Node-RED | Wert eines Knoten ist sichtbar | noch nicht als REQ formalisiert |
| Node-RED steuert Knoten | Befehl an Knoten | noch nicht als REQ formalisiert |
| Knoteninformationen per Web | Anzeigeverzögerung < 5 Sekunden | noch nicht als REQ formalisiert |
| Firmware-Update | Über Webinterface, Abbruch ohne Brick | Akzeptanzkriterien in FSPEC zu klären |
| Mesh ohne Gateway betriebsfähig | 2 Knoten tauschen Daten, Gateway ausgeschaltet | noch nicht als REQ formalisiert |
| Reichweitenverlängerung | Knoten außerhalb der Gateway-Reichweite | noch nicht als REQ formalisiert |

Diese Ziele auf Projektebene **sind noch keine formally approved Requirements**;
Messmethode, Toleranzen und Abnahmekriterien werden in Requirements und
Functional Specifications festgelegt.

## 3. Planned Use Cases

1. Mesh-Knoten senden regelmäßig Werte an das Gateway, Gateway
   überträgt sie an Node-RED.
2. Ein Benutzer ruft die Weboberfläche des Gateways auf und sieht Status und
   Metadaten der Knoten.
3. Ein Benutzer wählt im Webinterface eine Firmware-Datei und aktualisiert
   einen ausgewählten Knoten.

4. Node-RED sendet Befehle/Aufträge an die Mesh-Knoten.

5. Zwei Knoten tauschen Daten untereinander, während das Gateway ausgefahren ist.

6. Ein Knoten außerhalb der unmittelbaren Gateway-Reichweite liefert weiterhin Daten über benachbarte Knoten.

## 4. System Overview

### Hardware

- Gateway-Hardware: **M5Core2**, verbunden mit dem Node-RED-Host über **USB** (Flashen/Logging).
- Speicher: **SD-Card im Core2** – Ziel für statische Webdateien, Logs und
  Firmware-Images.
- Mesh-Knoten: ESP32-basierte Module, Start mit **~10 Knoten**, Ausbau ausdrücklich
  vorgesehen. Bestückte Knotentypen (Chipmarkierung bestätigt):
  - Wemos S2 Mini V1.0.0 (ESP32-S2)
  - Lolin32 mit ESP-WROOM-32 (klassischer ESP32)
  - SuperMini ESP32-C3
  - SuperMini ESP32-C6
  - Wemos D1 mini, bestückt mit **ESP32-WROOM-32** (zwei Boards mit unterschiedlicher
    Markierung, Chip-Aufdruck je klassischer ESP32):
    • AZ-Delivery ESP32-WROOM-32 (BT v4.2, IEEE 802.11 b/g/n)
    • ESP-WROOM-32, FCC ID 2AC7Z-ESP32WROOM32
    Damit ist die frühere ESP8266-Sorge vom Tisch – beide sind ESP-Mesh-Lite-Targets.
- **Kompatibilitätsstatus:** Alle Knotentypen (S2, WROOM-32, C3, C6) liegen auf
  unterstützten ESP-Mesh-Lite-Targets; offen bleibt die Implementierung je
  Plattform (siehe §8).
- **Konsequenz gemischte Plattformen:** Firmware- und Routing-Komponenten
  (ESP-Mesh-Lite, OTA, WebSocket) müssen auf **vier Chipsätze** gepflegt werden –
  die beiden D1-mini-Karten teilen sich den WROOM-32 mit der Lolin32.
- Funkstrecke: **ESP-Mesh-Lite** – Entscheidung in [`docs/adr/adr-0001-esp-mesh-lite.md`](../docs/adr/adr-0001-esp-mesh-lite.md) (approved, v0.2.0 vom 2026-10-25).

### Software

- Gateway-Firmware: **ESP-IDF pur** (Entscheidung ADR-0004, T-023) mit fest
  gebundener IDF-5.x-Version und ESP-Mesh-Lite als registrierte Component;
  Arduino/PlatformIO nur bei begründeter Notwendigkeit als Aufbau darüber.
- Node-RED: läuft auf **Raspberry Pi / Mini-PC**; Anbindung des Gateways über **WebSocket mit JSON** (Node-RED-Host im Heim-WLAN).
- Webserver: **auf dem Gateway** – liefert die statische Weboberfläche **von der
  SD-Card** und stellt Status/Steuerung per **WebSocket mit JSON** bereit (Browser
  und Node-RED teilen denselben Server). **Basic Auth auf der Webseite**
  (Entscheidung 2026-10-25); die Telegramm-Ebene hat bewusst keinen
  Zugriffsschutz — Node-RED zählt zum vertrauenswürdigen LAN-Betrieb.

### Connectivity

- Mesh-Knoten ↔ Gateway: Pfad, Protokoll und Reichweite noch zu klären.
- **WebSocket-Server auf dem Gateway:** Browser-UI und Node-RED verbinden sich
  ausnahmslos als Clients zum selben WebSocket-Server des Gateways.
- Gateway ↔ Node-RED: JSON-Nachrichten über diesen gemeinsamen WebSocket
  (Node-RED-Host Raspberry Pi / Mini-PC). USB am Core2 nur für Flashen/Logging –
  Bestätigung offen.
- Gateway ↔ Mesh-Knoten: IP über ESP-Mesh-Lite (Multi-Hop).
- **Zeitbasis:** Time-Sync innerhalb des Netzwerkes, getrieben von **Node-RED**:
  Node-RED sendet über das Gateway ein besonderes Time-Sync-Telegramm an alle
  Knoten; die Knoten führen die Zeit danach selbst weiter. Telegramme führen
  ihren Zeitstempel mit (RTC-/Stetigkeitsfrage → T-025).
- Telegramme Gateway ↔ Knoten im Auftrag von Node-RED: zwei Klassen –
  **asynchron** (Daten, ggf. gepuffert) und **synchron** (direkt an den Knoten mit
  sofortiger Rückmeldung, nicht gepuffert).
- Gateway-Redundanz (Konzept ADR-0002, Draft): Master und Standby-Pair – Master
  als Dienstgeber in Mesh und Node-RED, Standby als normaler Mesh-Knoten mit
  Standby-Kennung gegenüber Node-RED; Kopplung über Heartbeats, Übernahme nur
  bei einvernehmlicher Ausfallsicht.
- Firmware-Transportpfad: Gateway stellt Images von der SD-Card per **HTTP** bereit;
  der Knoten zieht per **Standard-OTA** (esp_ota / Update-Library) über die
  Mesh-IP (ESP-Mesh-Lite, Multi-Hop). **A/B-Partitionen + Bootloader-Verify**
  fangen fehlgeschlagene Updates ab.

### Backend/Operations

- Node-RED zentrale Logikinstanz im Heimnetz, Gateway als Vermittlungsinstanz.
- Gateway-Funktion auf Webserver beschränken; keine Datenhaltung noch zu klären.

## 5. Operation, Safety and Control

- Firmware-Update nur nach bewusster Benutzerbestätigung; ausgelöst über die
  per Basic Auth geschützte Web-UI oder über die Node-RED-Schiene
  (vertrauenswürdige Quelle, keine zusätzliche Anmeldung); die Images liegen
  zuvor auf der SD-Card des Gateways.
- Update-Auslösung als **Einzel- und Gruppen-Update**, aus Web-UI und Node-RED
  möglich (beide WS-Clients desselben Backends).
- Knoten mit **A/B-Partition und Bootloader-Verify**: bootet das neue Image nicht
  oder quittiert es Fehler, springt der Knoten automatisch auf die alte Version
  zurück – kein Brick durch Updates.
- Statusmeldungen laufen über die WebSocket-Verbindung (UI und Node-RED).
- **Node-RED nicht erreichbar:** Das Gateway arbeitet unverändert weiter, puffert
  die **asynchronen** Datenmeldungen in einem Ringpuffer und liefert sie nach dem
  Wiederaufbau der WebSocket-Verbindung der Reihenfolge nach nach. **Synchron
  gekennzeichnete Telegramme werden nicht gepuffert** – ohne bestehende Verbindung
  können sie nicht gestellt oder beantwortet werden.
- **Synchron-Telegramme:** Von Node-RED als synchron gekennzeichnete Telegramme
  leitet das Gateway direkt an den Mesh-Knoten weiter und gibt dessen Rückmeldung
  (Erfolg/Ergebnis) unverzüglich an Node-RED zurück. Ist der Knoten nicht
  erreichbar, antwortet das Gateway sofort mit einer Fehler-Rückmeldung – der
  Auftrag wird nicht zwischengespeichert, nicht erneut versucht und nicht
  nachgeliefert.
- **Knoten nicht erreichbar:** Das Gateway führt für jeden Knoten einen
  Heartbeat-/Zeitstempel-Timeout; nach X Sekunden ohne Meldung (Wert offen) gilt
  der Knoten als **offline**. Die letzten Werte bleiben mit Zeitstempel in UI und
  Node-RED sichtbar.
- **Lokale Anzeige:** Status und Störungen sind auf dem Core2-Display ablesbar –
  damit bleibt das Fehlerbild erkennbar, auch ohne geöffnete UI oder Node-RED.

## 6. Project Boundaries and Non-Goals

Der erste Umfang schließt ausdrücklich aus:

- **Keine Cloud-/Internet-Anbindung.** Vernetzung endet im eigenen LAN; Node-RED
  ist die einzige zentrale Instanz. Eine etwaige spätere Cloud-Anbindung ist ein
  Node-RED-Thema, kein Gateway-Umfang.
- **Nur ein aktives Gateway, keine Redundanz außer nach ADR-0002.** Kein
  Gateway-Failover, kein verteilter Mesh-Betrieb ohne zentrale Vermittlung.
  Ein passives Master-Standby ist als optionales Konzept in
  [`ADR-0002`](../docs/adr/adr-0002-passive-gateway-redundancy.md) (Draft)
  beschrieben, noch nicht freigegeben und noch nicht Umfang.
- **Keine Sicherheitsversprechen über das LAN hinaus.** Login am Gateway und
  Prüfsummen als Schutzebene, bewusster Verzicht auf Image-Signierung/Secure
  Boot und TLS. Betrieb in nicht vertrauenswürdigen Netzen ist nicht vorgesehen.
- **Stromsparprofil nur für Akku-Knoten ohne Relais.** Der Regelbetrieb geht von
  durchgehender Funkverfügbarkeit aus. Zukünftig planned sind spezielle
  Akku-Knoten; sie übernehmen ausdrücklich **keine Relaisfunktion** –
  Schlafprofile und Lebensdaueroptimierung betreffen nur diese Klasse und sind
  kein Ziel für ausbaubare Knoten.
- **Keine Auto-Update-Funktion auf Knotenebene.** Firmware-Aktualisierungen werden
  ausschließlich über Gateway-Web-UI oder Node-RED angestoßen; Knoten starten
  keine eigenen Update-Vorgänge.
- **Kein Produkt-/Zulassungsstatus.** Maker-/Heimanwendungs-Stand; keine
  regulatorische Konformität, keine Industriezuverlässigkeit zugesagt.
- **Keine dauerhafte Werkzeugbindung.** Versionierung von ESP-IDF/PlatformIO,
  Node-RED und Tools bleibt Laufzeitkonfiguration; die Mesh-Entscheidung ist
  per ADR-0001 für die Projektphase gebunden und kann im ADR-Zyklus wieder
  geöffnet werden.

## 7. Planned Milestones

| Phase | Inhalt | Evidence (preliminary) |
|---|---|---|
| M1 | Mesh-Aufbau M5Core2 ↔ **SuperMini C3** als einziger Knoten | Zwei Mesh-Teilnehmer halten Verbindung, Gateway und Knoten erreichen sich über die Mesh-IP |
| M2 | Gateway-End-to-End-Test **synchron**: Node-RED ↔ M5Core2 ↔ SuperMini C3 | Sync-Telegramm quittiert vom Knoten, Fehler-Rückmeldung bei nicht erreichbarem Knoten |
| M3 | Multi-Hop: Weiterleitung **mit** und **ohne** Gateway | Zwei Knoten tauschen Daten, ohne dass das Gateway gehört wird; mit Gateway kommt dieselbe Nachricht am Gateway an |
| M4 | Ausbau auf die übrigen Knoten-Chipsätze (ESP32/WROOM, S2, C6) | Jeder Chip im Mesh, OTA-Partition je Chip nutzbar, identische Mesh-Funktion |
| M5 | Gateway **asynchron** mit Ringpuffer | Asynchrone Knotendaten erreichen Node-RED, Puffer liefert nach einer Node-RED-Trennung auf, Sync bleibt ungepuffert |
| M6 | Webserver am Gateway: UI von SD, Login, Status-/Offline-Anzeige, Core2-Display | Knotenstatus per Web abfragbar, Offline-Knoten mit Zeitstempel, Display zeigt Störung |
| M7 | Firmware-Update: HTTP-Pull ab SD, A/B-Rollback, Einzel- und Gruppen-Update | Gruppen-Update läuft ohne Brick, Rollback-Falle beim Abbruch, Update nur durch Web-UI/Node-RED angestoßen |
| M8 (optional) | Gateway-Redundanz nach ADR-0002 – nur nach Freigabe | Passives Master-Standby übernimmt im Test ohne Doppelempfang |

Die Abfolge wird durch freigegebene Spezifikationen konkretisiert, diese Tabelle ist eine
**Planung, kein Implementierungsauftrag**.

## 8. Open Decisions and Risks

- Gateway- und Knoten-Hardware sind geklärt (§4); Git-Hosting ist geklärt: GitHub,
  vier Repos nach project.md §9.
- Mesh-Funkstrecke: **entschieden für ESP-Mesh-Lite** – dokumentiert und
  freigegeben in [`ADR-0001`](../docs/adr/adr-0001-esp-mesh-lite.md) (v0.2.0,
  2026-10-25). Verbleibende ADR-Punkte (PoC Routing/Hop, OTA übers Mesh,
  Registry-Kompatibilität) bleiben als T-003…T-008 im Register und werden im
  M1/M2-Aufbau abgearbeitet.
- USB-Anbindung des Gateways: als Flashen-/Logging-Pfad vorgesehen, Gegencheck offen.
- ESP-Mesh-Lite-Einbindung: **Toolchain-Pfad entschieden: ESP-IDF pur**
  ( [`ADR-0004`](../docs/adr/adr-0004-esp-idf-toolchain.md), Draft), Recherche-
  Grundlage in [`docs/poc/toolchain-check.md`](../docs/poc/toolchain-check.md).
- Firmware-Update-Mechanik steht (HTTP-Pull, A/B-Rollback, Einzel + Gruppe); offen:
  Image-Verwaltung auf der SD (Namensschema, Versionierung, Anzahl gehaltener
  Images) und Meldungsformat für Update-Anstoß/Fortschritt.
- Signierung von Images ist bewusst keine Voraussetzung (Secure Boot nicht
  eingebrannt) – Sicherheitsniveau bleibt bei LAN + Basic Auth (Web) + Checksummen.
- Webserver-Authentifizierung ist **geklärt: Basic Auth auf der Webseite**, die
  Telegramm-/WebSocket-Ebene bleibt ohne Zugriffsschutz; Klartext im LAN ist
  hingenommen. (2026-10-25)
- Timeout-/Puffer-/Antwortwerte für Offline-Erkennung, Node-RED-Puffer und
  Synchron-Antworten sind **parametrisch zu fordern**; konkrete Werte erst nach
  PoC-Messung im FSPEC. Telegrammklassen **Kategorie für Kategorie** im
  Nachrichtenlexikon.
- **Gemischte Chipsatz-Matrix:** Hardware-Klassen sind geklärt (ESP32, S2, C3, C6);
  offen bleibt die Verifikation je Board in ESP-IDF (Builds, OTA,
  WebSocket, ESP-Mesh-Lite-Funktionsumfang).
- **ESP-Mesh-Lite Wartungszusage:** bestätigt **Maintenance Mode** (2026-10-25,
  T-006 Done, Toolchain-Check) — nur Sicherheits-/Bugfix-Patches, Iteration im
  privaten Repo. Versionsbindung auf v1.0.x + IDF 5.x pflegen.
- **Flash-Größe je Board gegen A/B-Partitionen prüfen:** 4-MB-Module (S2 Mini,
  SuperMinis) halbieren den nutzbaren Anwendungsspeicher – bei voller
  Firmware-Größe ggf. eng.
- **Gateway-Redundanz (ADR-0002, Draft, Klärpunkte geklärt 2026-10-25):**
  Übernahme < 30 s (5-s-Heartbeat), kein Laufzeit-Sync, manuelle
  Image-Spiegelung, Übernahme nur bei einvernehmlicher Ausfallsicht. Freigabe
  und Umfangentscheidung (M8) stehen noch aus (T-016).
- Sicherheitsniveau im Mesh
- Die offenen Punkte dieses Abschnitts sind zentral im [`todo-register.md`](todo-register.md)
  mit IDs (T-001 ff.) geführt; §8 bleibt die fachliche Beschreibung, der Register
  den Arbeitszustand.

## 9. Development and Documentation Process

- Versionskontrolle und Hosting: **Git mit Remote-Hosting bei GitHub (öffentlich)**, 2026-10-25
  entschieden; Repos und Benennung nach Softwarestück (siehe auch ADR-0003, Cross-Repo-Traceability):
  - `esp32-mesh-framework` – Main: Governance, Requirements, FSPEC/ADR, Reports (dieses Repo)
  - `esp32-mesh-framework-gateway` – Gateway-Firmware (Core2), statische Web-UI, Update-Werkzeuge
  - `esp32-mesh-framework-nodes` – Knoten-Firmware (plattformübergreifend für ESP32, S2, C3, C6)
  - `esp32-mesh-framework-node-red` – Node-RED-Flows und eigene Nodes zur Mesh-Anbindung
  - `esp32-mesh-framework-lib` – nur bei realem Duplikations-Trigger (mindestens zwei betroffene
    Projekte, identischer Funktionskern); dann als PlatformIO-Bibliothek mit Semver, kein Vorabbau (T-020)
  - Arbeitsverzeichnis-Parallelen (`../<repo>/`) statt Unterverzeichnissen; keine Submodule (Worktree-Isolation)
- Prozessregeln: [`../docs/process/document-lifecycle.md`](../docs/process/document-lifecycle.md), [`../docs/process/multi-agent-orchestration.md`](../docs/process/multi-agent-orchestration.md)
- [`AGENTS.md`](../AGENTS.md) — verbindliche Agenten-Anweisungen.
- Ablauf: Idee → REQ freigegeben → FSPEC freigegeben → **expliziter
  Startbefehl** → Tasks → Verifikation → Report.
- Concurrency: governed by [`config/agent-policy.yaml`](../config/agent-policy.yaml).
- Modelle sind austauschbare Laufzeitkonfiguration; der Einsatz pro Task wird
  protokolliert.

## 10. Change History

| Version | Date | Change |
|---|---|---|
| 0.28.0 | 2026-10-25 | REQ-Kanons entschieden: sync/asyn als Telegrammtyp-Kategorien, Parameter parametrisch, Basic Auth nur Web (Telegramme ungesichert), WPA2-PSK ohne AES128, Time-Sync Node-RED-geführt, Toolchain ESP-IDF pur (ADR-0004), REQ-RED-1 als Draft. |
| 0.27.0 | 2026-10-25 | Zeitbasis ergänzt: Time-Sync im Netzwerk, Telegramme mit Zeitstempel (§4, R-DATA-5/6 im REQ-Gerüst, T-024). |
| 0.26.0 | 2026-10-25 | Toolchain-Check extern dokumentiert (docs/poc/toolchain-check.md, T-003 Stand/T-006 Done); REQ-Familien-Gerüst (project/req-families-outline.md) als Diskussionsentwurf; T-023 Toolchain-Pfad-ADR angelegt. |
| 0.25.0 | 2026-10-25 | ADR-0002 Klärpunkte entschieden (ADR v0.2.0: kein Sync, <30 s Übernahme, manuelle Spiegelung, Netz-Hälfte = beobachten); ADR-0003 Prüfpunkte: Vorlagen-Check abgehakt, Kantennachweis offen. |
| 0.24.0 | 2026-10-25 | ADR-0001 vom Projektverantwortlichen freigegeben (v0.2.0, 2026-10-25); §4/§8 auf approved-Fassung geführt, Vorbehalt Wartungsstatus als T-006. |
| 0.23.0 | 2026-10-25 | Projektbenennung auf „ESP32 Mesh Framework“ gehoben (Hardware-Marker M5Core2 nur noch Hardwarebezug); §9 Repo-Struktur in `esp32-mesh-framework*` überführt, Hosting auf öffentlich festgelegt; ADR-0003 (Cross-Repo-Traceability) referenziert. |
| 0.22.0 | 2026-10-25 | Positionierung als Living Context dokumentiert (Entscheidung 2026-10-25, P0.3); §9 Hosting konkretisiert: GitHub privat, Repo-Bezeichner offen (P0.2, T-001). |
| 0.21.0 | 2026-10-25 | §8: Verweis auf zentrales todo-register.md (T-001 ff.); Register mit offenen Punkten befüllt (Phase 0, P0.4). |
| 0.20.0 | 2026-10-25 | §7 Meilensteine verfeinert: Zwei-Node-Start (Core2 + SuperMini C3), Sync vor Multi-Hop, Chip-Ausbau und Async getrennt, M6–M8 für UI/Update/Redundanz. |
| 0.19.0 | 2026-10-25 | §7 Meilensteine auf den aktuellen Stand gebracht (Toolchain-Check, Sync/Async, Gruppen-Update, optionale Redundanz). |
| 0.18.0 | 2026-10-25 | §6-Kommentare eingearbeitet: Funktionsbeschreibung nach §1 verlagert, Non-Goal auf „keine Auto-Update-Funktion auf Knotenebene“ gekürzt. |
| 0.17.0 | 2026-10-25 | §6: Regelungs-Abgrenzung entfällt; autonome Daten-Knoten bleiben, Update-Auslösung nur durch Gateway/Node-RED als neue Grenze. |
| 0.16.0 | 2026-10-25 | §6 nachgeschärft: Akku-Knoten ohne Relaisfunktion; Knoten senden/afragen autonom, Regelungsautomatik bleibt Non-Goal. |
| 0.15.0 | 2026-10-25 | ADR-0002 (passives Master-Standby) angelegt; §4/§6/§8 auf das Redundanz-Konzept geführt. |
| 0.14.0 | 2026-10-25 | §6 Non-Goals ausgearbeitet (7 Grenzen statt 3 Stichpunkte). |
| 0.13.0 | 2026-10-25 | Git-Konzept: Remote-Hosting (privat) mit drei Repos (gateway, nodes, node-red-flows); §8/§9 bereinigt. |
| 0.12.0 | 2026-10-25 | D1-mini-Boards als ESP32-WROOM-32 verifiziert; vier Chipsätze (ESP32, S2, C3, C6) statt fünf, ESP8266-Falle entschärft. |
| 0.11.0 | 2026-10-25 | Knoten-Hardware dokumentiert (fünf Boards); Kompatibilitäts- und Wartungsrisiken (Maintenance Mode, Flash-Größe) festgehalten. |
| 0.10.0 | 2026-10-25 | Telegramm-Klassen eingeführt: synchron (direkt, Feedback, ungedämpft) vs. asynchron (gepuffert, nachlieferbar). |
| 0.9.0 | 2026-10-25 | Ausfallpfade festgelegt: Node-RED-Puffer mit Nachlieferung, Heartbeat-Timeout, lokale Display-Anzeige. |
| 0.8.0 | 2026-10-25 | Firmware-Update-Konzept: HTTP-Pull ab SD, A/B-Rollback, Einzel- und Gruppen-Update über UI/Node-RED. |
| 0.7.0 | 2026-10-25 | WebSocket-Topologie (Server am Gateway, UI und Node-RED als Clients) und SD-Card-Speicher festgehalten. |
| 0.6.0 | 2026-10-25 | Webserver-Konzept festgehalten: UI am Gateway, WebSocket/JSON, Login vor Updates. |
| 0.5.0 | 2026-10-25 | Node-RED-Anbindung (WebSocket/JSON), Host (Raspberry Pi/Mini-PC) und Framework (Arduino/PlatformIO) festgehalten. |
| 0.4.0 | 2026-10-25 | ADR-0001 (ESP-Mesh-Lite) angelegt, Projektdokument auf die Entscheidung geführt. |
| 0.3.0 | 2026-10-25 | Mesh-Anforderungen konkretisiert (Multi-Hop, gatewayloser Betrieb); Kandidaten bewertet, ESP-Mesh-Lite als Favorit. |
| 0.2.0 | 2026-10-25 | Gateway-Hardware bestätigt (M5Core2, USB-Anbindung), Knotenzuschnitt ~10 mit Ausbau; Mesh-Optionen gegenübergestellt. |
| 0.1.0 | 2026-10-25 | Erster Entwurf der Projektbeschreibung aus mündlicher Beschreibung. |
