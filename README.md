# Docker Images for nodenv

This repository contains Dockerfiles to install [nodenv](https://github.com/nodenv/nodenv) within docker containers. Pre-build images are available at [Docker Hub](https://hub.docker.com/r/conchoid/docker-nodenv/).

## Build a multi-architecture OCI image

`24.22.3-trixie/Dockerfile` targets both `linux/amd64` and `linux/arm64`. The `git-lfs` binary is taken from the matching `conchoid/debian` tag via `TARGETARCH`.

```sh
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  --output type=oci,dest=nodenv-oci.tar,name=conchoid/docker-nodenv:v1.6.2-1-24.22.3-trixie \
  -f 24.22.3-trixie/Dockerfile 24.22.3-trixie
```

The result is an OCI image archive containing a multi-platform index. To push it to a registry instead, replace `--output` with `--push` and use `-t conchoid/docker-nodenv:v1.6.2-1-24.22.3-trixie`.
