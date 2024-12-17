FROM snowdreamtech/golang:1.23.3-bookworm AS builder

ENV SSHESAME_VERSION=0.0.39

RUN apt-get update && apt-get -y upgrade && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /workspace
WORKDIR /workspace

RUN set -eux \
    && wget -c https://github.com/jaksi/sshesame/archive/refs/tags/v${SSHESAME_VERSION}.tar.gz -O sshesame-${SSHESAME_VERSION}.tar.gz \
    && tar zxvf sshesame-${SSHESAME_VERSION}.tar.gz \
    && cd sshesame-${SSHESAME_VERSION} \
    && go mod tidy \
    && go build -v -o sshesame 

    
FROM snowdreamtech/debian:12.8.0

# OCI annotations to image
LABEL org.opencontainers.image.authors="Snowdream Tech" \
    org.opencontainers.image.title="Base Image Based On Debian" \
    org.opencontainers.image.description="Docker Images for Base on Debian. (i386,amd64,arm32v5,arm32v7,arm64,mips64le,ppc64le,s390x)" \
    org.opencontainers.image.documentation="https://hub.docker.com/r/snowdreamtech/base" \
    org.opencontainers.image.base.name="snowdreamtech/base:debian" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.source="https://github.com/snowdreamtech/base" \
    org.opencontainers.image.vendor="Snowdream Tech" \
    org.opencontainers.image.version="0.0.39" \
    org.opencontainers.image.url="https://github.com/snowdreamtech/base"

# keep the docker container running
ENV KEEPALIVE=1 \
    SSHESAME_VERSION=0.0.39 

COPY --from=builder /workspace/sshesame-${SSHESAME_VERSION}/sshesame /usr/local/bin/sshesame
COPY --from=builder /workspace/sshesame-${SSHESAME_VERSION}/sshesame.yaml /etc/sshesame/config.yaml

RUN set -eux \
    && cd /etc/sshesame \
    && sed -i 's/listen_address: .*/listen_address: 0.0.0.0:2022/' config.yaml \
    && sed -i 's/file: .*/file: \/etc\/sshesame\/access.log/' config.yaml 

EXPOSE 2022

COPY docker-entrypoint.sh /usr/local/bin/

ENTRYPOINT ["docker-entrypoint.sh"]