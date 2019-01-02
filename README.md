# [bghost/dockerfiles](https://github.com/bghost/dockerfiles)

## This repo has some recipes for building and running desktop containers

### The main credit for this repo goes to [@jessfraz](https://twitter.com/jessfraz) and her [excellent repo](https://github.com/jessfraz/dockerfiles)

Each application subdirectory should have the following structure: `Dockerfile` and scripts:

* a `build` script to build the image(s)
* a run script using the name of the application
* further subdirectories as necessary representing tags for the images

Building should be done with tags in the application directory for example:

```console
$ cd chromium && ./build [tag]
```

This will run the following `build` script:

```sh
#!/bin/bash

set -euo pipefail

user='bghost'
repo='chromium'
tag='alpine'

# set environment vars
export GID=$(id -g)
export PLUGDEV=$(/bin/grep "plugdev" /etc/group | cut -d ':' -f 3)
export ID=$(id -u)

# build the container:
docker build \
    --build-arg GID=${GID} \
    --build-arg ID=${ID} \
    --build-arg PLUGDEV=${PLUGDEV} \
    -t ${user}/${repo}:${tag} .

# clean up our host environment
unset {GID,PLUGDEV,ID}
```


This script builds the tagged `chromium` image using the following Dockerfile:

```dockerfile
# Run Chromium in a container
FROM alpine:edge
LABEL maintainer "bghost bghost@bghost.xyz"

RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" \
        >>/etc/apk/repositories \
    && apk upgrade --no-cache

ARG GID
ARG PLUGDEV
ARG ID

RUN apk add chromium mesa-dri-intel mesa-gl \
    && apk add libu2f-host \
    && addgroup -S chromium -g $GID \
    && addgroup -S plugdev -g $PLUGDEV \
    && adduser -S chromium -G chromium -u $ID \
    && addgroup chromium audio \
    && addgroup chromium video \
    && addgroup chromium plugdev \
    && mkdir -p /home/chromium/Downloads \
    && mkdir -p /home/chromium/data \
    && chown -R chromium:chromium /home/chromium

# Run as non privileged user
USER chromium

ENTRYPOINT [ "/usr/bin/chromium-browser" ]
```


There are (potentially) helpful comments in the `Dockerfile` in the `chromium` directory

This can then be launched with the following run script, which can also be
wrapped in a function and added to the appropriately sourced file such as
`.bash_aliases`, etc.

```sh
#!/bin/bash

# You will want the custom seccomp profile from Jessie Frazzelle
# https://raw.githubusercontent.com/jessfraz/dotfiles/master/etc/docker/seccomp/chrome.json

# Use bghost/chromium:alpine by default
tag=${1:-alpine}
proxy=

if [[ $# -gt 2 ]]; then
    (>&2 printf "%s\n" \
        "chromium takes 2 optional arguments \"tor\" to use the proxy and <tag>")
            return 1
fi
if  [[ "$1" == "tor" ]]; then
    proxy="socks5://172.17.0.1:9050"
    tag="alpine"
fi
if [[ $# -eq 2 ]]; then
    tag="$2"
fi

# u2f may require the user be in the plugdev group
# also, the --device /dev/hidraw1 line may need modification. Check dmesg.
# `find /dev -name 'hidraw*' | head -n 2` or so

docker run --rm -d \
    -c 4 \
    -m 4096M \
    -v /etc/localtime:/etc/localtime:ro \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -e "DISPLAY=unix${DISPLAY}" \
    -v "${HOME}/Downloads:/home/chromium/Downloads" \
    -v "${HOME}/.config/chromium:/home/chromium/data" \
    -v /dev/shm:/dev/shm \
    -v /etc/hosts:/etc/hosts \
    --security-opt seccomp="${HOME}/chrome.json" \
    --device /dev/snd \
    --device /dev/dri \
    --device /dev/usb \
    --device /dev/bus/usb \
    --device /dev/hidraw0 \
    --device /dev/hidraw1 \
    --name chromium \
    bghost/chromium:"$tag" \
    --user-data-dir="/home/chromium/data" \
    --proxy-server="$proxy" \
    --force-device-scale-factor="1.3" \
    --enable-native-gpu-memory-buffers
```


The commands present in the associated bash scripts can easily be modified to
be functions that can be sourced by via `.bashrc` or so, which then allows
them to override the default install of an application using an intentional
namespace collision, when considering using the Docker containers as a
replacement for the traditional apps.

Before running any of the containers, it is necessary to run something such as
`xhost local:root` on the host to allow access to the unix socket and
`DISPLAY` environment var.  

### Notes:

* `UID` and `GID` of an unprivileged container user must match those of the
  user running the container.
* Some assembly required if using `udev` and `systemd` for hid access.
* the `plugdev` group may be necessary for access to a u2f token such as a
  YubiKey
* for assistance with setting up a YubiKey, [drduh](https://github.com/drduh)
  has a [comprehensive guide](https://github.com/drduh/YubiKey-Guide)

### TODO:

* CI pipeline and Docker registry for regular updates
