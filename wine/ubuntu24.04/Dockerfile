# ==============================================
# Ubuntu 24.04 Wine Yolk with SteamCMD & Winetricks
# ==============================================
FROM ubuntu:24.04 AS builder

LABEL author="brainshead"
LABEL org.opencontainers.image.source="https://github.com/brainshead/yolks"
LABEL org.opencontainers.image.description="Ubuntu 24.04 base yolk with Wine, SteamCMD & Winetricks support"

ENV DEBIAN_FRONTEND=noninteractive

# Add 32-bit architecture and install required packages
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
       iproute2 \
       numactl \
       locales \
       libc6:i386 \
       libncurses6:i386 \
       libstdc++6:i386 \
       zlib1g:i386 \
       lib32z1 \
       lib32ncurses6 \
       lib32stdc++6 \
       wine32 \
       steamcmd \
    && locale-gen en_US.UTF-8 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install lightweight tools
RUN cd /tmp \
    && curl -sSL https://github.com/gorcon/rcon-cli/releases/download/v0.10.3/rcon-0.10.3-amd64_linux.tar.gz -o rcon.tar.gz \
    && tar -xzf rcon.tar.gz \
    && mv rcon-0.10.3-amd64_linux/rcon /usr/local/bin/rcon \
    && chmod +x /usr/local/bin/rcon \
    && rm -rf /tmp/*

# Install Winetricks
RUN wget -q -O /usr/local/bin/winetricks https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks \
    && chmod +x /usr/local/bin/winetricks

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

# Copy Wine and tools from builder
COPY --from=builder /usr/bin/wine /usr/bin/wine
COPY --from=builder /usr/bin/steamcmd /usr/bin/steamcmd
COPY --from=builder /usr/local/bin/winetricks /usr/local/bin/winetricks
COPY --from=builder /usr/local/bin/rcon /usr/local/bin/rcon
COPY --from=builder /usr/lib /usr/lib

# Create container user
RUN useradd -m -d /home/container container
USER container
WORKDIR /home/container

# Initialize Wine prefix
RUN wineboot --init || true

# Entrypoint
COPY            ./../entrypoint.sh /entrypoint.sh
CMD             [ "/bin/bash", "/entrypoint.sh" ]
