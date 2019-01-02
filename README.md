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

This will run the following Dockerfile:

```dockerfile
# Run Chromium in a container
FROM alpine:edge
LABEL maintainer "bghost bghost@bghost.xyz"

RUN apk add --no-cache \
    --repository "http://dl-cdn.alpinelinux.org/alpine/edge/testing" \
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
