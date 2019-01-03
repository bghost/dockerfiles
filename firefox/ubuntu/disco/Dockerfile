# Run firefox in a container

FROM ubuntu:disco
LABEL maintainer "bghost bghost@bghost.xyz"

RUN apt-get update && apt-get dist-upgrade -y \
    && apt-get install -y firefox \
    && apt-get install -y libgl1-mesa-dri libgl1-mesa-glx
    
ARG GID
ARG PLUGDEV
ARG ID

RUN groupadd -g $GID firefox \
    && groupmod -g $PLUGDEV plugdev \
    && useradd -m -G audio,video,plugdev -u $ID -g $GID firefox \
    && mkdir -p /home/firefox/Downloads \
    && mkdir -p /home/firefox/.mozilla/firefox \
    && chown -R firefox:firefox /home/firefox \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /home/firefox

# Run as non privileged user
USER firefox

ENTRYPOINT [ "/usr/bin/firefox" ]
