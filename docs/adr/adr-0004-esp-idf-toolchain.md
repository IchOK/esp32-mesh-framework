---
id: ADR-0004
title: "Firmware-Toolchain: ESP-IDF pur mit ESP-Mesh-Lite als Component"
status: Approved
version: 0.2.1
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

1. Fest gepinnte **ESP-IDF `v5.4.4`** (Release-Stufe, ohne Build- oder
   Versions-Hash; Standard-Build-Umgebung, Punkt 5) und
   **espressif/mesh_lite v1.0.1** als registrierte Component.
2. Keine Arduino- oder PlatformIO-Frameworks; PlatformIO darf nur als
   **IDE/Ablage-Drehscheibe** (`platform- espressif` als reiner IDF-Installer)
   genutzt werden, wenn es den Build nicht verändert.
3. Versionsbindung liegt pro Repo in einer dokumentierten Toolchain-Datei
   (`toolchain.lock`): IDF-Stufe `v5.4.4` ohne Build-/Hash-Zusatz, Tag des
   Standard-Images und Component-Pins. Pflege
   analog zum Wartungsvorbehalt aus ADR-0001.
4. project.md-Einordnung „Arduino/PlatformIO“ ist damit auf ESP-IDF pur gehoben.
5. **Standard-Build-Umgebung ist der Projekt-Container** (Ubuntu/glibc,
   aarch64; ESP-IDF unter `/opt/esp/idf`, OpenCode in derselben Umgebung).
   Alle Worker-Builds laufen **direkt im betroffenen Firmware-Teilprojekt**
   (Worktree von `esp32-mesh-framework-gateway` bzw.
   `esp32-mesh-framework-nodes`), nicht in temporären Verzeichnissen und
   nicht auf separaten Build-Nodes.

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

- Der Container stellt nur die Toolchain-Basis; `toolchain.lock` je Repo muss
  die exakte Stufe nachziehen, wenn das Bild aktualisiert wird (sonst baut
  der Lock eine Umgebung, die niemand mehr hat).
- Erfahrene Arduino-Entwickler im Projekt müssen sich auf IDF-Struktur
  (CMake, components) umstellen — Lernkosten, aber nur einmal.

# Verification / Open questions

- [x] Grundbuild im Standard-Container verifiziert: `hello_world` erfolgreich
      für `esp32` und `esp32c3` gebaut (2026-10-26, Smoke-Test).
- [ ] „Hello mesh_lite“ Build je Plattformfamilie (S2, ESP32, C3, C6) in
      Versionierungsform — Teil von T-004.
- [ ] Core2-Peripherie (AXP192, Display, SD) unter reinem IDF — T-004,
      bleibt als Risiko-Marker bis zur Verifikation.

# Human approval

Approver: IchOK (Nutzer)  
Approved version/date: v0.2.1 / 2026-10-26
