# =============================================
# Ubuntu 24.04 (Noble) + SteamCMD + Wine (devel)
# =============================================
FROM ubuntu:24.04

LABEL author="Brainshead"      

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

RUN             cd /tmp/ \
                && curl -sSL https://github.com/gorcon/rcon-cli/releases/download/v0.10.3/rcon-0.10.3-amd64_linux.tar.gz > rcon.tar.gz \
                && tar xvf rcon.tar.gz \
                && mv rcon-0.10.3-amd64_linux/rcon /usr/local/bin/
				
# Install wine and with recommends
RUN             mkdir -pm755 /etc/apt/keyrings
RUN             wget -O /etc/apt/keyrings/winehq-archive.key https://dl.winehq.org/wine-builds/winehq.key
RUN             wget -NP /etc/apt/sources.list.d/ https://dl.winehq.org/wine-builds/debian/dists/bookworm/winehq-bookworm.sources
RUN             apt update
RUN             apt install --install-recommends winehq-stable cabextract -y

# Set up Winetricks
RUN	            wget -q -O /usr/sbin/winetricks https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks \
                && chmod +x /usr/sbin/winetricks

# ---------------------------------------------
# Default environment variables for Pterodactyl
# ---------------------------------------------
ENV             HOME=/home/container
ENV             WINEPREFIX=/home/container/.wine
ENV             WINEDLLOVERRIDES="mscoree,mshtml="
ENV             DISPLAY=:0
ENV             DISPLAY_WIDTH=1024
ENV             DISPLAY_HEIGHT=768
ENV             DISPLAY_DEPTH=16
ENV             AUTO_UPDATE=1
ENV             XVFB=1

				


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
# Entrypoint
# ---------------------------------------------
COPY ./entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
CMD ["bash"]
