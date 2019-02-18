# Run Libreoffice in a container

FROM alpine:edge
LABEL maintainer "bghost bghost@bghost.xyz"

ARG GID
ARG ID

RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" \
        >>/etc/apk/repositories \
    && apk upgrade --no-cache \
    && apk add libreoffice ttf-dejavu \
    && addgroup -S libreoffice -g $GID \
    && adduser -S libreoffice -G libreoffice -u $ID \
    && addgroup libreoffice audio \
    && addgroup libreoffice video \
    && mkdir -p /home/libreoffice/Documents \
    && chown -R libreoffice:libreoffice /home/libreoffice

WORKDIR /home/libreoffice/Documents

# Run as non privileged user
USER libreoffice

ENTRYPOINT ["/usr/bin/libreoffice"]

