# Changelog

All notable changes to this deployment definition are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Changed

- Upstream image tracks BookStack v25.12.8 (LinuxServer.io build `ls250`) as defined in `service.yml`.

## [0.0.1]

### Added

- Initial App-as-Code service definition for BookStack with a MariaDB dependency.
- Web interface on port 80 (published on host port 28226) with Traefik routing and automatic DNS.
- Optional OpenID Connect login (Authentik) with group synchronization.
- SMTP mail configuration.
- README with configuration reference and project icon.
