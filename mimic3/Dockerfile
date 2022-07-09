# https://developers.home-assistant.io/docs/add-ons/configuration#add-on-dockerfile
ARG BUILD_FROM
FROM $BUILD_FROM

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Execute during the build of the image
ARG TEMPIO_VERSION BUILD_ARCH
RUN \
    curl -sSLf -o /usr/bin/tempio \
    "https://github.com/home-assistant/tempio/releases/download/${TEMPIO_VERSION}/tempio_${BUILD_ARCH}"

# Copy root filesystem
COPY rootfs /


# Copy Python requirements file
COPY requirements.txt /tmp/

# Setup base
ARG BUILD_ARCH=amd64
# hadolint ignore=DL3003
RUN \
    apk add --no-cache --virtual .build-dependencies \
        build-base=0.5-r2 \
        libffi-dev=3.4.2-r1 \
        openssl-dev=1.1.1o-r0 \
        py3-wheel=0.37.1-r0 \
        python3-dev=3.10.4-r0 \
    \
    && apk add --no-cache \
        py3-bcrypt=3.2.2-r0 \
        py3-cryptography=3.4.8-r1 \
        py3-pip=22.1.1-r0 \
        python3=3.10.4-r0 \
    \
    && pip install \
        --no-cache-dir \
        --prefer-binary \
        --find-links "https://wheels.home-assistant.io/alpine-3.16/${BUILD_ARCH}/" \
        -r /tmp/requirements.txt \
    \
    && apk del --no-cache --purge .build-dependencies

USER mimic3

EXPOSE 59125

ENTRYPOINT ["/home/mimic3/app/.venv/bin/python3", "-m", "mimic3_http"]
