# ==============================================
# Ubuntu 24.04 (Noble Numbat) Wine Yolk
# ==============================================
FROM ubuntu:24.04

LABEL author="brainshead" maintainer="brainshead"
LABEL org.opencontainers.image.source="https://github.com/brainshead/yolks"
LABEL org.opencontainers.image.description="Ubuntu 24.04 base yolk with Wine (devel) for Pterodactyl"

ENV DEBIAN_FRONTEND=noninteractive

# -------------------------------------------------
# Install dependencies and Wine (devel)
# -------------------------------------------------
RUN apt-get update && apt-get install -y --no-install-recommends \
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
    && dpkg --add-architecture i386 \
    && mkdir -pm755 /etc/apt/keyrings \
    && curl -fsSL https://dl.winehq.org/wine-builds/winehq.key | gpg --dearmor -o /etc/apt/keyrings/winehq-archive.key \
    && echo "deb [signed-by=/etc/apt/keyrings/winehq-archive.key] https://dl.winehq.org/wine-builds/ubuntu/ noble main" > /etc/apt/sources.list.d/winehq.list \
    && apt-get update \
    && apt-get install -y --install-recommends winehq-devel winbind \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# -------------------------------------------------
# RCON CLI
# -------------------------------------------------
RUN cd /tmp/ \
    && curl -sSL https://github.com/gorcon/rcon-cli/releases/download/v0.10.3/rcon-0.10.3-amd64_linux.tar.gz -o rcon.tar.gz \
    && tar -xzf rcon.tar.gz \
    && mv rcon-0.10.3-amd64_linux/rcon /usr/local/bin/rcon \
    && chmod +x /usr/local/bin/rcon \
    && rm -rf /tmp/*

# -------------------------------------------------
# Winetricks
# -------------------------------------------------
RUN wget -q -O /usr/local/bin/winetricks https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks \
    && chmod +x /usr/local/bin/winetricks

# -------------------------------------------------
# Default environment variables
# -------------------------------------------------
ENV HOME=/home/container
ENV WINEPREFIX=/home/container/.wine
ENV WINEDLLOVERRIDES="mscoree,mshtml="
ENV DISPLAY=:0
ENV DISPLAY_WIDTH=1024
ENV DISPLAY_HEIGHT=768
ENV DISPLAY_DEPTH=16
ENV AUTO_UPDATE=1
ENV XVFB=1

# -------------------------------------------------
# Setup user
# -------------------------------------------------
RUN useradd -m -d /home/container container
USER container
WORKDIR /home/container

# -------------------------------------------------
# Entrypoint
# -------------------------------------------------
# Make sure entrypoint.sh is in the same folder as this Dockerfile
# Copy entrypoint from repo root
ARG ENTRYPOINT_PATH
COPY ${ENTRYPOINT_PATH} /entrypoint.sh
RUN chmod +x /entrypoint.sh


ENTRYPOINT ["/entrypoint.sh"]
