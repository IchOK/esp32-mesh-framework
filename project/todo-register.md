# TODO-Register — lose Fäden, bewusst außerhalb des Freigabe-Prozesses

Nicht jedem offenen Punkt steht ein Dokument oder eine FSPEC zu: Dies ist die
Sammelstelle für Todos, die unterwegs entstehen. **Kein Governance-Objekt, keine
Approval-Kolonie** — Einträge dürfen jederzeit ohne Freigabe entstehen, geändert und
auf `Done`/`Wont fix` gezogen werden. Der Register ist bewusst flach (diese eine
Datei), damit er nie veraltet, und kein Task-Plan hierher zweckt.

Verlinkung: `project.md` (§Offene Entscheidungen) weist auf diese Datei.
Regel für unterwegs: Wenn ein Todo eine formale Änderung an Approved-REQ/FSPEC/ADR
verlangt, wird er als Todo gehalten, bis der Freigabezyklus beginnt.

| ID | Status | Todo | Ursprung/Kontext | Blockiert durch | Trigger |
|---|---|---|---|---|---|
| T-001 | Done | GitHub-Remotes für die vier Repos angelegt und erster Push erfolgt (öffentliche Repos, `esp32-mesh-framework*`) | project.md §9/§8 | — | — |
| T-019 | Done | GitHub-Login per Device-Flow eingerichtet (Account IchOK, gh 2.103.2, Git-Credential-Helper, Konfig /workspace/.tools/ghcfg) | GitHub-Frage 2026-10-25 | — | — |
| T-020 | Open | Lib-Repo nur bei realem Duplikations-Trigger gründen (min. zwei betroffene Projekte, identischer Kern; dann PlatformIO-Lib mit Semver) | Repo-Aufteilung 2026-10-25 | — | erst bei Trigger |
| T-021 | Done | Repo-/Verzeichnis-Aufteilung (Main + 3 Code-Repos als parallele Pfade, Benennung `esp32-mesh-framework*`) umgesetzt | Repo-Aufteilung 2026-10-25 | T-001 (Remote/Push) | — |
| T-022 | Done | ADR-0003 (Cross-Repo-Traceability repo@sha) als Draft angelegt | Repo-Aufteilung 2026-10-25 | — | — |
| T-002 | Open | project.md-Status klären: formal freigeben oder dauerhaft Living Context | Review 2026-10-25 | — | vor REQ-Erstellung |
| T-003 | Open | ESP-Mesh-Lite als Arduino-/PlatformIO-Lib/-Wrapper prüfen (Version, Kompatibilität) | project.md §8 | Toolchain-Umgebung | vor M1-Implementierung |
| T-004 | Open | Build-Verifikation je Chipsatz (S2, WROOM-32, C3, C6): Builds, OTA, WebSocket, Mesh-Funktion | project.md §8 | T-003 | vor REQ-Akzeptanzkriterien |
| T-005 | Open | Flash-Größe je Board gegen A/B-Partitionen prüfen (4-MB-Module S2 Mini, SuperMinis) | project.md §8 | T-004 | vor Update-REQ |
| T-006 | Open | ESP-Mesh-Lite-Wartungszusage (Maintenance Mode) gegenchecken | project.md §8 | — | Vorbehalt der ADR-Freigabe: vor M1 |
| T-007 | Open | PoC Mesh: Routing, Hop-Tiefe, Latenz, Durchsatz, Gateway-Ausfall mit 2–3 Knoten | ADR-0001 Checkliste | Hardware-Aufbau | bis M1 |
| T-008 | Open | PoC Firmware-OTA über Mesh mit realistischer Größe (Abbruch, Wiederanlauf) | ADR-0001 Checkliste | T-003 | bis M2 |
| T-009 | Open | Relais-Anordnung im Zielgebäude planen (Ausbau >10 Knoten) | ADR-0001 Checkliste | T-007 | vor M4 |
| T-010 | Open | USB-Anbindung Core2 als Flash-/Logging-Pfad gegenprüfen | project.md §4/§8 | — | vor erstem M1-Test |
| T-011 | Open | Auth-Zugangsweg Webserver wählen (Basic Auth / Token / Session), Klartext vs. TLS im LAN | project.md §8 | — | vor UI-FSPEC |
| T-012 | Open | Parameter festlegen: Offline-Timeout X, Ringpuffer-Größe/-Dauer, Sync-Antworttimeout | project.md §8 | — | im jeweiligen FSPEC |
| T-013 | Open | Einordnungskriterium „asynchron“ vs. „synchron“ Telegramme formulieren | project.md §5/§8 | — | im Sync/Async-FSPEC |
| T-014 | Open | Image-Verwaltung auf SD (Namensschema, Versionierung, Anzahl) + Meldungsformat Update-Anstoß/Fortschritt | project.md §8 | — | im Update-FSPEC |
| T-015 | Open | Mesh-Sicherheitsniveau einordnen | project.md §8 | — | im Sicherheits-REQ |
| T-016 | Open | ADR-0002 Zweck-Entscheidung: explizit „zurückgestellt bis M7“ statt Draft-Schwebezustand | Review 2026-10-25 | — | nach REQ-Vorlage |
| T-017 | Done | ADR-0002-Klärpunkte falls Umfang gewählt: Übernahme, Sync-Umfang, Übernahmedauer, halbes Netz | ADR-0002 Checkliste | — | entschieden 2026-10-25 (ADR v0.2.0) |
| T-018 | Open | Orchestrierungs-PoC CHECK-01…12 abarbeiten, Evidence nach docs/poc/evidence/ | PROC-0002 §7 | — | vor Implementierungsstart eines FSPECs |
