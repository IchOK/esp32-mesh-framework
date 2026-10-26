---
id: REQ-0016
title: "Update-Bereitstellung per HTTP ab SD-Card mit Standard-OTA über die Mesh-Strecke"
version: 0.1.0
status: Draft
created_at: 2026-10-26
implemented_in:
  functional_specs: []
  adrs: [ADR-0001, ADR-0004]
---

# Motivation

project.md §4 (Firmware-Transportpfad) und §5: Images liegen auf der SD-Card
des Gateways und werden per HTTP bereitgestellt; der Knoten zieht per
Standard-OTA über die Mesh-IP (Multi-Hop, ADR-0001).

# Requirement

1. Das Gateway stellt Firmware-Images, die **vorab auf die SD-Card**
   (Namensschema/Verzeichnisablage → project.md §8, offen) abgelegt wurden,
   über **HTTP** bereit.
2. Die Anwendung auf dem Knoten erfolgt mit der **Standard-OTA-Mechanik**
   (esp_ota/Update-Library) über die Mesh-IP — kein proprietärer
   Updatetransport, kein Cloud-/Internetbezug.
3. Der Update-Pfad funktioniert über **alle unterstützten Chipplattformen**
   (S2/WROOM/C3/C6) und **jeden Hop** des Meshs, also auch für Knoten,
   die nur über benachbarte Knoten erreichbar sind.

# Rationale

HTTP ab SD ist die einzige vom Gateway her gebaute Bereitstellung ohne
Cloud-Schiene (project.md §6). Standard-OTA statt Eigenbau hält die Knoten-
Komponenten auf dem offiziellen Supportpfad und macht Rollback (REQ-0017)
vergleichbar mit ESP-Standardverhalten.

# Verification

M2-Integrationstest: Image auf die SD, Update eines direkt erreichbaren
Knotens, Update eines Multi-Hop-Knoten — jeweils mit OTA-Erfolgsmeldung und
neuer Version im Status. Negativtest: kein HTTP-Zugriff aufs Image außerhalb
des Freabgabepfads.

# Human approval
Approver: pending  
Approved version/date: pending
