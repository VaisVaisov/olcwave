FROM golang:1.26 AS builder

RUN apt-get update && \
    apt-get install -y git && \
    rm -rf /var/lib/apt/lists/*

RUN git clone --depth=1 https://github.com/VaisVaisov/yptun.git /build/yptun
RUN git clone --depth=1 https://github.com/VaisVaisov/j.git /build/j

# YPtun vendors the olcrtc core it actually speaks, so the panel builds the
# server from exactly the same source (and gets every provider it supports).
WORKDIR /build/yptun/olcrtc

# Our fork of the jitsi library carries the config.js User-Agent fix.
RUN go mod edit -replace github.com/zarazaex69/j=/build/j \
    && CGO_ENABLED=0 go build -buildvcs=false -o /olcrtc ./cmd/olcrtc/


WORKDIR /build/proxy

COPY proxy.go go.mod go.sum ./

RUN CGO_ENABLED=0 GOOS=linux \
    go build \
    -trimpath \
    -ldflags="-s -w" \
    -o /proxy \
    proxy.go


# =======================================================
# Runtime
# =======================================================

FROM alpine:3.20

RUN apk add --no-cache \
    ca-certificates \
    bash

WORKDIR /app

COPY --from=builder /olcrtc ./olcrtc
COPY --from=builder /proxy ./proxy

COPY entrypoint.sh .

RUN chmod +x \
    /app/olcrtc \
    /app/proxy \
    /app/entrypoint.sh

RUN mkdir -p \
    /var/lib/olcwave \
    /tmp/olcwave \
    /tmp/olcwave/data

COPY --from=builder /build/yptun/olcrtc/internal/names/data /tmp/olcwave/data

ENV PROXY_BIND_ADDR=127.0.0.1
ENV PROXY_PORT=1080
ENV STATS_FILE=/tmp/olcwave/stats.json

ENTRYPOINT ["/app/entrypoint.sh"]
