# Vorschlag für ADR-0004 (Build-Umgebung) — noch kein normatives Artefakt.
# Baubar nur auf einer glibc-Maschine mit Docker/Podman (nicht in diesem
# Alpine/musl-Container).
#
# Bild enthält beides, damit Worker direkt bauen können:
#   - ESP-IDF 5.4 (Pin gem. ADR-0004-Kandidat) für alle vier Chips (S2/WROOM/C3/C6)
#   - OpenCode als Agent-Laufzeit
#
# Basis: Ubuntu 22.04 arm64 (glibc). Espressif liefert seine vorkompilierten
# Toolchains als linux-amd64- und linux-arm64-Pakete, aber nie für musl —
# Alpine-Families scheiden als Build-Platz grundsätzlich aus.

FROM --platform=linux/arm64 ubuntu:22.04

ARG DEBIAN_FRONTEND=noninteractive
ARG IDF_VERSION=v5.4

RUN apt-get update && apt-get install -y --no-install-recommends \
        git cmake ninja-build ccache \
        python3 python3-venv python3-pip \
        libffi-dev libssl-dev \
        wget curl ca-certificates unzip \
        libglib2.0-0 libgtk-3-0 libnotify4 libnss3 libatk-bridge2.0-0 \
        libdrm2 libxkbcommon0 libgbm1 libasound2 \
        libusb-1.0-0 \
        locales \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# --- ESP-IDF ------------------------------------------------------------
# install.sh holt beim Bauen exakt die zur Pin-Stufe gehörenden Toolchains.
RUN useradd -m -u 1000 build \
    && git clone --depth 1 --branch ${IDF_VERSION} \
        https://github.com/espressif/esp-idf.git /opt/esp-idf \
    && chown -R build:build /opt/esp-idf \
    && su build -c "/opt/esp-idf/install.sh all"

# Umgebungs-Einstiegspunkt für jede Shell: Quelle der IDF-Variablen.
ENV IDF_PATH=/opt/esp-idf
ENV BASH_ENV=/opt/esp-idf/export.sh
ENV ENV=/opt/esp-idf/export.sh

# --- OpenCode -----------------------------------------------------------
# Standalone-Binary (Node-enbundenes Paket). Plattform je Ziel anpassen.
ARG OPENCODE_VERSION
RUN if [ -n "${OPENCODE_VERSION}" ]; then \
      wget -q "https://github.com/sst/opencode/releases/download/v${OPENCODE_VERSION}/opencode-linux-arm64.tar.gz" -O /tmp/oc.tar.gz \
      && tar -xzf /tmp/oc.tar.gz -C /usr/local/bin \
      && rm /tmp/oc.tar.gz \
      && opencode --version; \
    fi

# --- Git-Basics für Agentik ----------------------------------------------
RUN git config --system --add safe.directory '*' \
    && printf '\n[credential]\n    helper = \n' >> /etc/gitconfig

WORKDIR /workspace
USER build
CMD ["opencode"]
