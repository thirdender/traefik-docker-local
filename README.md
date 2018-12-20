# Traefik for local dev

## One time setup

Create the Docker network that all projects will use to communicate with Traefik:

    docker network create traefik

Then `cd` into this directory and run:

    docker-compose up -d

From this point forward Traefik will control ports 80 and 443 on the machine, listening to the Docker daemon for containers starting and stopping, and routing traffic to these containers as appropriate. The Traefik UI is exposed at http://localhost/traefik/.


## Using with a docker-compose project

To use an external docker-compose project with this Traefik instance, include the following directives in your `docker-compose.yml` file:

```
version: "3.3"

networks:
  # This allows your services to communicate with the Traefik container over the
  # network we set up during the "One time setup" step
  traefik:
    external: true
  # An internal network is recommended to isolate ports your services expose
  internal:
    driver: bridge

services:
  EXAMPLE:
    # Make sure the HTTP service you wish to expose through Traefik is on the
    # `traefik` network, and optionally on your custom "internal" network
    networks:
      - traefik
      - internal
    # These labels are how Traefik configures itself for this container. See
    # https://docs.traefik.io/configuration/backends/docker/#on-containers
    labels:
      - "traefik.backend=dashboard"
      - "traefik.docker.network=traefik"
      - "traefik.frontend.rule=Host:${HOSTNAME}"
      - "traefik.port=3000"
```
