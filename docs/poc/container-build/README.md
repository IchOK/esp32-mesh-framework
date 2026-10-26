# Vorschlag: Ein Container mit OpenCode **und** ESP-IDF (zu ADR-0004)

Stand 2026-10-26 — Diskussionsgrundlage, kein normatives Artefakt.

## Warum die Frage neu gestellt werden musste

Der bestehende Container (Alpine 3.24, musl, uid 1000 ohne Docker-Rechte)
kann ESP-IDF **nicht** beherbergen: Espressif verteilt seine vorgebauten
Cross-Compiler ausschließlich als glibc-Pakete (`-linux-gnu`), und zwar auch
für `linux-arm64`. Diese Box ist aarch64, die vier Chips also prinzipiell bedienbar
unterstützbar — aber an musl beißen sich die Toolchains die Zähne aus.

Ein gemeinsamer Container ist aber machbar, wenn das **Bild selbst** glibc
mitbringt. Dann laufen OpenCode (Agent) und IDF (Build) in derselben Umgebung
und Worker können bauen, ohne den Kontext zu verlassen.

## Der Vorschlag

Ein eigenes, versioniertes Image auf Ubuntu-22.04-Basis (arm64, da die
Zielbox aarch64 ist), das enthält:

1. **ESP-IDF `v5.4`** per `git clone --branch v5.4` + `install.sh all` —
   damit stimmen Compiler, Python-Werkzeuge und Components exakt zur Pin.
   Die IDF-Werkezuge liegen unter `/opt/esp-idf`, jede Shell zieht über
   `BASH_ENV`/`ENV` automatisch `export.sh`.
2. **OpenCode** als Standalone-Binary unter `/usr/local/bin` — identisch zur
   jetzigen Orchestrator-Rolle, nur mit echtem Build-Werkzeug direkt darunter.
3. **Git** mit `safe.directory` und leerer Credential-Helferkette — dann
   greifen die bestehenden Token/Helper-Regeln der Agentik.

Das Dockerfile liegt neben dieser Datei (`container-build/Dockerfile`).

## Bauen und nutzen

Auf einer Maschine **mit** Container-Laufzeit (Host oder Build-Node):

```sh
docker build -t esmf-agent:latest \
  --build-arg OPENCODE_VERSION=<aktuell> \
  docs/poc/container-build/
```

Start wie bisher, nur mit eingebettetem Workspace:

```sh
docker run -it --rm -v /workspace:/workspace esmf-agent:latest
```

Danach gilt: `git … && idf.py build` in der Worker-Shell ohne Zusatzarbeit.
`toolchain.lock` (ADR-0004) verankert zusätzlich die exakte IDF-Stufe, auf die
`install.sh` beim Bildbau aufgelöst wurde.

## Was der Vorschlag **nicht** kann und beachtet sein will

- **Der neue Container entsteht nicht von innen heraus.** Innerhalb dieses
  musl-Containers steht kein Docker/Podman — das Bild muss auf einem Host
  erzeugt werden. Die Entscheidung bleibt also, *wo* das Bild gepflegt wird.
- **Flashen/Zielhardware** ist nicht automatisch drin (`esptool` ja, USB und
  reale Geräte bleiben Sache der Host-Anbindung).
- **Build-Identität**: Da OpenCode und Build im selben Bild laufen, ist der
  Bauplatz *notwendigerweise* reproduzierbar. Einmal pro Tag (oder pro
  Versionssprung) `docker build` auf einer festen Build-Node, Image-Tag im
  `toolchain.lock` vermerken — dann sind Worker-Ergebnisse vergleichbar.
- **Rückbau**: Weigert sich der Mensch gegen ein zweites Bild, bleibt Option
  „Build auf Host/Node“ aus ADR-0004.

## Auswirkung auf ADR-0004

Würde dieser Weg gewählt, trüge der ADR eine zusätzliche Entscheidung:

> **Build-Umgebung:** Einheitliches Container-Bild (Ubuntu/glibc) mit
> OpenCode und ESP-IDF als Standard-Arbeitsplatz für alle Worker.
