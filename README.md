# Base images

This repo has the docker image definitions which I use on my dev container environments, CI/CD and Production.


# Backend

## 1. Pocketbase base image: `backend/docker-alpine-pocketbase`

A dockerised solution for the [Pocketbase](https://pocketbase.io/) backend service. 
It is configured to run on port 8080 by default. 

### Build
```bash
docker build --progress=plain -t test-base-image-pocketbase -f backend/dockerfile-alpine-pocketbase .
```

### Run
This image can be configured to run on a specific port by re-defining the variable environment `VE_PB_PORT`.

```bash
# Run with default port
docker run --rm -it test-base-image-pocketbase:latest

# Run on custom port
docker run --rm -it -e VE_PB_PORT=8091 test-base-image-pocketbase:latest
```
<br/>


# Frontend


## 1. 

This image has a basic setup to work and run node projects using pnpm.

```bash

docker run --rm -it ghcr.io/marcandreuf/base-images/frontend-node22-pnpm:main
```

# Node base image for web development

This image expands the "frontend-node22-pnpm" image with mermaid deps to build mdx with diagrams.

```bash

docker run --rm -it ghcr.io/marcandreuf/base-images/dockerfile-node22-pnpm-web-dev:main
```


## Custom builds

```bash
# Node22 pnpm build image with custom username
docker build --no-cache --progress=plain --build-arg USERNAME=node --build-arg HOST_UID=1000 --build-arg HOST_GID=1000 -t test-node22-pnpm-build -f frontend/dockerfile-node22-pnpm-build .

docker build --progress=plain --build-arg USERNAME=node --build-arg HOST_UID=1000 --build-arg HOST_GID=1000 -t test-node22-pnpm-build -f frontend/dockerfile-node22-pnpm-build .

```