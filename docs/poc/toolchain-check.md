# Toolchain-Check: ESP-Mesh-Lite (T-003/T-006, extern dokumentiert)

> Stand: 2026-10-25, reine Sekundärrecherche (Web). **Kein Build verifiziert** —
> die Praxistests (T-004, T-007) bleiben auf Hardware liegen. Dieses Dokument ist
> Vorarbeit, keine Akzeptanzquittierung.

## Quellen (abgerufen 2026-10-25)

- github.com/espressif/esp-mesh-lite (release/v1.0, README, User_Guide.md)
- components/espressif/mesh_lite SUPPORT_POLICY.md
- components.espressif.com: `espressif/mesh_lite` v1.0.1
- github.com/espressif/esp-mesh-lite Issue #199 (IDF v6)
- github.com/andrewflash/esp-mesh-lite-arduino (Third-Party-Wrapper)

## Kernbefunde

| # | Befund | Konsequenz |
|---|---|---|
| 1 | **Alle vier Projektplattformen unterstützt**: ESP32 (=WROOM-32), C3, S2 in Matrix ab IDF 5.0; C6 ab IDF 5.1. (S3/C2/C5/C61 ebenfalls gelistet) | T-004 ist realistisch; Chip-Matrix steht der ADR-0001-Entscheidung nicht entgegen |
| 2 | **IDF-Bindung**: v1.0 nur **IDF 5.x**; **kein IDF v6** für v1.0, IDF-6-Support nur für v2.x (ab ~Mai 2026 angekündigt) | Versionierung fixiert auf **IDF 5.3/5.4**; keine IDF-6-Upgrade-Pfade für v1.0 |
| 3 | **Maintenance Mode bestätigt** (SUPPORT_POLICY): nur Security-/Kreuzbugfixes, Weiterentwicklung wandert in privates Repo | Vorbehalt aus ADR-0001-Freigabe ist berechtigt; Projekt bleibt bei v1.0 und pflegt Versionsbindung selbst (T-006-Aufgabe abgehakt) |
| 4 | **Kein offizielles PlatformIO-/Arduino-Paket**: Registry-Komponente `espressif/mesh_lite` (IDF Component Registry), Third-Party-Arduino-Wrapper nur GitHub, Qualität/Alter ungewiss | Empfohlener Pfad: **ESP-IDF pur oder "Arduino als IDF-Component"**, nicht reines Arduino-PlatformIO. Plattform-Entscheidung braucht kleines ADR |
| 5 | **IoT-Bridge-Patch-Hinweis**: Mesh-Lite patched IDF beim Bauen (iot_bridge); Empfehlung separates IDF für Mesh-Projekt | Toolchain-Hygiene: projekteinheitliches pinned IDF, keine geteilte IDF-Umgebung |
| 6 | **Grenzwerte**: max. 1–15 Layer, 1–10 Downstream-Kinder je Knoten; Performance ~8–12 ms/Hop, Provisioning <60 s, Repair <50 s | Anker für REQ-Messgrößen (Ausfalldauer, Latenz); ~10 Knoten comfortably, Ausbau Richtung 50+ dokumentiert getestet |
| 7 | **Sicherheit**: WPA2-PSK erzwungen für SoftAP, optional AES128 für Mesh-Daten (`esp_mesh_lite_aes_set_key`) | Input für T-015 (Mesh-Sicherheitsniveau) |
| 8 | **Root-Node-Einzigartigkeit** im Modell (Normalfall genau ein Root); Root-Ausfall heilt automatisch über zweiten Layer | Deckt sich mit ADR-0002-Modell (Standby = normaler Knoten) und ADR-0001 Gateway-Ausfall-Pfad |

## Empfehlungen für die anstehenden Schritte

1. **T-003:** Versionsbindung dokumentieren: `mesh_lite v1.0.1` + IDF 5.3 oder 5.4, pro Plattform. Baus anstehend.
2. **PlatformIO-Entscheid (neues kleines ADR, T-023):** Pfadwahl IDF pur vs. Arduino-auf-IDF vs. Third-Party-Wrapper — betrifft beide Firmware-Repos.
3. **REQ-Formulierung:** Performance-Ankers aus Befund 6 können als Messgrößen-Grundwert in REQ/FSPEC (mit Vorbehalt Feldtest) dienen.
4. **T-015 (Mesh-Sicherheit):** Befund 7 als Basis; Grundannahme "LAN + Login + WPA2-PSK im Mesh" bleibt, AES128 optional.

## Offene Verifikationspunkte (Hardware-Stufe)

- [ ] Build der vier Plattformen mit v1.0.1 (T-004)
- [ ] Mesh-Aufbau Core2 (Root) + 1–2 Knoten, Hop-/Latenz-/Repair-Prüfung (T-007)
- [ ] OTA über Mesh unter v1.0.1 (T-008)
- [ ] Koexistenz Heim-WLAN/Mesh-Kanal (ADR-0001 §Konsequences)
