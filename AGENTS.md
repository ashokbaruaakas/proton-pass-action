# Proton Pass Action: Agent Instructions

This repository contains a **GitHub Composite Action** that fetches secrets from Proton Pass and writes them to `GITHUB_ENV`.

## Project intent
- Primary implementation lives in `action.yml` (bash-based composite action runtime).
- Documentation lives in `README.md`.
- Contributor/security guidance lives in `CONTRIBUTING.md`.

## What the action does
- Accepts two required inputs:
  - `proton_pat`
  - `secrets_map` (`ENV_VAR=pass://vault/item/field` per line)
- Installs `pass-cli`, authenticates with PAT, resolves each secret path, masks values with `::add-mask::`, and exports them into `GITHUB_ENV`.
- Logs out at the end of execution.

## Editing expectations
- Keep the action headless and non-interactive.
- Preserve strict failure behavior (`set -eo pipefail`) and explicit error messages.
- Never print raw secret values.
- Keep `README.md` aligned when inputs/behavior change.

