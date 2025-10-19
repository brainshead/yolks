# ==============================================
# Ubuntu 24.04 Wine Yolk for SteamCMD
# ==============================================
FROM ubuntu:24.04 AS builder

ENV DEBIAN_FRONTEND=noninteractive

# Install build dependencies + 32-bit libs
RUN dpkg --add-architecture i386 \
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
       software-properties-common ca-certificates curl wget git tar unzip xvfb \
       cabextract gnupg libvulkan1 python3 python3-pip winbind \
       iproute2 numactl libc6:i386 libncurses5:i386 libncurses6:i386 \
       libsdl2-2.0-0 libsdl2-2.0-0:i386 libstdc++6:i386 zlib1g:i386 \
       wine32 locales \
    && locale-gen en_US.UTF-8 \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

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
# Final image
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
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8

# Copy Wine and tools from builder
COPY --from=builder /usr/bin/wine /usr/bin/wine
COPY --from=builder /usr/local/bin/winetricks /usr/local/bin/winetricks
COPY --from=builder /usr/local/bin/rcon /usr/local/bin/rcon
COPY --from=builder /usr/lib /usr/lib
COPY --from=builder /usr/share/locale /usr/share/locale

# Install runtime tools
RUN apt-get update -y && apt-get install -y iproute2 numactl xvfb curl wget tar unzip && apt-get clean && rm -rf /var/lib/apt/lists/*

# User setup
RUN useradd -m -d /home/container container
USER container
WORKDIR /home/container

# Entrypoint
COPY ./../entrypoint.sh /entrypoint.sh
CMD ["/bin/bash", "/entrypoint.sh"]
