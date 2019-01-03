# Run firefox in a container

FROM ubuntu:disco
LABEL maintainer "bghost bghost@bghost.xyz"

RUN apt-get update && apt-get dist-upgrade -y \
    && apt-get install -y firefox \
    && apt-get install -y libgl1-mesa-dri libgl1-mesa-glx
    
RUN groupadd -g 1000 firefox \
    && useradd -m -G audio,video,plugdev -u 1000 -g 1000 firefox \
    && mkdir -p /home/firefox/Downloads \
    && mkdir -p /home/firefox/.mozilla/firefox \
    && chown -R firefox:firefox /home/firefox \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /home/firefox

# Run as non privileged user
USER firefox

ENTRYPOINT [ "/usr/bin/firefox" ]
