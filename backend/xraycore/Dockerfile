FROM alpine:3.20

RUN apk add --no-cache \
    ca-certificates \
    bash wget unzip

WORKDIR /app

RUN wget -q https://github.com/XTLS/Xray-core/releases/download/v26.3.27/Xray-linux-arm64-v8a.zip

RUN unzip Xray-linux-arm64-v8a.zip && rm Xray-linux-arm64-v8a.zip

COPY entrypoint.sh /app/entrypoint.sh
RUN chmod +x /app/entrypoint.sh

ENTRYPOINT ["bash", "/app/entrypoint.sh"]