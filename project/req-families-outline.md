# REQ-Familien — Grobgliederung (Diskussionsentwurf)

> **Kein REQ-Objekt, kein Governance-Dokument** — nur Gerüst für die Detailierung.
> Ziel: REQs sollen vollständig über die project.md-Ziele (§2/§3/§5) liegen und
> später saubere FSPEC-Andockpunkte haben. Nummern (REQ-00xx) erst bei Draft-Anlage.

## Familie 1 — Mesh-Betrieb (Grundfunktion)

- **R-MESH-1** Multi-Hop: Knoten außerhalb Gateway-Reichweite liefert über Nachbarn (Ziel §2)
- **R-MESH-2** Gatewayloser Betrieb: Datenaustausch zwischen Knoten ohne Gateway (Ziel §2)
- **R-MESH-3** Selbstorganisation/Heilung: Netzwerk baut sich ohne manuellen Eingriff (Referanz User_Guide: Provisioning/Repair)
- Messgrößen: Repair < 50 s, Provisioning < 60 s (Toolchain-Check Befund 6)

## Familie 2 — Datentransport (Gateway ↔ Mesh ↔ Node-RED)

- **R-DATA-1** Asynchron: Knotendaten→Node-RED, mit Ringpuffer bei Node-RED-Trennung + Reihenfolge beim Wiederaufbau
- **R-DATA-2** Synchron: direktes Durchreichen + sofortige Rückmeldung, kein Puffer, kein Retry, Fehler bei nicht erreichbar
- **R-DATA-3** Klassenzuordnung: Telegramme fallen unter eine der Klassen (Kriterium aus T-013, noch offen)
- **R-DATA-4** Heartbeat-/Offline-Erkennung: Timeout X (Parameter im FSPEC), letzte Werte bleiben sichtbar

## Familie 3 — Web-UI/Status (Gateway)

- **R-UI-1** Anzeigeverzögerung < 5 s (Ziel §2, Messmethode zu schärfen)
- **R-UI-2** UI statisch von SD, Core2-Display zeigt Status/Störung lokal
- **R-UI-3** Login vor Update/Aktionen; Zugangsweg offen (T-011)
- **R-UI-4** Offline-Knoten mit Zeitstempel, Browser und Node-RED gleiche Sicht

## Familie 4 — Firmware-Update

- **R-UPD-1** HTTP-Bereitstellung ab SD + Standard-OTA übers Mesh
- **R-UPD-2** A/B + Bootloader-Verify: kein Brick, automatischer Rollback
- **R-UPD-3** Einzel- und Gruppen-Update aus UI oder Node-RED, nur mit Bestätigung nach Login
- **R-UPD-4** Update-Erkennbarkeit: kein Update ohne Anstoß von außen (Non-Goal-Grenze)

## Familie 5 — Betrieb/Ausfall

- **R-OPS-1** Node-RED-Ausfall: Gateway läuft weiter, puffert asynchron, liefert nach
- **R-OPS-2** Knoten ausgefallen: Offline-Anzeige, Werte mit Zeitstempel, Störung auf Display
- **R-OPS-3** Gateway-Ausfall: Mesh betriebsfähig für Knotentausch, Root-Heilung ohne Gateway (R-MESH-2 + User-Guide-Heilung)

## Familie 6 — Sicherheitsrahmen

- **R-SEC-1** LAN-Begrenzung: keine Cloud-/Internetanbindung (Non-Goal)
- **R-SEC-2** Zugang: Login am Gateway + Checksummen; Signierung/Secure Boot/TLS bewusst raus (bewusster Verzicht)
- **R-SEC-3** Mesh-Sicherheit: WPA2-PSK im Mesh, AES128 optional — Einordnung als Entscheidungs-REQ (T-015)

## Familie 7 — Redundanz (optional, M8)

- **R-RED-1** nur bei ADR-0002-Freigabe; Normpunkte stehen im ADR (Übernahme <30 s, kein Sync, einvernehmliche Ausfallsicht)
- Anmerkung: als REQ nur anlegbar, wenn Umfangentscheidung positiv — sonst als Nicht-Umfang markiert

## Querbezug Hardware (kein REQ, Randbedingung)

- Chip-Matrix S2/WROOM-32/C3/C6 alle offiziell unterstützt (Toolchain-Check);
  Flash-/Partition-Grenze T-005 beeinflusst nur R-UPD-2.

## Offene Punkte, die REQ-Inhalt noch bremsen

| Punkt | Wo |
|---|---|
| Sync/Async-Kriterium | T-013 → R-DATA-3 |
| Timeout/Puffer-Größen | T-012 → R-DATA-2/4 |
| Auth-Weg | T-011 → R-UI-3 |
| AES128-Entscheidung | T-015 → R-SEC-3 |
| ADR-0002-Umfang | T-016 → Familie 7 |
| PlatformIO-Entscheidung | T-023 (neu) → Toolchain-Rahmen |
