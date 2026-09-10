# Session log

**Date:** 2026-09-10
**Workspace:** `F:\dev\Projects\_reasonix`
**Tooling:** reasonix v1.38.3 · Node v24.21.0 · npm 11.19.0 · git 2.55.0 · Windows / amd64

## Goal

Write a step-by-step guide for installing Reasonix on Windows and using it with
DeepSeek, given a DeepSeek key is already available. Starting outline was
*Windows Terminal → npm → reasonix*; the ask was to find what was missing.

## What we did

1. **Surveyed the workspace** — found it empty except for an empty `onboarding/`
   directory, so there was no code to read. Pivoted to evidence gathering.
2. **Inspected the live install on this machine** — the CLI is already installed
   globally (`reasonix@1.38.3`), with a working DeepSeek setup in
   `%APPDATA%\reasonix\config.toml` and `.env`.
3. **Read authoritative sources** — the shipped npm package README, `reasonix --help`,
   `reasonix doctor --json`, and the official docs (`GUIDE.md`, `CONFIG_PATHS.md`)
   from the repo's default branch `main-v2`.
4. **Wrote the guide** to `onboarding/install-reasonix-deepseek-windows.md`.

## Deliverable

`onboarding/install-reasonix-deepseek-windows.md` — a 9-step guide (Step 0–8)
covering install, configuration, credential placement, smoke test, first session,
and updates, plus an optional add-ons table and a quick reference.

## Key findings

- **Node.js is the real prerequisite, not npm.** npm ships with Node; the package
  requires `engines.node >= 18`.
- **`npm i -g reasonix` installs a small Node launcher plus a prebuilt native Go
  binary** (`@reasonix/cli-win32-x64`/`-arm64`) — Node is only needed to install
  and launch it.
- **Reasonix home on Windows is `%APPDATA%\reasonix`**; global config is
  `config.toml`, global credentials are `.env`.
- **The API key does not belong in `config.toml`.** Config stores only the
  variable *name* (`api_key_env = "DEEPSEEK_API_KEY"`); the value lives in the
  global `.env`. Project `.env` files and inherited shell env vars are **not**
  runtime fallbacks for provider keys.
- **Config resolution order:** flags > `./reasonix.toml` > `%APPDATA%\reasonix\config.toml` > built-in defaults.
- **Model IDs are version-specific.** This build's DeepSeek preset writes
  `deepseek-v4-flash` / `deepseek-v4-pro` against `https://api.deepseek.com`
  (Anthropic-style alt: `https://api.deepseek.com/anthropic`).
- **Windows has no OS-level Bash sandbox**, so `[sandbox] bash` is forced to
  `"off"` — shell commands are not jailed.
- **Documentation discrepancy:** the CLI hints you can `export DEEPSEEK_API_KEY=...`,
  but the docs state inherited shell env vars are not a runtime provider-key
  fallback. The global `.env` is the reliable path.

## Notes

- Guide verified against reasonix v1.38.3; no secrets were written to disk.
- A host-generated `reasonix.toml` (session permission grants) exists at the
  workspace root and was left untouched.

## Open threads

- Optional follow-ups not yet done: a troubleshooting section (401s, proxy/region
  issues, `key_present: false`) and a Chinese translation of the guide.
