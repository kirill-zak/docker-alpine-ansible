# docker-alpine-ansible
Based on Alpine Linux Docker image with built-in Ansible for test playbooks and roles

## Tags

  - `3.17.x`: Based on branch `v3.17`. Releases: `3.17.0`, `3.17.1`, `3.17.2`, `3.17.3`, `3.17.4`, `3.17.5`, `3.17.6`, `3.17.7`, `3.17.8`, `3.17.9`, `3.17.10`.
  - `3.18.x`: Based on branch `v3.18`. Releases: `3.18.0`, `3.18.2`, `3.18.3`, `3.18.4`, `3.18.5`, `3.18.6`, `3.18.7`, `3.18.8`, `3.18.9`, `3.18.10`, `3.18.11`. 
  - `3.19.x`: Based on branch `v3.19`. Releases: `3.19.0`, `3.19.1`, `3.19.2`, `3.19.3`, `3.19.4`, `3.19.5`, `3.19.6`, `3.19.7`, `3.19.8`.
  - `3.20.x`: Based on branch `v3.20`. Releases: `3.20.0`, `3.20.1`, `3.20.2`, `3.20.3`, `3.20.4`, `3.20.5`, `3.20.6`, `3.20.7`.
  - `3.21.x`: Based on branch `v3.21`. Releases: `3.21.0`, `3.21.1`, `3.21.2`, `3.21.3`, `3.21.4`.
  - `3.22.x`: Based on branch `v3.22`. Releases: `3.22.0`, `3.22.1`.
  - `latest`: Latest stable version of Alpine Linux `3.22.x` with built-in Ansible

## How to Build

To build the image on your own locally, do the following:

  1. [Install Docker](https://docs.docker.com/engine/installation/).
  2. `cd` into target directory. For example, target directory for `Alpine Linux 3.22.x` is `alpine3.22`
  3. Run `docker build -t docker-alpine-ansible . --build-arg ALPINE_VERSION=3.22.<release>`. Where `<release>` is release version of Alpine Linux. List of minor releases can found on official release page of Alpine Linux (https://www.alpinelinux.org/releases/)


## How to Use

  1. [Install Docker](https://docs.docker.com/engine/installation/).
  2. Pull pre-build image via `docker image pull kirillzak/docker-alpine-ansible:latest` or build image.
  3. Run a container from the image: `docker run --detach --privileged --volume /sys/fs/cgroup:/sys/fs/cgroup:ro docker-alpine-ansible:latest`

## Author

[Kirill Ziuzin](https://kirill-zak.ru/)

## Archive tags

Not supported and not updated docker images.

List of archive tags:
  - `3.16.x`: Based on branch `v3.16`. Releases: `3.16.0`, `3.16.1`, `3.16.2`, `3.16.3`, `3.16.4`, `3.16.5`, `3.16.6`, `3.16.7`, `3.16.8`, `3.16.9`.