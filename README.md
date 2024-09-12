# base-images

This repo has the docker image definitions for the base images used in running containers of itqualab projects.


## Pocketbase base image

This image is a dockerised solution for the [Pocketbase](https://pocketbase.io/) backend service. It is configured to run on port 8080.

```bash
# Run with default port
docker run --rm -it ghcr.io/marcitqualab/base-images/backend-alpine-pocketbase:main

# Run on custom port
docker run --rm -it -e VE_PB_PORT=8081 ghcr.io/marcitqualab/base-images/backend-alpine-pocketbase:main
```

## Node base image

This image has a basic setup to work and run node projects using pnpm.

```bash

docker run --rm -it ghcr.io/marcitqualab/base-images/frontend-node22-pnpm:main
```
