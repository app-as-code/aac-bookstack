<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/icon-dark.svg">
    <source media="(prefers-color-scheme: light)" srcset="assets/icon-light.svg">
    <img src="assets/icon.svg" alt="BookStack logo" width="128">
  </picture>
</p>

<h1 align="center">aac-bookstack</h1>

<p align="center">Self-hosted deployment definition for BookStack, a documentation wiki for notes and guides.</p>

<p align="center">
  <a href="https://github.com/BookStackApp/BookStack"><img alt="Upstream" src="https://img.shields.io/badge/upstream-BookStackApp%2FBookStack-blue?logo=github"></a>
  <img alt="Image" src="https://img.shields.io/badge/image-lscr.io%2Flinuxserver%2Fbookstack%3Av25.12.8--ls250-2496ED?logo=docker&logoColor=white">
  <a href="https://github.com/BookStackApp/BookStack/blob/development/LICENSE"><img alt="License" src="https://img.shields.io/badge/license-MIT-green"></a>
</p>

## About

BookStack is a self-hosted wiki and documentation platform that organizes content in a
clear hierarchy of shelves, books, chapters and pages. It is designed for teams and
individuals who want to write, share and maintain notes and how-to guides in one place,
with a simple editor and full-text search.

Key features (as provided by upstream):

- Hierarchical structure: shelves, books, chapters and pages
- WYSIWYG and Markdown editing with revision history
- Full-text search across all content
- Granular permissions per shelf, book, chapter and page
- Multi-user support with roles and groups
- Login via built-in accounts or OpenID Connect (this deployment is prepared for Authentik)

## Upstream

- Project: <https://github.com/BookStackApp/BookStack>
- Website: <https://www.bookstackapp.com>
- Documentation: <https://www.bookstackapp.com/docs/>
- Container image: `lscr.io/linuxserver/bookstack:v25.12.8-ls250` (LinuxServer.io build)
- Database: `mariadb:12.3.1-ubi10-rc` (deployed as `aac-bookstack-db`)
- License: MIT (upstream project)

## Quick start (Docker Compose)

The Compose file is rendered from `service.yml` by the App-as-Code template engine. For a
manual run, the external networks used for routing must exist first:

```bash
docker network create services-secured
docker network create services-exposed
docker network create docker-default

# Provide the values listed under "Configuration" in .env and stack.env
docker compose up -d
```

Secrets are not stored in this repository. Provide them through the deployment pipeline
or create the `.env` and `stack.env` files yourself (see below). Do not commit them.

## Configuration

Environment variables for the application container (defaults from `service.yml`;
`CHANGE_ME` must be set per deployment, secrets are marked as such):

| Variable | Default | Description |
|---|---|---|
| `APP_URL` | `CHANGE_ME` | Public URL of the BookStack instance, e.g. `https://bookstack.example.com` |
| `APP_KEY` | `CHANGE_ME` (secret) | Application encryption key (Laravel format, `base64:...`) |
| `APP_PROXIES` | `*` | Trusted reverse proxies, used for correct client IP handling |
| `DB_HOST` | `CHANGE_ME` | Database host (the `aac-bookstack-db` service) |
| `DB_PORT` | `3306` | Database port |
| `DB_DATABASE` | `bookstack` | Database name |
| `DB_USERNAME` | `bookstack` | Database user |
| `DB_PASSWORD` | `CHANGE_ME` (secret) | Database password |
| `AUTH_METHOD` | `oidc` | Authentication method |
| `AUTH_AUTO_INITIATE` | `true` | Redirect directly to the OIDC login |
| `OIDC_NAME` | `authentik` | Label of the OIDC login provider |
| `OIDC_ISSUER` | `CHANGE_ME` | OIDC issuer URL of the identity provider |
| `OIDC_ISSUER_DISCOVER` | `true` | Discover OIDC endpoints from the issuer |
| `OIDC_CLIENT_ID` | `CHANGE_ME` (secret) | OIDC client ID |
| `OIDC_CLIENT_SECRET` | `CHANGE_ME` (secret) | OIDC client secret |
| `OIDC_END_SESSION_ENDPOINT` | `CHANGE_ME` | OIDC end-session endpoint for logout |
| `OIDC_ADDITIONAL_SCOPES` | `groups` | Additional OIDC scopes requested |
| `OIDC_DISPLAY_NAME_ATTRIBUTES` | `name` | Claims used as display name |
| `OIDC_EMAIL_ATTRIBUTE` | `email` | Claim used as email address |
| `OIDC_GROUPS_CLAIM` | `groups` | Claim containing group memberships |
| `OIDC_USER_TO_GROUPS` | `true` | Sync user groups from the OIDC claim |
| `OIDC_REMOVE_FROM_GROUPS` | `true` | Remove users from groups no longer present in the claim |
| `MAIL_DRIVER` | `smtp` | Mail transport |
| `MAIL_HOST` | `CHANGE_ME` | SMTP server host |
| `MAIL_PORT` | `465` | SMTP server port |
| `MAIL_ENCRYPTION` | `tls` | SMTP encryption |
| `MAIL_USERNAME` | `CHANGE_ME` | SMTP user name |
| `MAIL_PASSWORD` | `CHANGE_ME` (secret) | SMTP password |
| `MAIL_FROM` | `CHANGE_ME` | Sender address |
| `MAIL_FROM_NAME` | `BookStack` | Sender display name |
| `PUID` / `PGID` | `1000` | User and group ID the container runs as |
| `TZ` | `Europe/Berlin` | Time zone |

Environment variables passed to the application container for the database dependency
(`MARIADB_*`, see `service.yml`) are secrets and must be set per deployment:
`MARIADB_DATABASE`, `MARIADB_USER`, `MARIADB_PASSWORD`, `MARIADB_ROOT_PASSWORD`.

Ports:

| Port | Protocol | Purpose |
|---|---|---|
| `80` (published as `28226`) | TCP | Web interface (`web`) |

Volumes:

| Mount | Description |
|---|---|
| `config` -> `/config` | BookStack configuration and uploaded files |
| `db` -> `/var/lib/mysql` | MariaDB data (database service) |

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, ports, environment,
   volumes, dependencies and per-stage overrides. Do not edit the rendered output.
2. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
3. Releases of this definition are published under <https://github.com/app-as-code/aac-bookstack>.

## Credits and license

BookStack is developed by the BookStack project and its contributors and licensed under MIT.
This repository only contains the deployment definition for it and is not a fork of the
application. The image is built by LinuxServer.io. The logo is provided by the selfh.st icon
collection; see [`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md) for icon sources and licenses.
