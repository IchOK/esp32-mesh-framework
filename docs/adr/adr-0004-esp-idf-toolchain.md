---
id: ADR-0004
title: "Firmware-Toolchain: ESP-IDF pur mit ESP-Mesh-Lite als Component"
status: Draft
version: 0.1.0
date: 2026-10-25
---

# Context

Beide Firmware-Repos (gateway, nodes) brauchen eine einheitliche Toolchain.
Der Toolchain-Check (`docs/poc/toolchain-check.md`) ergab:

- ESP-Mesh-Lite v1.0.1 ist offiziell als ESP-IDF-Component gepflegt,
  Build-Anforderungen schließen an registrierte IDF-Versionen (5.x) an.
- Kein offizielles PlatformIO-/Arduino-Paket; Arduino-Pfade nur als
  IDF-Component-Aufbau oder nicht offizieller Third-Party-Wrapper.
- Die Component patched IDF beim Bauen (IoT-Bridge); Espressif empfiehlt eine
  eigene, gepinnte IDF-Umgebung.

# Decision

**Wir bauen beide Firmware-Repos als reine ESP-IDF-Projekte:**

1. Fest gepinnte **ESP-IDF 5.3 oder 5.4** (Entscheidung pro Repo identisch) und
   **espressif/mesh_lite v1.0.1** als registrierte Component.
2. Keine Arduino- oder PlatformIO-Frameworks; PlatformIO darf nur als
   **IDE/Ablage-Drehscheibe** (`platform- espressif` als reiner IDF-Installer)
   genutzt werden, wenn es den Build nicht verändert.
3. Versionsbindung liegt pro Repo in einer dokumentierten Toolchain-Datei
   (z. B. `toolchain.lock`/README), Pflege analog zum Wartungsvorbehalt aus
   ADR-0001.
4. project.md-Einordnung „Arduino/PlatformIO“ ist damit auf ESP-IDF pur gehoben.

# Alternatives

| Option | Ergebnis | Begründung |
|---|---|---|
| **ESP-IDF pur (gewählt)** | ✅ | Offizieller Support, Patch-Kontrolle, Versionsbindung sichtbar |
| Arduino als IDF-Component | ❌ | Zwei Versionsketten zu pflegen, Framework tut dem Mesh-Component-Bau nichts Gutes |
| Third-Party-Wrapper | ❌ | Nicht offiziell gepflegt, Lizenzen/Gewähr unsicher |

# Consequences

**Positiv:**

- Deckung mit dem offiziellen Supportpfad von ADR-0001 (v1.0.x + IDF 5.x).
- Ein Build-Muster für beide Firmware-Repos, Worker-Worktrees einheitlich.

**Zu managen:**

- Build-Umgebung braucht saubere Verankerung (Docker/venv-Anleitung je Repo
  empfohlen, sonst reproduzieren sich Fehler nicht).
- Erfahrene Arduino-Entwickler im Projekt müssen sich auf IDF-Struktur
  (CMake, components) umstellen — Lernkosten, aber nur einmal.

# Verification / Open questions

- [ ] „Hello mesh_lite“ Build je Plattformfamilie (S2, ESP32, C3, C6) in
      Versionierungsform — Teil von T-004.

# Human approval

Approver: pending  
Approved version/date: pending
