# EscapeCloud Platform - Docker Compose

Deploying EscapeCloud Platform via `docker compose` is intended to be simple. This folder contains a single Compose template, an environment template, and this README.

Project structure:

```text
.
├── docker-compose.yml
├── env.template
└── README.md
```

## Overview

The Compose template runs the main Escape components:

- `escape_webapp`
- `escape_scheduler`
- `escape_api`
- `escape_engine_stage1` to `escape_engine_stage9`
- `escape_db_init`
- `escape_engine_db`

The template also contains optional containerized services for:

- `escape_web_db`
- `rabbitmq`

If you are using managed infrastructure for PostgreSQL or RabbitMQ, keep using your external services and set the connection details in `.env`.

## Prerequisites

Before deployment, make sure you have:

- Docker Engine installed
- Docker Compose plugin installed
- Access to `registry.onstackit.cloud`
- A VM or host with ports `80` and `443` reachable
- DNS pointing your hostname to the server

For HTTPS mode you also need:

- a `.pfx` certificate file
- the PFX password

## Configuration

Copy `env.template` to `.env`:

```bash
cp env.template .env
```

Then fill in the required values.

Important variables:

- `HOST_NAME`
  Public hostname, for example `domain.tld`
- `WEB_ENV`
  Example: `prod`
- `WEB_IMAGE_TAG`
  Web image tag to deploy
- `ENGINE_IMAGE_TAG`
  Engine image tag to deploy
- `WEB_DB_*`
  Web database connection settings
- `ENGINE_DB_*`
  Engine database connection settings
- `RABBITMQ_*`
  RabbitMQ connection settings
- `WEB_PFX_FILE`
  Container path to the mounted PFX, for example `/run/certs/domain_tld.pfx`
- `WEB_PFX_PASSWORD`
  Password for the PFX file

## HTTP And HTTPS Modes

The webapp supports two modes.

### HTTP-only mode

Leave these values empty:

```env
WEB_PFX_FILE=
WEB_PFX_PASSWORD=
```

In this mode, the webapp serves HTTP on port `80`.

### HTTPS mode

Set both values:

```env
WEB_PFX_FILE=/run/certs/your-certificate.pfx
WEB_PFX_PASSWORD=your-password
```

The Compose file mounts:

```yaml
./certs:/run/certs:ro
```

So place the certificate file in a local `certs/` folder next to `docker-compose.yml`.

In this mode:

- HTTP on port `80` redirects to HTTPS
- HTTPS is served on port `443`

## Deploy

Run the stack from this directory:

```bash
docker compose up -d
```

If you want to pull the latest tagged images first:

```bash
docker compose pull
docker compose up -d
```
