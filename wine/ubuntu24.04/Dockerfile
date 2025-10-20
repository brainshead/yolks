# ==============================================
# Ubuntu 24.04 Wine Yolk with SteamCMD & Winetricks
# ==============================================
FROM ubuntu:24.04 AS builder

LABEL author="brainshead"
LABEL org.opencontainers.image.source="https://github.com/brainshead/yolks"
LABEL org.opencontainers.image.description="Ubuntu 24.04 base yolk with Wine, Winetricks & SteamCMD support"

ENV DEBIAN_FRONTEND=noninteractive
ENV LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8

# Core dependencies
RUN dpkg --add-architecture i386 && \
    apt-get update -y && \
    apt-get install -y --no-install-recommends \
        ca-certificates curl wget git tar unzip xvfb cabextract gnupg locales \
        software-properties-common python3 python3-pip winbind iproute2 numactl \
        lib32gcc-s1 lib32stdc++6 libvulkan1 libvulkan1:i386 \
        libc6:i386 libncurses6:i386 libstdc++6:i386 zlib1g:i386 \
        libtinfo6:i386 libsdl2-2.0-0:i386 libasound2-plugins:i386 \
        libpulse0:i386 libopenal1:i386 && \
    locale-gen en_US.UTF-8 && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Wine (stable branch)
RUN mkdir -pm755 /etc/apt/keyrings && \
    wget -O /etc/apt/keyrings/winehq-archive.key https://dl.winehq.org/wine-builds/winehq.key && \
    wget -NP /etc/apt/sources.list.d/ https://dl.winehq.org/wine-builds/ubuntu/dists/noble/winehq-noble.sources && \
    apt-get update && \
    apt-get install -y --install-recommends winehq-stable cabextract && \
    rm -rf /var/lib/apt/lists/*

# Install Winetricks
RUN wget -q -O /usr/local/bin/winetricks https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks && \
    chmod +x /usr/local/bin/winetricks

# Install RCON client
RUN cd /tmp && \
    curl -sSL https://github.com/gorcon/rcon-cli/releases/download/v0.10.3/rcon-0.10.3-amd64_linux.tar.gz -o rcon.tar.gz && \
    tar -xzf rcon.tar.gz && mv rcon-0.10.3-amd64_linux/rcon /usr/local/bin/rcon && \
    chmod +x /usr/local/bin/rcon && rm -rf /tmp/*

# -------------------
# Final lightweight image
# -------------------
FROM ubuntu:24.04

ENV HOME=/home/container
ENV WINEPREFIX=/home/container/.wine
ENV WINEARCH=win32
ENV WINEDLLOVERRIDES="mscoree,mshtml="
ENV DISPLAY=:0
ENV DISPLAY_WIDTH=1024
ENV DISPLAY_HEIGHT=768
ENV DISPLAY_DEPTH=16
ENV AUTO_UPDATE=1
ENV XVFB=1
ENV STEAMCMD_DIR=/home/container/steamcmd
ENV PATH=$PATH:/home/container/steamcmd

# Copy tools from builder
COPY --from=builder /usr /usr
COPY --from=builder /etc/apt/keyrings /etc/apt/keyrings

# Create user
RUN useradd -m -d /home/container container
USER container
WORKDIR /home/container

# Ensure wine prefix exists
RUN wineboot --init || true

COPY ./../entrypoint.sh /entrypoint.sh
CMD ["/bin/bash", "/entrypoint.sh"]
