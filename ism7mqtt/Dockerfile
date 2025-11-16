ARG BUILD_FROM

# Build ism7mqtt mit TLS-Fixes aus GitHub
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build-env
ARG TARGETARCH
WORKDIR /app

# Clone und baue ism7mqtt mit TLS-Fixes
RUN apt-get update && apt-get install -y git && \
    git clone https://github.com/a-buntjer/ism7mqtt.git /src && \
    cd /src && \
    if [ "$TARGETARCH" = "amd64" ]; then RID=linux-musl-x64; \
    elif [ "$TARGETARCH" = "arm64" ]; then RID=linux-musl-arm64; \
    elif [ "$TARGETARCH" = "arm" ]; then RID=linux-musl-arm; \
    else RID=linux-musl-x64; fi && \
    dotnet publish -c Release -o /app/out -r $RID --sc -nowarn:IL2026,IL2104 src/ism7mqtt/ism7mqtt.csproj && \
    dotnet publish -c Release -o /app/out -r $RID --sc -nowarn:IL2026,IL2104 src/ism7config/ism7config.csproj

# Finale Image
FROM $BUILD_FROM

COPY run.sh /
RUN \
    apk add \
        icu-libs \
        moreutils \
        mosquitto-clients \
    && chmod a+x /run.sh

COPY --from=build-env /app/out /app/

CMD [ "/run.sh" ]
