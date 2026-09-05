# SwiftDeploy

**SwiftDeploy is a manifest-driven deployment tool for containerized applications.**

Define your application once in a simple `manifest.yaml`, and SwiftDeploy handles the repetitive work of preparing the deployment, validating the environment, starting the application, and verifying that it is healthy.

Instead of manually managing Docker Compose and Nginx configuration for every deployment, you describe what you want and let SwiftDeploy generate and manage it.

---

## Why SwiftDeploy?

Deploying a small application often means maintaining several pieces of configuration:

* Container configuration
* Reverse proxy configuration
* Networking
* Environment settings
* Health checks
* Deployment state

SwiftDeploy brings these into a single manifest and turns them into a repeatable deployment workflow.

```text
             manifest.yaml
                   │
                   ▼
             ┌─────────────┐
             │ SwiftDeploy │
             └──────┬──────┘
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
       Generate   Validate   Deploy
       configs    setup      service
                              │
                              ▼
                         Health Check
```

## Features

* Manifest-driven deployments
* Automatic configuration generation
* Pre-deployment validation
* Docker-based application deployment
* Health-aware deployments
* Stable and canary deployment modes
* Simple promotion between deployment modes
* Clean teardown of deployed resources

---

## Requirements

* Python 3.11+
* [uv](https://github.com/astral-sh/uv)
* Docker

---

## Installation

```bash
git clone https://github.com/nehecodes/swiftdeploy.git
cd swiftdeploy

uv venv .venv
source .venv/bin/activate

uv pip install -e swiftdeploy/
```

---

## Quick Start

Create a `manifest.yaml` in your project:

```yaml
services:
  name: my-app
  image: my-app:latest
  port: 8080
  env:
    mode: stable
  health_path: /healthz

nginx:
  port: 8443
  server_name: localhost
  proxy_timeout: 30s

network:
  name: myapp-net
  driver_type: bridge
```

Then deploy:

```bash
swiftdeploy deploy
```

SwiftDeploy will:

1. Generate the required configuration
2. Validate the deployment
3. Start the application
4. Wait for the health check
5. Confirm that the service is ready

---

## Commands

### Initialize

Generate the deployment configuration from the manifest.

```bash
swiftdeploy init
```

### Validate

Run pre-deployment checks without starting the application.

```bash
swiftdeploy validate
```

SwiftDeploy checks the manifest, required configuration, Docker image, network availability, and generated configuration before deployment.

### Deploy

Prepare and start the application, then wait for it to become healthy.

```bash
swiftdeploy deploy
```

You can also specify a custom timeout:

```bash
swiftdeploy deploy --timeout 90
```

### Promote

Switch between deployment modes.

```bash
swiftdeploy promote canary
swiftdeploy promote stable
```

This allows a running application to be moved between stable and canary modes without manually editing generated configuration.

### Teardown

Stop and remove the deployed application and its resources.

```bash
swiftdeploy teardown
```

To also remove generated configuration:

```bash
swiftdeploy teardown --clean
```

---

## Custom Manifests

SwiftDeploy can use a manifest other than the default `manifest.yaml`:

```bash
swiftdeploy --manifest config/prod.yaml deploy
```

---

## Deployment Model

SwiftDeploy separates **what you want to deploy** from **how the deployment is configured**.

The manifest describes the application:

```text
Application
├── Service
├── Image
├── Port
├── Environment
├── Health check
├── Network
└── Proxy configuration
```

SwiftDeploy turns that definition into the configuration required to run the service.

This makes deployments more **repeatable, predictable, and easier to manage**.

---

## Project Status

SwiftDeploy is an actively developed deployment automation project focused on making containerized application deployments simpler and more repeatable.

The long-term goal is to provide a lightweight deployment workflow that bridges the gap between a Dockerized application and a production-ready deployment without requiring developers to manually manage every piece of infrastructure configuration.
