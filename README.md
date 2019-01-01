# Dockerfiles

## This repo has some recipes for building and running desktop containers

### The main credit for this repo goes to [@jessfraz](https://twitter.com/jessfraz) and her [excellent repo](https://github.com/jessfraz/dockerfiles)

Each subdirectory should have a `Dockerfile` and bash scripts which can be
used to build and run the image respectively.

Building should be done with tags in the directory containing the
desired `Dockerfile`, for example:

```console
$ cd chromium && docker build -t bghost/chromium:alpine .
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

* CI pipline and Docker registry for regular updates
