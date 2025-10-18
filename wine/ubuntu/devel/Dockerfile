# =============================================
# Ubuntu 24.04 (Noble) + SteamCMD + Wine (devel)
# IW4x / Proton-compatible yolk
# =============================================
FROM ubuntu:24.04

LABEL author="Brainshead" \
      maintainer="Brainshead <brainshead010@example.com>" \
      org.opencontainers.image.source="https://github.com/brainshead/yolks" \
      org.opencontainers.image.title="wine-ubuntu24.04"

ENV DEBIAN_FRONTEND=noninteractive
ENV USER=container
ENV HOME=/home/container
ENV WINEPREFIX=/home/container/.wine
ENV WINEDEBUG=-all

# ---------------------------------------------
# Base system setup
# ---------------------------------------------
RUN dpkg --add-architecture i386 && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
    locales software-properties-common wget curl unzip tar xz-utils ca-certificates gnupg \
    cabextract xvfb gosu net-tools iproute2 \
    wine64 wine32 winbind \
    winetricks \
    python3 \
    lib32gcc-s1 lib32stdc++6 libncurses5:i386 libtinfo5:i386 \
    && locale-gen en_US.UTF-8 && \
    update-locale LANG=en_US.UTF-8 && \
    rm -rf /var/lib/apt/lists/*

# ---------------------------------------------
# Install SteamCMD
# ---------------------------------------------
RUN mkdir -p /home/container/steamcmd && \
    cd /home/container/steamcmd && \
    curl -sSL -o steamcmd_linux.tar.gz https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz && \
    tar -xzf steamcmd_linux.tar.gz && rm steamcmd_linux.tar.gz

# ---------------------------------------------
# Add user
# ---------------------------------------------
RUN useradd -m -d /home/container container && \
    chown -R container:container /home/container

USER container
WORKDIR /home/container

# ---------------------------------------------
# Default environment variables for Pterodactyl
# ---------------------------------------------
ENV WINEPREFIX=/home/container/.wine
ENV WINEDEBUG=-all
ENV DISPLAY=:0

# ---------------------------------------------
# Entrypoint
# ---------------------------------------------
COPY ./entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
CMD ["bash"]
