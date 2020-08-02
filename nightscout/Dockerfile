ARG BUILD_FROM=hassioaddons/ubuntu-base:5.2.1
FROM $BUILD_FROM

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN mkdir -p /opt/app
ADD . /opt
WORKDIR /opt/app

ARG BUILD_ARCH=amd64
RUN \
    URL="http://archive.ubuntu.com/ubuntu/" \
    && if [ "${BUILD_ARCH}" = "armv7" ] \
        || [ "${BUILD_ARCH}" = "aarch64" ]; then \
        URL="http://ports.ubuntu.com/ubuntu-ports/"; \
    fi \
    && echo "deb ${URL} xenial universe" \
        > /etc/apt/sources.list.d/xenial-universe.list \
    \
    && echo "deb ${URL} xenial main" \
        > /etc/apt/sources.list.d/xenial-main.list \
    \
    && curl -sL https://deb.nodesource.com/setup_10.x | bash - \
    && apt-get update \
    \
    && apt list -a nodejs npm \
    && apt-get install -y --no-install-recommends \
        mongodb \
        nodejs=10.22.0-1nodesource1 \
        git \
        webpack \
        build-essential \
        python2.7 \
        nginx \
        libnginx-mod-http-lua

RUN git clone git://github.com/nightscout/cgm-remote-monitor.git /opt/app

RUN npm install \
    && npm run postinstall \
    && npm run env \
    && npm audit fix

VOLUME ["/data/db"]

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="NightScout" \
    io.hass.description="Nightscout acts as a web-based CGM (Continuous Glucose Montinor) to allow multiple caregivers to remotely view a patients glucose data in realtime." \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Marcio Granzotto <oscensores@gmail.com>" \
    org.opencontainers.image.title="NightScout" \
    org.opencontainers.image.description="Nightscout acts as a web-based CGM (Continuous Glucose Montinor) to allow multiple caregivers to remotely view a patients glucose data in realtime." \
    org.opencontainers.image.vendor="Marcio Granzotto" \
    org.opencontainers.image.authors="Marcio Granzotto <oscensores@gmail.com>" \
    org.opencontainers.image.licenses="GNUv3" \
    org.opencontainers.image.source="https://github.com/marciogranzotto/addon-nightscout" \
    org.opencontainers.image.documentation="https://github.com/marciogranzotto/addon-nightscout/blob/master/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}