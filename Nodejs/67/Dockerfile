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
    ffmpeg iproute2 dnsutils iputils-ping procps \
    sqlite3 libsqlite3-dev \
    libnss3 libdbus-1-3 \
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
