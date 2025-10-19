# ==============================================
# Ubuntu 24.04 (Noble Numbat) Wine Yolk
# ==============================================
FROM ubuntu:24.04 AS builder

LABEL author="brainshead" maintainer="brainshead"
LABEL org.opencontainers.image.source="https://github.com/brainshead/yolks"
LABEL org.opencontainers.image.description="Ubuntu 24.04 base yolk with Wine for Pterodactyl"

ENV DEBIAN_FRONTEND=noninteractive

# Install build dependencies
RUN dpkg --add-architecture i386 \
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
       software-properties-common \
       ca-certificates \
       curl \
       wget \
       git \
       tar \
       unzip \
       xvfb \
       cabextract \
       gnupg \
       libvulkan1 \
       python3 \
       python3-pip \
       winbind \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install Wine (Ubuntu repo temporarily)
RUN apt-get update -y \
    && apt-get install -y wine \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install lightweight tools
RUN cd /tmp \
    && curl -sSL https://github.com/gorcon/rcon-cli/releases/download/v0.10.3/rcon-0.10.3-amd64_linux.tar.gz -o rcon.tar.gz \
    && tar -xzf rcon.tar.gz \
    && mv rcon-0.10.3-amd64_linux/rcon /usr/local/bin/rcon \
    && chmod +x /usr/local/bin/rcon \
    && rm -rf /tmp/*

# Set up Winetricks
RUN wget -q -O /usr/local/bin/winetricks https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks \
    && chmod +x /usr/local/bin/winetricks

# -------------------
# Final lightweight image
# -------------------
FROM ubuntu:24.04

ENV HOME=/home/container
ENV WINEPREFIX=/home/container/.wine
ENV WINEDLLOVERRIDES="mscoree,mshtml="
ENV DISPLAY=:0
ENV DISPLAY_WIDTH=1024
ENV DISPLAY_HEIGHT=768
ENV DISPLAY_DEPTH=16
ENV AUTO_UPDATE=1
ENV XVFB=1

# Copy Wine and tools from builder
COPY --from=builder /usr/bin/wine /usr/bin/wine
COPY --from=builder /usr/local/bin/winetricks /usr/local/bin/winetricks
COPY --from=builder /usr/local/bin/rcon /usr/local/bin/rcon
COPY --from=builder /usr/lib /usr/lib

# User setup
RUN useradd -m -d /home/container container
USER container
WORKDIR /home/container

# Entrypoint (single file in wine root)
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
