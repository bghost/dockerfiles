# Run Chromium in a container

# You will want the custom seccomp profile:
# wget https://raw.githubusercontent.com/jfrazelle/dotfiles/master/etc/docker/seccomp/chrome.json -O ~/chrome.json

FROM alpine:edge
LABEL maintainer "bghost bghost@bghost.xyz"

RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories \
    && apk upgrade --no-cache \
    && apk add ca-certificates firefox mesa-dri-intel mesa-gl libu2f-host

ARG GID
ARG PLUGDEV
ARG ID

# Add firefox user
# Need to explicitly set UID / GID to avoid issues with persistence
RUN addgroup -S firefox -g $GID \
    && addgroup -S plugdev -g $PLUGDEV \
    && adduser -S firefox -G firefox -u $ID \
    && addgroup firefox audio \
    && addgroup firefox video \
    && addgroup firefox plugdev \
    && mkdir -p /home/firefox/Downloads \
    && mkdir -p /home/firefox/.mozilla/firefox \
    && chown -R firefox:firefox /home/firefox

# Run as non privileged user
USER firefox

ENTRYPOINT [ "/bin/sh" ]
CMD [ "dbus-launch", "/usr/bin/firefox" ]
