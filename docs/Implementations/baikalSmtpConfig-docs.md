# baikalSmtpConfig - Implementation Docs

## Overview

- Goal: Provide a ready-to-run SMTP example for Baikal using the requested Elements System mail server account so invitations can be tested quickly.
- Scope: Update the generic SMTP Docker Compose example and document the change.

## What Was Built

- Feature 1: Replaced placeholder SMTP values in the generic compose example with concrete server and account values, while keeping password via env interpolation.
- Feature 2: Added implementation documentation for maintainability and future updates.
- Feature 3: Adjusted container port declaration to avoid host port 80 conflicts in reverse-proxy deployments (for example Coolify).

## Key Files & Structure

- `examples/docker-compose.email.yaml`: Generic Baikal SMTP compose example now prefilled with requested SMTP host, sender, username, and an interpolated password variable.
- `docs/Implementations/baikalSmtpConfig-docs.md`: Tracks what was changed, how it works, and how to extend it.

## How It Works

- Data flow: Baikal container reads `MSMTPRC` from environment at startup, generates msmtp config, and sends invitation emails through the configured SMTP relay.
- Important classes/modules: Runtime SMTP bootstrap script in `files/docker-entrypoint.d/50-start-msmtp.sh` consumes `MSMTPRC`.
- External services/APIs: SMTP endpoint `mail.elementsystem.com` on port `587` with auth + TLS.

## Setup & Usage

- Prereqs: Valid mailbox credentials for `testcalendar@elementsystem.com`, reachable SMTP server, and `SMTP_PASSWORD` exported in environment/Coolify secrets.
- Run: Start stack with compose file, then test with `msmtp` from inside the running container.

## Testing

- How to run tests: Run container and execute a manual send test from shell:
  - `docker exec -it <container_name> /bin/sh`
  - `echo "SMTP test" | msmtp -a default testcalendar@elementsystem.com`

## Extensibility Notes

- Known extension points: Convert password line to env interpolation (for example `${SMTP_PASSWORD}`) to avoid storing secrets in git.
- Future improvements: Add Coolify-specific deployment template with secret-managed SMTP password.

## Changelog (summary)

- Phase 1: Updated `examples/docker-compose.email.yaml` placeholders to concrete SMTP settings with secret-safe password interpolation.
- Phase 2: Added implementation documentation under `docs/Implementations`.
- Phase 3: Switched from `80:80` to `80` in the SMTP compose example to remove external host port 80 binding.
