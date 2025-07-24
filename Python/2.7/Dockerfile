FROM        --platform=$TARGETOS/$TARGETARCH python:2.7-slim

LABEL       author="Michael Parker" maintainer="support@cybrancee.com"

# Remove default sources.list and add archive repositories for Debian Buster
RUN rm /etc/apt/sources.list

RUN echo "deb http://archive.debian.org/debian-security buster/updates main" >> /etc/apt/sources.list.d/buster.list

RUN echo "deb http://archive.debian.org/debian buster main" >> /etc/apt/sources.list.d/buster.list

RUN         apt-get update \
            && apt -y install git gcc g++ ca-certificates dnsutils curl iproute2 ffmpeg procps tini \
            && useradd -m -d /home/container container

RUN python -m pip install --upgrade pip

USER        container
ENV         USER=container HOME=/home/container
WORKDIR     /home/container

STOPSIGNAL SIGINT

COPY        --chown=container:container ./../entrypoint.sh /entrypoint.sh
RUN         chmod +x /entrypoint.sh
ENTRYPOINT    ["/usr/bin/tini", "-g", "--"]
CMD         ["/entrypoint.sh"]