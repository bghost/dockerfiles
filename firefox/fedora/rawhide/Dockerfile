# Run firefox in a container

FROM fedora:rawhide
LABEL maintainer "bghost bghost@bghost.xyz"

RUN dnf upgrade -y \
    && dnf install -y firefox mesa-dri-drivers mesa-libGL \
    && dnf install -y libu2f-host u2f-hidraw-policy

ARG GID
ARG PLUGDEV
ARG ID

# Add firefox user
# Need to explicitly set UID / GID to avoid issues with persistence
RUN groupadd -g $GID firefox \
    && groupadd -g $PLUGDEV plugdev \
    && useradd -m -G audio,video,plugdev -u $ID -g $GID firefox \
    && mkdir -p /home/firefox/Downloads \
    && mkdir -p /home/firefox/.mozilla/firefox \
    && chown -R firefox:firefox /home/firefox

WORKDIR /home/firefox

# Run as non privileged user
USER firefox

ENTRYPOINT [ "/usr/bin/firefox" ]
