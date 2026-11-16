<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/icon-dark.svg">
    <img src="assets/icon.svg" alt="Wiki.js logo" width="128">
  </picture>
</p>

<h1 align="center">aac-wikijs</h1>

<p align="center">Self-hosted deployment definition for Wiki.js, an open-source wiki with Git storage.</p>

<p align="center">
  <a href="https://github.com/requarks/wiki"><img alt="Upstream" src="https://img.shields.io/badge/upstream-requarks%2Fwiki-blue?logo=github"></a>
  <a href="https://hub.docker.com/r/requarks/wiki"><img alt="Image" src="https://img.shields.io/badge/image-requarks%2Fwiki%3A2.5-2496ED?logo=docker&logoColor=white"></a>
  <a href="https://github.com/requarks/wiki/blob/main/LICENSE"><img alt="License" src="https://img.shields.io/badge/license-AGPL--3.0-green"></a>
</p>

## About

Wiki.js is a self-hosted wiki application that runs on Node.js and stores its pages in a
PostgreSQL database. Pages can be written in Markdown or with a visual editor, and content
can be synchronized with a Git repository, so the wiki history stays available outside the
application.

Key features (as provided by upstream):

- Markdown and visual page editors
- Git storage: content is synchronized with an external Git repository
- Full-text search across pages
- Fine-grained page access rules and multiple authentication providers
- Multi-language user interface

## Upstream

- Project: <https://github.com/requarks/wiki>
- Website: <https://js.wiki>
- Container image: `requarks/wiki:2.5` (Docker Hub)
- Database: `postgres:18` (deployed as `aac-wikijs-db`)
- License: AGPL-3.0 (upstream project)

## Quick start (Docker Compose)

The Compose file is rendered from `service.yml` by the App-as-Code template engine. For a
manual run, the external networks used for routing must exist first:

```bash
docker network create services-secured
docker network create services-exposed

# Provide the values listed under "Configuration" in .env / stack.env
docker compose up -d
```

Secrets are not stored in this repository. Provide them through the deployment pipeline
or create the `.env` and `stack.env` files yourself (see below). Do not commit them.

## Configuration

Environment variables (defaults from `service.yml`; `CHANGE_ME` must be set per deployment):

| Variable | Default | Description |
|---|---|---|
| `PUID` / `PGID` | `1000` | User and group ID the container runs as |
| `TZ` | `Europe/Berlin` | Time zone |
| `DB_TYPE` | `postgres` | Database type used by Wiki.js |
| `DB_HOST` | `CHANGE_ME` | Database host name, e.g. the `aac-wikijs-db` container |
| `DB_PORT` | `5432` | Database port |
| `DB_NAME` | `wikijs` | Database name |
| `DB_USER` | `wikijs` | Database user |
| `DB_PASS` | `CHANGE_ME` (secret) | Database password |
| `POSTGRES_USER` | `wikijs` (secret) | User for the PostgreSQL dependency |
| `POSTGRES_PASSWORD` | `CHANGE_ME` (secret) | Password for the PostgreSQL dependency |
| `POSTGRES_DB` | `wikijs` (secret) | Database created by the PostgreSQL dependency |

Ports:

| Port | Protocol | Purpose |
|---|---|---|
| `3000` | TCP | Web interface (`web`); published host port `3185` in `service.yml` |

Volumes:

| Mount | Description |
|---|---|
| `data` -> `/data` | Wiki.js application data and assets |
| `repo` -> `/var/wiki/repo` | Git repository used for content storage |
| `db` -> `/var/lib/postgresql` | PostgreSQL data (database dependency) |

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, ports, environment,
   volumes, dependencies and per-stage overrides. Do not edit the rendered output.
2. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
3. Releases of this definition are published under <https://github.com/app-as-code/aac-wikijs>.

## Credits and license

Wiki.js is developed by the requarks project and licensed under AGPL-3.0. This repository
only contains the deployment definition for it and is not a fork of the application. The
logo is taken from the selfh.st icon collection; see
[`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md) for icon sources and licenses.
