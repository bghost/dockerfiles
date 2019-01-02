# Run chromium in a container

FROM debian:sid-slim
LABEL maintainer "bghost bghost@bghost.xyz"

RUN apt-get update && apt-get dist-upgrade -y \
    && apt-get install -y chromium \
    && apt-get install -y libgl1-mesa-dri libgl1-mesa-glx

ARG GID
ARG PLUGDEV
ARG ID
    
RUN groupadd -g $GID chromium \
    && groupmod -g $PLUGDEV plugdev \
    && useradd -m -G audio,video,plugdev -u $ID -g $GID chromium \
    && mkdir -p /home/chromium/Downloads \
    && mkdir -p /home/chromium/data \
    && chown -R chromium:chromium /home/chromium

# Run as non privileged user
USER chromium

ENTRYPOINT [ "/usr/bin/chromium" ]
