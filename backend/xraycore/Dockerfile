FROM alpine:3.20

RUN apk add --no-cache \
    ca-certificates \
    bash wget unzip

WORKDIR /app

RUN ARCH=$(uname -m); \
    case "$ARCH" in \
      x86_64) SUFFIX=64 ;; \
      aarch64) SUFFIX=arm64-v8a ;; \
      *) SUFFIX=64 ;; \
    esac; \
    wget -q "https://github.com/XTLS/Xray-core/releases/download/v26.3.27/Xray-linux-${SUFFIX}.zip" -O xray.zip && \
    unzip xray.zip && rm xray.zip

# geoip/geosite are required by the routing config validation.
RUN wget -q -O /app/geoip.dat https://github.com/Loyalsoldier/v2ray-rules-dat/releases/latest/download/geoip.dat && \
    wget -q -O /app/geosite.dat https://github.com/Loyalsoldier/v2ray-rules-dat/releases/latest/download/geosite.dat

COPY entrypoint.sh /app/entrypoint.sh
RUN chmod +x /app/entrypoint.sh

ENTRYPOINT ["bash", "/app/entrypoint.sh"]