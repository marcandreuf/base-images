# Base images

This repo has the docker image definitions that I use on my dev container environments, CI/CD and Production services.

**The packages are published at: https://github.com/marcandreuf?tab=packages**

---

## Architecture

**Published packages:** [https://github.com/marcandreuf/base-images](https://github.com/marcandreuf/base-images)

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'background': '#ffffff', 'primaryColor': '#e8f4f8', 'primaryTextColor': '#1a1a2e', 'primaryBorderColor': '#0984e3', 'lineColor': '#2d3436', 'secondaryColor': '#dfe6e9', 'tertiaryColor': '#ffeaa7'}}}%%
flowchart TD
    subgraph BaseImages["📦 Base Images - Official"]
        NodeBase["🟢 node:22-bookworm\n~1.1GB"]
        AlpineBase["🏔️ alpine:latest\n~7MB"]
    end

    subgraph FrontendImages["🎨 Frontend Images"]
        Node22PNPM["📁 node22-pnpm\nNode + PNPM + TypeScript\n~1.3GB"]
        Node22WebDev["🌐 node22-pnpm-web-dev\n+ Playwright + Chromium\n~1.8GB"]
        Node22Build["🔨 node22-pnpm-build\n+ Custom User (CI/CD)\n~1.8GB"]
    end

    subgraph BackendImages["⚙️ Backend Images"]
        Pocketbase["🗄️ alpine-pocketbase\nPocketBase v0.22.21\n~50MB"]
    end

    subgraph MultiStage["🚀 Multi-Stage Build Pattern"]
        BuildStage["Stage 1: BUILD\nInstall dependencies\nCompile TypeScript\nBundle assets\n(uses node22-pnpm-build)"]
        ProdStage["Stage 2: PRODUCTION\nCopy artifacts only\nNo dev dependencies\n(uses alpine or distroless)"]
        FinalImage["✅ Final Image\nMinimal footprint\nProduction ready\n~100-200MB"]
    end

    subgraph CacheStrategy["💾 PNPM Cache Strategy"]
        PNPMStore["PNPM Store\n${PNPM_HOME}/store"]
        SharedCache["Host Volume Mount\nReuse across containers"]
    end

    NodeBase --> Node22PNPM
    Node22PNPM --> Node22WebDev
    Node22PNPM --> Node22Build
    AlpineBase --> Pocketbase

    Node22Build --> BuildStage
    BuildStage --> ProdStage
    ProdStage --> FinalImage

    PNPMStore -.-> SharedCache
    SharedCache -.-> Node22PNPM
    SharedCache -.-> Node22WebDev
    SharedCache -.-> Node22Build

    style BaseImages fill:#e8f5e9,stroke:#43a047,stroke-width:2px,color:#1a1a2e
    style FrontendImages fill:#e3f2fd,stroke:#1976d2,stroke-width:2px,color:#1a1a2e
    style BackendImages fill:#fff3e0,stroke:#fb8c00,stroke-width:2px,color:#1a1a2e
    style MultiStage fill:#f3e5f5,stroke:#8e24aa,stroke-width:2px,color:#1a1a2e
    style CacheStrategy fill:#fce4ec,stroke:#c2185b,stroke-width:2px,color:#1a1a2e
```

*Ejemplo de multi-stage build para base images optimizadas, similar a mis Dockerfiles para Node.js y Pocketbase.*

### Image Hierarchy

| Stage | Purpose | Size Impact |
|-------|---------|-------------|
| **Base Image** | Official Node/Alpine | Starting point |
| **Build Stage** | Install deps, compile TS, bundle | Temporary (~1.8GB) |
| **Production Stage** | Copy only artifacts | Final (~100-200MB) |

---

# Backend

## 1. Pocketbase base: `backend/docker-alpine-pocketbase`

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


## 1. Docker Node 22 pnpm: `frontent/dockerfile-node22-pnpm`

This image has a basic node env setup with PNPM_HOME. This folder can be mounted ona docker compose file mapping the host PNPM_HOME folder and thus reusing all the pnpm cached packages to save disk space.

### Build
```bash
docker build --progress=plain -t test-base-image-node22-pnpm -f frontend/dockerfile-node22-pnpm .
```

### Run

```bash
docker run --rm -it test-base-image-node22-pnpm:latest /bin/bash
```

## 2. Docker Node 22 pnpm web dev: `frontent/dockerfile-node22-pnpm-web-dev`

This image expands the "frontend-node22-pnpm" image with [mermaid](https://mermaid.js.org/) deps to render mdx with mermaid diagrams on the server side.

### Build
```bash
docker build --progress=plain -t test-base-image-node22-pnpm-web-dev -f frontend/dockerfile-node22-pnpm-web-dev .
```

### Run

```bash
docker run --rm -it test-base-image-node22-pnpm-web-dev:latest /bin/bash
```


## 3. Docker Node 22 pnpm web dev: `frontent/dockerfile-node22-pnpm-build`

This image expands the "frontend-node22-pnpm-web-dev" with build args to customise the image to the user on the shared runner host machines in Github.

### Build
```bash
docker build --progress=plain -t test-base-image-node22-pnpm-build -f frontend/dockerfile-node22-pnpm-build .
```
**Custom builds**

```bash
# Github runs docker containers with the runner user with id 1001.
docker build --no-cache --progress=plain --build-arg USERNAME=node --build-arg HOST_UID=1000 --build-arg HOST_GID=1000 -t test-node22-pnpm-build -f frontend/dockerfile-node22-pnpm-build .

```

### Run

```bash
# Run with the default user `runner`
docker run --rm -it test-base-image-node22-pnpm-build:latest /bin/bash

# Run with custom node user
docker run --rm -it -u node test-base-image-node22-pnpm-build:latest /bin/bash
```

