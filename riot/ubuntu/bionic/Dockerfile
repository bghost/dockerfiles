# Run riot in a container

FROM ubuntu:bionic
LABEL maintainer "bghost bghost@bghost.xyz"

RUN apt-get update && apt-get dist-upgrade -y \
    && apt-get install -y wget libasound2 \
    && apt-get install -y libgl1-mesa-dri libgl1-mesa-glx \
    && apt-get install -y curl gnupg

RUN echo 'deb https://riot.im/packages/debian/ bionic main' \
    > /etc/apt/sources.list.d/matrix-riot-im.list \
    && curl -L https://riot.im/packages/debian/repo-key.asc | apt-key add - \
    && apt-get update \
    && apt-get install -y riot-web

ARG GID
ARG ID
    
RUN groupadd -g $GID riot \
    && useradd -m -G audio,video -u $ID -g $GID riot \
    && mkdir -p /home/riot/Downloads \
    && chown -R riot:riot /home/riot \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /home/riot

# Run as non privileged user
USER riot

ENTRYPOINT [ "/usr/local/bin/riot-web" ]
