[![MIT License](https://img.shields.io/github/license/zenosmosis/docker-coturn)](https://raw.githubusercontent.com/zenOSmosis/docker-coturn/master/LICENSE)
[![ci][ci-image]][ci-url]

[ci-image]: https://github.com/zenosmosis/docker-coturn/actions/workflows/ci.yml/badge.svg
[ci-url]: https://github.com/zenOSmosis/docker-coturn/actions

# Docker image for Coturn TURN / STUN server

A Docker container with the [Coturn TURN server](https://github.com/coturn/coturn).

* hub.docker.com (Docker image): [zenosmosis/docker-coturn](https://hub.docker.com/r/zenosmosis/docker-coturn/)
* github.com (Repo): [zenOSmosis/docker-coturn](https://github.com/zenOSmosis/docker-coturn)

Note, for those who would rather build this sort of thing from scratch, here's an article written by the author of the original package we forked from:  https://devblogs.microsoft.com/cse/2018/01/29/orchestrating-turn-servers-cloud-deployment.

# Run the container

```bash
docker run \
  -d \
  -p 3478:3478 \
  -p 3478:3478/udp \
  -p 65435-65535:65435-65535/udp \
  --restart=always \
  --name coturn \
  zenosmosis/docker-coturn
```

## Environment variables

This image supports some environment variables:

* `USERNAME`: Username needed for turn. Defaults to `username`
* `PASSWORD`: Password needed for turn. Defaults ro `password`
* `REALM`: Realm needed for turn. Defaults to `realm`
* `MIN_PORT`: This defines the min-port for the range used by turn. Defaults to `65435`
* `MAX_PORT`: This defines the max-port for the range used by turn. Defaults to `65535`

*An example:*

```bash
# This makes sure, that the min- and max-port is the same for all environment variables
export MIN_PORT=50000
export MAX_PORT=50010
docker run \
  -d \
  -p 3478:3478 \
  -p 3478:3478/udp \
  -p ${MIN_PORT}-${MAX_PORT}:${MIN_PORT}-${MAX_PORT}/udp \
  -e USERNAME=another_user \
  -e PASSWORD=another_password \
  -e REALM=another_realm \
  -e MIN_PORT=${MIN_PORT} \
  -e MAX_PORT=${MAX_PORT} \
  --restart=always \
  --name coturn \
  zenosmosis/docker-coturn
```

*An easier example (if Docker Compose is installed)*

```bash
docker-compose up
```

(see [docker-compose.yml](docker-compose.yml) for configuration)

## Certificates

Store the cert under `/opt/cert.pem` and the key under `/opt/pkey.pem` and mount them as volumes:

```bash
docker run \
  -d \
  -p 3478:3478 \
  -p 3478:3478/udp \
  -p 65435-65535:65435-65535/udp \
  --volume /opt/cert.pem:/etc/ssl/turn_server_cert.pem \
  --volume /opt/pkey.pem:/etc/ssl/turn_server_pkey.pem \
  --restart=always \
  --name coturn \
  zenosmosis/docker-coturn
```

## Debugging

```bash
docker logs coturn
docker exec -it coturn /bin/bash
```

# Bonus: Build and push the container to [Docker Hub](https://hub.docker.com/)

```bash
# Clone
git clone https://github.com/zenOSmosis/docker-coturn.git 

# Build
docker build -t zenosmosis/docker-coturn .

# Tag
VERSION=0.0.2
docker tag zenosmosis/docker-coturn zenosmosis/docker-coturn:$VERSION

# Login to Docker (if not already logged in)
docker login

# Push
docker push zenosmosis/docker-coturn:latest
docker push zenosmosis/docker-coturn:$VERSION

# At this point, you MAY want to log out of Docker, as it could cause authentication errors when trying to build other's containers
docker logout
```

# Thanks

The initial version of this image was created by [anastasiia-zolochevska/turn-server-docker-image](https://github.com/anastasiia-zolochevska/turn-server-docker-image).  Thanks to [boldt/turn-server-docker-image](https://github.com/boldt/turn-server-docker-image) for the README.md and Dockerfile updates.
