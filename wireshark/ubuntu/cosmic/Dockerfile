# Run chromium in a container

FROM ubuntu:cosmic
LABEL maintainer "bghost bghost@bghost.xyz"

RUN apt-get update \
    && apt-get install -y software-properties-common \
    && add-apt-repository ppa:wireshark-dev/stable \
    && apt-get update && apt-get dist-upgrade -y \
    && apt-get install -y wireshark \
    libgl1-mesa-dri libgl1-mesa-glx

ARG GID
ARG PLUGDEV
ARG ID
ARG AUDIO
    
RUN groupmod -g "${PLUGDEV}" plugdev \
    && groupmod -g "${AUDIO}" audio \
    && useradd -m -G audio,video,plugdev -u "${ID}" wireshark \
    && mkdir -p /home/wireshark/data \
    && chown -R wireshark:wireshark /home/wireshark \
    && rm -rf /var/lib/apt/lists/*

RUN chown root:wireshark /usr/bin/dumpcap \
	&& setcap 'CAP_NET_RAW+eip CAP_NET_ADMIN+eip' /usr/bin/dumpcap

WORKDIR /home/wireshark/data

# Run as non privileged user
USER wireshark

ENTRYPOINT ["/usr/bin/wireshark"]
