---
id: REQ-0025
title: "Mesh-Sicherheit: WPA2-PSK Pflicht, AES128 deaktiviert"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001]
---

# Motivation

project.md §4 und die Kanon-Entscheidung T-015 (2026-10-25, ADR-0001-Kontext):
die Mesh-Funkstrecke ist die physisch exponierteste Schicht des Systems —
Zugangssicherung ist dort die einzige Schutzebene.

# Requirement

1. Das Mesh-Netz erfordert als Zugang **WPA2-PSK** (von ESP-Mesh-Lite für
   SoftAP erzwungen); die Schlüssel je Knoten kommen aus der
   Webserver-Einrichtung (REQ-0010, persistenter Speicher).
2. **AES128 als zusätzliche Mesh-Datenverschlüsselung**
   (`esp_mesh_lite_aes_set_key`, Toolchain-Check Befund 7, optional) wird
   **nicht eingesetzt**. WPA2-PSK ist die einzige Link-Schutzschicht des
   Meshs.

# Rationale

Der Mesh-Zugang ist durch PSK verriegelt; darüber entscheidet der
LAN-Vertrauensansatz (REQ-0024) über die restliche Schutztiefe. Eine
zusätzliche AES128-Schicht über der Link-Verschlüsselung wäre doppelte
Absicherung gegen dieselbe Annahme und nur mit Key-Pflege über elf Knoten
bezahlbar.

# Verification

Konfigurationsprüfung: Join ohne PSK verweigert; Join und Betrieb unter
gesetzter Standardkonfiguration über die Zielplattformen hinweg.

# Human approval
Approver: pending  
Approved version/date: pending
