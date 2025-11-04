FROM --platform=$TARGETOS/$TARGETARCH python:3.11-slim-bookworm

ARG CHROME_VERSION="130.0.6723.116"

LABEL author="Michael Parker" maintainer="support@cybrancee.com"
LABEL org.opencontainers.image.source="https://github.com/Cybrancee/Yolks"
LABEL org.opencontainers.image.licenses=MIT

RUN set -eux; \
    apt-get update; \
    apt-get -y upgrade; \
    apt-get install -y --no-install-recommends \
      sudo wget curl git gcc g++ ca-certificates dnsutils iproute2 ffmpeg procps tini \
      libmariadb3 libmariadb-dev libnss3 libnss3-dev libnss3-tools \
      libgdk-pixbuf2.0-dev libgtk-3-dev libxss-dev xvfb x11-utils x11-xkb-utils \
      tzdata \
      x11vnc xvnc4viewer tigervnc-standalone-server xfce4 \
    ; \
    rm -rf /var/lib/apt/lists/*; \
    useradd -m -d /home/container container; \
    useradd -m -d /home/pteronano pteronano

RUN python -m pip install --upgrade pip

RUN wget --no-verbose -O /tmp/chrome.deb https://dl.google.com/linux/chrome/deb/pool/main/g/google-chrome-stable/google-chrome-stable_${CHROME_VERSION}-1_amd64.deb \
    && apt install -y /tmp/chrome.deb \
    && rm /tmp/chrome.deb

USER container
ENV USER=container HOME=/home/container
WORKDIR /home/container

EXPOSE 5900

RUN echo '#!/bin/bash' > /home/container/start_vnc.sh && \
    echo 'export DISPLAY=:0' >> /home/container/start_vnc.sh && \
    echo 'Xvnc :0 -geometry 1280x800 -depth 24 -SecurityTypes None &' >> /home/container/start_vnc.sh && \
    echo 'sleep 2' >> /home/container/start_vnc.sh && \
    echo 'startxfce4 &' >> /home/container/start_vnc.sh && \
    echo 'google-chrome --no-sandbox --disable-dev-shm-usage &' >> /home/container/start_vnc.sh && \
    chmod +x /home/container/start_vnc.sh

STOPSIGNAL SIGINT

COPY --chown=container:container ./../entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/usr/bin/tini", "-g", "--"]
CMD ["/entrypoint.sh"]
