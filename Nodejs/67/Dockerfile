FROM --platform=$TARGETOS/$TARGETARCH node:22-bookworm-slim

LABEL author="Michael Parker"
LABEL maintainer="support@cybrancee.com"
LABEL org.opencontainers.image.source="https://github.com/Cybrancee/Yolks"
LABEL org.opencontainers.image.licenses=MIT

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update \
 && apt -y upgrade \
 && apt -y install \
    python3 python3-dev python3-pip \
    curl wget git ca-certificates tzdata \
    build-essential pkg-config \
    cmake \
    ffmpeg iproute2 dnsutils iputils-ping procps \
    sqlite3 libsqlite3-dev \
    libnss3 libdbus-1-3 \
    xvfb \
    tigervnc-standalone-server tigervnc-common netcat-traditional \
    libx11-6 libx11-xcb1 libxcb1 libxcomposite1 \
    libxcursor1 libxdamage1 libxext6 libxfixes3 libxi6 \
    libxrandr2 libxrender1 libxss1 libxtst6 \
    libpango-1.0-0 libcairo2 libasound2 libatk1.0-0 \
    libatk-bridge2.0-0 libcups2 libdrm2 libgbm1 \
    libatspi2.0-0 libxkbcommon0 \
    fonts-liberation libappindicator3-1 xdg-utils \
    tini \
 && rm -rf /var/lib/apt/lists/*

RUN curl -LsSf https://astral.sh/uv/install.sh | sh \
 && ln -s /root/.cargo/bin/uv /usr/local/bin/uv

RUN npm config set registry https://registry.npmjs.org/ --global \
 && npm cache clear --force \
 && npm install -g npm@10 typescript ts-node @types/node \
 && npm install -g corepack@latest \
 && corepack enable \
 && corepack prepare pnpm@latest --activate

RUN useradd -m -d /home/container container

USER container
ENV USER=container \
    HOME=/home/container \
    PATH="/home/container/.local/bin:/usr/local/bin:$PATH"

WORKDIR /home/container

STOPSIGNAL SIGINT

COPY --chown=container:container ./../entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/usr/bin/tini", "-g", "--"]
CMD ["/entrypoint.sh"]
