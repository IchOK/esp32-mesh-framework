# REQ-Familien — Grobgliederung (Diskussionsentwurf)

> **Kein REQ-Objekt, kein Governance-Dokument** — nur Gerüst für die Detailierung.
> Ziel: REQs sollen vollständig über die project.md-Ziele (§2/§3/§5) liegen und
> später saubere FSPEC-Andockpunkte haben. Nummern (REQ-00xx) erst bei Draft-Anlage.
>
> **Nummerierung (2026-10-26):** global aufsteigend statt family-basiert.
> Angelegte Drafts: REQ-0001…REQ-0003 (Familie 1), REQ-0004…REQ-0009 (Familie 2),
> REQ-0010 (Familie 1, nachgezogen). Weitere steigen bei REQ-0011.

## Familie 1 — Mesh-Betrieb (Grundfunktion)

- **REQ-0001 (R-MESH-1)** Multi-Hop: Knoten außerhalb Gateway-Reichweite liefert über Nachbarn (Ziel §2)
- **REQ-0002 (R-MESH-2)** Gatewayloser Betrieb: Datenaustausch zwischen Knoten ohne Gateway (Ziel §2)
- **REQ-0003 (R-MESH-3)** Selbstorganisation/Heilung: Netzwerk baut sich ohne manuellen Eingriff (Referenz User_Guide: Provisioning/Repair)
- **REQ-0010 (R-MESH-4)**: Knoteneinrichtung über knoteneigenen Mini-Webserver;
  AP-Hilfsmodus bei fehlender oder verstellter Mesh-Konfiguration oder
  Verbindungsunfähigkeit über einen parametrischen Zeitraum
- Messgrößen: Repair < 50 s, Provisioning < 60 s (Toolchain-Check Befund 6)

## Familie 2 — Datentransport (Gateway ↔ Mesh ↔ Node-RED)

- **REQ-0004 (R-DATA-1)** Asynchron: Knotendaten→Node-RED, mit Ringpuffer bei Node-RED-Trennung + Reihenfolge beim Wiederaufbau
- **REQ-0005 (R-DATA-2)** Synchron: direktes Durchreichen + sofortige Rückmeldung, kein Puffer, kein Retry, Fehler bei nicht erreichbar
- **REQ-0006 (R-DATA-3)** Klassenzuordnung: **keine Grundregel** — jeder Telegrammtyp wird
  im Nachrichtenlexikon einzeln als synchron/asynchron klassifiziert (2026-10-25)
- **REQ-0007 (R-DATA-4)** Heartbeat-/Offline-Erkennung: Timeout als **parametrisch** formuliert
  (Werte erst nach PoC-Messungen im FSPEC, 2026-10-25), letzte Werte bleiben sichtbar
- **REQ-0008 (R-DATA-5)** Timestamp: **jedes Telegramm enthält seinen Zeitstempel**
- **REQ-0009 (R-DATA-6)** Time-Sync: **Node-RED** sendet über das Gateway ein besonderes
  Time-Sync-Telegramm an alle Knoten; die Knoten führen die Zeit danach selbst
  weiter (Uhrstetigkeit/RTC-Frage als FSPEC-Punkt, T-025). Ohne Node-RED-Bezug:
  zuletzt bekannte Zeit mit Flag „nicht referenziert“ (2026-10-25)

## Familie 3 — Web-UI/Status (Gateway)

- **R-UI-1** Anzeigeverzögerung < 5 s (Ziel §2, Messmethode zu schärfen)
- **R-UI-2** UI statisch von SD, Core2-Display zeigt Status/Störung lokal
- **R-UI-3** **Basic Auth auf der Gateway-Webseite** (2026-10-25); die
  Telegrammebene hat **keinen Zugriffsschutz** — Node-RED-Anbindung gilt als
  vertrauenswürdiger Teil des LAN-Betriebs
- **R-UI-4** Offline-Knoten mit Zeitstempel, Browser und Node-RED gleiche Sicht

## Familie 4 — Firmware-Update

- **R-UPD-1** HTTP-Bereitstellung ab SD + Standard-OTA übers Mesh
- **R-UPD-2** A/B + Bootloader-Verify: kein Brick, automatischer Rollback
- **R-UPD-3** Einzel- und Gruppen-Update aus UI (mit Basic-Auth-Bestätigung) oder Node-RED
- **R-UPD-4** Update-Erkennbarkeit: kein Update ohne Anstoß von außen (Non-Goal-Grenze)

## Familie 5 — Betrieb/Ausfall

- **R-OPS-1** Node-RED-Ausfall: Gateway läuft weiter, puffert asynchron, liefert nach
- **R-OPS-2** Knoten ausgefallen: Offline-Anzeige, Werte mit Zeitstempel, Störung auf Display
- **R-OPS-3** Gateway-Ausfall: Mesh betriebsfähig für Knotentausch, Root-Heilung ohne Gateway (R-MESH-2 + User-Guide-Heilung)

## Familie 6 — Sicherheitsrahmen

- **R-SEC-1** LAN-Begrenzung: keine Cloud-/Internetanbindung (Non-Goal)
- **R-SEC-2** Zugang: Basic Auth auf Web-UI, Telegrammebene ohne Zugriffsschutz
  (Node-RED vertrauenswürdig); Checksummen als Schutzebene, bewusster Verzicht auf
  Image-Signierung/Secure Boot/TLS
- **R-SEC-3** Mesh-Sicherheit: WPA2-PSK Pflicht, **AES128 deaktiviert** (2026-10-25)

## Familie 7 — Redundanz (optional, M8)

- **R-RED-1** (wird jetzt als Draft angelegt, 2026-10-25) — nur bei ADR-0002-Freigabe;
  Normpunkte stehen im ADR (Übernahme <30 s, kein Sync, einvernehmliche Ausfallsicht).
  Umsetzung bleibt M8-Aufgriff.

## Querbezug Hardware (kein REQ, Randbedingung)

- Chip-Matrix S2/WROOM-32/C3/C6 alle offiziell unterstützt (Toolchain-Check);
  Flash-/Partition-Grenze T-005 beeinflusst nur R-UPD-2.

## Kanon-Status der Klärungen (2026-10-25)

| Punkt | Status |
|---|---|
| Sync/Async-Kriterium | Geklärt: Kategorie pro Telegrammtyp im Nachrichtenlexikon (T-013) |
| Timeout/Puffer-Werte | Geklärt: REQ parametrisch, Werte nach PoC im FSPEC (T-012) |
| Auth | Geklärt: Basic Auth Web, Telegramme ohne Zugriffsschutz (T-011) |
| Mesh-Security | Geklärt: WPA2-PSK, AES128 aus (T-015) |
| Redundanz | Geklärt: REQ-RED-1 als Draft angelegt, Umsetzung M8 (T-016) |
| Toolchain-Pfad | Geklärt: ESP-IDF pur, ADR-0004 (T-023) |
| Time-Sync | Geklärt: Node-RED-getrieben, Knoten führen Uhr selbst (T-024) |
| RTC-/Uhrstetigkeit | Neu T-025 → R-DATA-6, FSPEC Knoten-Firmware |
