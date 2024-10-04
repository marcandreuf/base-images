# base-images

This repo has the docker image definitions for the base images used in running containers in staging and production environments.


## Pocketbase base image

A dockerised solution for the [Pocketbase](https://pocketbase.io/) backend service. 
It is configured to run on port 8080 by default and can be configure to run on specific port by defining the environment variable `VE_PB_PORT`.

```bash
# Run with default port
docker run --rm -it ghcr.io/marcandreuf/base-images/backend-alpine-pocketbase:main

# Run on custom port
docker run --rm -it -e VE_PB_PORT=8091 ghcr.io/marcandreuf/base-images/backend-alpine-pocketbase:main
```

## Node base image

This image has a basic setup to work and run node projects using pnpm.

```bash

docker run --rm -it ghcr.io/marcandreuf/base-images/frontend-node22-pnpm:main
```
