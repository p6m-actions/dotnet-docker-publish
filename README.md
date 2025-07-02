# Dotnet Docker Publish

![Latest Release](https://img.shields.io/github/v/release/p6m-actions/dotnet-docker-publish?style=flat-square&label=Latest%20Release&color=blue)

## Description

A GitHub Action that builds and publishes .NET applications as Docker containers. This action simplifies the process of containerizing .NET applications and pushing them to container registries.

## Usage

```yaml
- name: Build and publish Docker image
  uses: p6m-actions/dotnet-docker-publish@v1
  with:
    image-name: my-dotnet-app
    image-tag: ${{ github.sha }}
    registry: ghcr.io
    push: true
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `dockerfile-path` | Path to the Dockerfile | No | `Dockerfile` |
| `context-path` | Build context path | No | `.` |
| `image-name` | Docker image name | Yes | - |
| `image-tag` | Docker image tag | No | `latest` |
| `registry` | Container registry URL | No | - |
| `push` | Whether to push the image to registry | No | `true` |
| `build-args` | Build arguments for Docker build (multiline string) | No | - |
| `platforms` | Target platforms for multi-platform build | No | `linux/amd64` |

## Outputs

| Output | Description |
|--------|-------------|
| `image-digest` | The digest of the built image |
| `image-metadata` | Build result metadata |
| `image-uri` | Full image URI with registry and tag |

## Examples

### Basic Usage

```yaml
- name: Build and publish to Docker Hub
  uses: p6m-actions/dotnet-docker-publish@v1
  with:
    image-name: myapp
    image-tag: v1.0.0
```

### With Registry

```yaml
- name: Build and publish to GitHub Container Registry
  uses: p6m-actions/dotnet-docker-publish@v1
  with:
    image-name: myapp
    image-tag: ${{ github.sha }}
    registry: ghcr.io/${{ github.repository_owner }}
```

### With Build Arguments

```yaml
- name: Build with custom build args
  uses: p6m-actions/dotnet-docker-publish@v1
  with:
    image-name: myapp
    image-tag: latest
    build-args: |
      DOTNET_VERSION=8.0
      BUILD_CONFIGURATION=Release
```

### Multi-platform Build

```yaml
- name: Build for multiple platforms
  uses: p6m-actions/dotnet-docker-publish@v1
  with:
    image-name: myapp
    image-tag: latest
    platforms: linux/amd64,linux/arm64
```

### Complete Workflow Example

```yaml
name: Build and Deploy

on:
  push:
    branches: [main]

jobs:
  build-and-publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Login to Container Registry
        uses: p6m-actions/docker-repository-login@v1
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      
      - name: Build and publish Docker image
        uses: p6m-actions/dotnet-docker-publish@v1
        with:
          image-name: myapp
          image-tag: ${{ github.sha }}
          registry: ghcr.io/${{ github.repository_owner }}
          dockerfile-path: ./src/MyApp/Dockerfile
          context-path: ./src
```