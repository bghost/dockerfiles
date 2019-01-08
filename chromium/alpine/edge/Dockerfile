# Run Chromium in a container
FROM alpine:edge
LABEL maintainer "bghost bghost@bghost.xyz"


# Combine the two RUN statements for a smaller image size
# They are separated here to avoid rebuilding the alpine:edge image
# with the testing repo for other containers

# Setup testing repository for alpine and update
RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" \
        >>/etc/apk/repositories \
    && apk upgrade --no-cache


# Chromium specific stuff:
# * Install the required packages
# * Add chromium user and required groups
# * Need to explicitly set UID / GID to avoid issues with persistence

# We can export environment vars on the host:
# export GID=$(id -g)
# export PLUGDEV=$(getent group plugdev | cut -d ':' -f 3)
# export AUDIO=$(getent group audio | cut -d ':' -f 3)
# export ID=$(id -u)
# use the --build-arg flag to pass them to the image at build time
#  --build-arg GID=${GID}
#  --build-arg PLUGDEV=${PLUGDEV}
#  --build-arg AUDIO=${AUDIO}
#  --build-arg ID=${ID}
ARG GID
ARG PLUGDEV
ARG AUDIO
ARG ID
# Alternatively, hardcoding these vars will make a smaller image


RUN apk add chromium font-noto mesa-dri-intel mesa-gl \
    && ln -s /usr/bin/chromium-browser /usr/bin/chromium \
    && apk add libu2f-host \
    && addgroup -S chromium -g $GID \
    && addgroup -S plugdev -g $PLUGDEV \
    && addgroup -S audio -g $AUDIO \
    && adduser -S chromium -G chromium -u $ID \
    && addgroup chromium audio \
    && addgroup chromium video \
    && addgroup chromium plugdev \
    && mkdir -p /home/chromium/Downloads \
    && mkdir -p /home/chromium/data \
    && chown -R chromium:chromium /home/chromium

WORKDIR /home/chromium

# Run as non privileged user
USER chromium

ENTRYPOINT [ "/usr/bin/chromium-browser" ]
