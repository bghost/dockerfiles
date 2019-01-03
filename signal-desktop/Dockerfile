# Run signal-desktop in a container

FROM ubuntu:bionic
LABEL maintainer "bghost bghost@bghost.xyz"

ARG GID
ARG ID

RUN apt-get update && apt-get dist-upgrade -y \
    && apt-get install -y curl gpg \
    && apt-get install -y libgl1-mesa-dri libgl1-mesa-glx \
    && curl -s https://updates.signal.org/desktop/apt/keys.asc | apt-key add - \
    && echo "deb [arch=amd64] https://updates.signal.org/desktop/apt xenial main" \
        >/etc/apt/sources.list.d/signal-xenial.list \
    && apt-get update && apt-get install -y signal-desktop \
    && groupadd -g 1000 signal \
    && useradd -m -G audio,video -u 1000 -g 1000 signal \
    && mkdir -p /home/signal/.config/Signal \
    && mkdir -p /home/signal/attachments \
    && chown -R signal:signal /home/signal \
    && rm -rf /var/lib/apt/lists/*

# Run as non privileged user
USER signal

ENTRYPOINT [ "/usr/local/bin/signal-desktop" ]
