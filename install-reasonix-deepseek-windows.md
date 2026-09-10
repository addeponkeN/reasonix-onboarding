# Install Reasonix on Windows and use it with DeepSeek

**Assumes:** Windows 10/11 and a DeepSeek API key. Verified against reasonix v1.38.3.

---

## Step 1 — Install Windows Terminal *(optional)*

```powershell
winget install --id Microsoft.WindowsTerminal -e
```

Renders the TUI correctly (truecolor, Unicode, mouse, copy/paste). Skip if you already have it.

---

## Step 2 — Install Node.js

```powershell
winget install --id OpenJS.NodeJS.LTS -e
```

Needs Node 18+. npm comes with Node — it is not a separate install.

Reopen your terminal, then verify:

```powershell
node -v
npm -v
```

---

## Step 3 — Install Reasonix

```powershell
npm i -g reasonix
```

Verify:

```powershell
reasonix --version
```

**PATH gotcha:** if `reasonix` is "not recognized", add the npm prefix to `PATH`, then open a new terminal:

```powershell
npm config get prefix
```

---

## Step 4 — Configure provider and model

```powershell
reasonix setup
```

The wizard writes the global config and scaffolds the credential file. DeepSeek preset: `default_model = "deepseek-flash"`, providers `deepseek-flash` / `deepseek-pro`, `api_key_env = "DEEPSEEK_API_KEY"`.

Files:

| File | Purpose |
| --- | --- |
| `%APPDATA%\reasonix\config.toml` | global config |
| `%APPDATA%\reasonix\.env` | provider credentials |
| `.\reasonix.toml` | optional per-project override |

```text
flags > ./reasonix.toml > %APPDATA%\reasonix\config.toml > built-in defaults
```

---

## Step 5 — Place the API key

The wizard does this. To do it by hand, write it to `%APPDATA%\reasonix\.env`:

```dotenv
DEEPSEEK_API_KEY=sk-...
```

- Never put the key in `config.toml` — that stores only the variable name.
- Project `.env` files and shell env vars (`setx`, `$env:`) are **not** runtime fallbacks. Use the global `.env`.
- Format: one `KEY=value` per line.

Verify:

```powershell
reasonix doctor --json
```

Expect `"key_present": true`.

---

## Step 6 — Smoke test

```powershell
reasonix run "reply with the single word: ready"
```

A response means install + key + network are good.

- **401** → bad key in `.env`.
- **timeout** → proxy/network (see Optional add-ons).
- **`key_present: false`** → `.env` in the wrong place.

---

## Step 7 — Install Git

```powershell
winget install --id Git.Git -e
```

Not required to install or run Reasonix, but `reasonix review` and the diff/checkpoint features work on repositories.

winget drives the full Git for Windows installer unattended (`/SP- /VERYSILENT /SUPPRESSMSGBOXES /NORESTART`) — no wizard, just one UAC prompt. ~62 MB download.

Reopen your terminal, then verify:

```powershell
git --version
```

Git refuses to commit without an identity, so set one once:

```powershell
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

---

## Step 8 — First real session

```powershell
cd C:\path\to\your\project
reasonix
```

- `/init` — generate `AGENTS.md`.
- **Shift+Tab** — toggle Plan mode.
- **Ctrl+Y** — toggle YOLO mode.

Windows has no Bash sandbox (`[sandbox] bash = "off"` is forced), so shell commands are not jailed — approve only for trusted projects.

---

## Step 9 — Keep it updated

```powershell
reasonix upgrade --check
reasonix upgrade
```

---

## Optional add-ons

| Want | Do this |
| --- | --- |
| Desktop app | <https://reasonix.io/?download=desktop#start> |
| Browser UI | `reasonix web` |
| VS Code | Install the `SivanLiu.reasonix-agent` extension (needs Step 3) |
| Proxy / VPN | `[network] proxy_mode = "custom"` (or `"env"`) in `config.toml` |
| Planner + executor | Set `[agent].planner_model = "deepseek-pro"` |
| Telemetry | `reasonix config telemetry auto\|on\|off` |

---

## Quick reference

```powershell
# install
winget install --id Microsoft.WindowsTerminal -e
winget install --id OpenJS.NodeJS.LTS -e
npm i -g reasonix
winget install --id Git.Git -e

# configure
reasonix setup
reasonix doctor --json

# use
reasonix run "implement the TODOs in main.go"
reasonix
reasonix --continue
reasonix web
reasonix upgrade
```

```text
%APPDATA%\reasonix\config.toml   global config (no secrets)
%APPDATA%\reasonix\.env          provider API keys
.\reasonix.toml                  per-project config override
```
