# CodeYam Editor for Claude

Build beautifully designed and highly maintainable apps — web, mobile, desktop,
backend, and more — just by describing them to Claude. CodeYam is a
live-preview editor that builds your app against its real captured states
("scenarios"), whatever your stack: React/Next/Vite, native iOS and Android,
React Native, Tauri/Electron, CLIs, and backend services.

This repository ships CodeYam to Claude in two forms:

- **A Claude Code plugin** — the `/codeyam` skill plus CodeYam's MCP tools, in one install.
- **A Claude Desktop extension** — the `codeyam-editor.mcpb` bundle, attached to each [release](https://github.com/codeyam-ai/codeyam-editor-mcpb/releases).

Once installed, tell Claude:

> **open codeyam**

…and CodeYam opens in your browser, ready to build.

**Version:** 0.1.7 &nbsp;•&nbsp; **Platforms:** macOS (Apple Silicon + Intel), Windows, Linux

---

## Claude Code plugin

```
/plugin install codeyam-editor --marketplace codeyam-ai/codeyam-editor-mcpb
```

On older Claude Code versions, add the marketplace first:

```
/plugin marketplace add codeyam-ai/codeyam-editor-mcpb
/plugin install codeyam-editor@codeyam
```

Then type `/codeyam` (or just ask Claude to "open codeyam").

### What the plugin runs, installs, and connects to

The plugin is two readable files, and it does nothing it doesn't describe here:

- **`skills/codeyam/SKILL.md`** — the `/codeyam` skill. It tells Claude to check
  for the `codeyam-editor` CLI (`command -v codeyam-editor`), install it if it's
  missing, run `codeyam-editor start` in your current directory, and report the
  local UI URL (`http://localhost:14199` by default).
- **`bin/codeyam-mcp`** — a POSIX `sh` script that Claude Code runs as the
  plugin's MCP server. If `codeyam-editor` is already on your `PATH` it runs
  `codeyam-editor mcp` and nothing else. If it is missing, it first runs the
  official installer.

**Installation (only when the CLI is missing).** Both entry points install with
`curl -fsSL https://codeyam.com/install.sh | sh`. That script checks your OS,
architecture, Node.js (20+) and glibc version, then runs
`npm install -g @codeyam-editor/codeyam-editor` from the public npm registry and
adds npm's global `bin` directory to `PATH` in your shell profile files (for
example `~/.zshrc` / `~/.zprofile` or `~/.bashrc` / `~/.bash_profile`). It never
calls `sudo`, and it exits with an error and a suggested fix if a prerequisite
is missing. You can install the CLI yourself beforehand
(`npm install -g @codeyam-editor/codeyam-editor`) and the plugin will skip this
step entirely.

**Network and data.** The plugin's own files contact only `codeyam.com` (the
installer script) and the npm registry (the package), and only when the CLI is
missing. The editor itself runs locally: it serves its UI on `localhost`, reads
and writes the project you point it at, and stores its state in a `.codeyam/`
folder inside that project. The plugin asks for no credentials and reads none
from your environment.

**MCP tools.** `codeyam` (open the editor), `list_projects`, `list_scenarios`,
`show_scenario`, `scenario_gallery`, `design_gallery`, `quickstart_demo`,
`create_plan`, `list_plans`, and `get_plan`. The listing, viewing, and gallery
tools are read-only; `create_plan` writes a plan file into a project's
`.codeyam/plans/` queue; `codeyam` starts the local editor process.

---

## Claude Desktop extension (`.mcpb`)

### Download

**[⬇ Download codeyam-editor.mcpb](https://github.com/codeyam-ai/codeyam-editor-mcpb/releases/latest/download/codeyam-editor.mcpb)** (87 MB)

The bundle is self-contained — it ships a complete CodeYam editor for each
platform, so there is nothing else to install and no network access needed to
start.

<details>
<summary>Verify the download (optional)</summary>

**macOS / Linux**

```sh
shasum -a 256 codeyam-editor.mcpb
```

**Windows (PowerShell)**

```powershell
Get-FileHash .\codeyam-editor.mcpb -Algorithm SHA256
```

Expected SHA-256 for v0.1.7:

```
1799869cf2d3bb011d9dd5dc051ee22204f035e210403c25cbc2298152c560d6
```

</details>

---

## Requirements

- **Claude Desktop** — [download it here](https://claude.ai/download) if you don't have it yet. Extensions require a reasonably recent version; if you don't see an **Extensions** section in Settings, update Claude Desktop first.
- **A modern browser** — CodeYam's editor UI opens in your default browser.
- That's it. No Node.js, no npm, no terminal, no separate CodeYam install.

---

## Install on macOS

1. Download `codeyam-editor.mcpb` using the link above.
2. **Double-click the file.** Claude Desktop opens and shows an install prompt.
   *(Alternatively: open Claude Desktop → **Settings** → **Extensions**, then
   drag `codeyam-editor.mcpb` onto the window.)*
3. Confirm the install. Claude Desktop registers the CodeYam MCP server for you
   — no JSON editing, no terminal.
4. **Quit and reopen Claude Desktop** so it picks up the new extension.
5. Start a new chat and type **`open codeyam`**.

Claude will call CodeYam's `codeyam` tool, start the editor, and hand you
back a URL. Open it and you're building.

### macOS notes

- The bundled macOS binary is a **universal** build signed with an Apple
  Developer ID certificate (*Nod Labs, Inc.*), so it runs natively on both
  Apple Silicon and Intel Macs.
- Claude Desktop extracts the extension outside of Gatekeeper's quarantine, so
  you should **not** see a "cannot be opened" or "unidentified developer"
  warning. If you ever do, clear the quarantine flag on the extracted extension
  and restart Claude Desktop:

  ```sh
  xattr -dr com.apple.quarantine ~/Library/Application\ Support/Claude/Claude\ Extensions/local.mcpb.codeyam.codeyam-editor
  ```

- Installed files land in:

  ```
  ~/Library/Application Support/Claude/Claude Extensions/local.mcpb.codeyam.codeyam-editor/
  ```

---

## Install on Windows

1. Download `codeyam-editor.mcpb` using the link above.
2. **Double-click the file.** Claude Desktop opens and shows an install prompt.
   *(Alternatively: open Claude Desktop → **Settings** → **Extensions**, then
   drag `codeyam-editor.mcpb` onto the window.)*
3. Confirm the install.
4. **Quit and reopen Claude Desktop** (make sure it isn't still running in the
   system tray — right-click the tray icon and quit).
5. Start a new chat and type **`open codeyam`**.

### Windows notes

- **SmartScreen warning.** The bundled `codeyam-editor.exe` is Authenticode-signed,
  but with a certificate that isn't yet publicly trusted, so Windows may show
  *"Windows protected your PC"* or *"Publisher: Unknown"*. If prompted, choose
  **More info → Run anyway**. (A publicly trusted EV certificate is on the
  roadmap; until then this warning is expected, not a sign of a bad download —
  verify the SHA-256 above if you want certainty.)
- If your organization blocks unsigned/unknown publishers via policy, you may
  need IT to allow it, or use the npm install path instead:

  ```powershell
  npm install -g @codeyam-editor/codeyam-editor
  ```

  If a `codeyam-editor` binary is already on your `PATH`, the extension prefers
  that one — which also gives you automatic version upgrades.
- Installed files land in:

  ```
  %APPDATA%\Claude\Claude Extensions\local.mcpb.codeyam.codeyam-editor\
  ```

---

## Install on Linux

Same flow as above — double-click the `.mcpb`, or drag it into Claude Desktop's
**Settings → Extensions**. The bundle carries an `x86_64` glibc build. Installed
files land under `~/.config/Claude/Claude Extensions/`.

---

## What you can do once it's installed

Just talk to Claude. It has these CodeYam tools available:

| Tool | What it does |
|---|---|
| `codeyam` | **The main one.** Opens CodeYam in your browser. Triggered by "open codeyam". |
| `quickstart_demo` | Guided first-success runbook for a brand-new user. |
| `list_projects` | Lists the CodeYam projects registered on this machine. |
| `list_scenarios` | Lists a project's captured scenarios — the real states of your app. |
| `show_scenario` | Shows one captured scenario as an inline screenshot. |
| `scenario_gallery` | Renders your whole app, across every captured state, as an HTML gallery. |
| `design_gallery` | Renders a project's design mockups as a gallery. |
| `create_plan` | Writes a researched plan into a project's CodeYam plan queue. |
| `list_plans` / `get_plan` | Browses and reads queued and completed plans. |

Things to try in a Claude Desktop chat:

- *"open codeyam"* — launch the editor.
- *"what codeyam projects do I have?"*
- *"show me the scenarios in my note-keeper project"*
- *"build me a gallery of every screen in my app"*
- *"plan a fix for the login redirect bug in my-app"* — the plan lands in the
  project's queue, ready to run next time you open the editor.

The pattern: **Claude Desktop is where you think and plan; the CodeYam editor is
where you build and verify.**

---

## How it behaves

- **First launch needs no install and no network.** The bundle already contains a
  full editor, so `open codeyam` works on a fresh, offline machine.
- **A `PATH` install wins if you have one.** If `codeyam-editor` is already
  installed (e.g. via `npm install -g @codeyam-editor/codeyam-editor`), the
  extension launches *that* binary instead — it's npm-managed and self-upgrading.
- **Closing Claude Desktop does not stop the editor.** The editor is spawned as
  its own detached process. Quitting Claude Desktop stops the MCP server but
  leaves your editor running, same as if you'd started it from a terminal. To
  stop it explicitly:

  ```sh
  codeyam-editor editor stop-server
  ```

  Or just ask Claude to stop the editor.

---

## Updating

The bundled editor isn't auto-updating (it isn't npm-managed). When a newer
version is available, Claude will surface an update notice. To update:

1. Download the newest `codeyam-editor.mcpb` from
   [Releases](https://github.com/codeyam-ai/codeyam-editor-mcpb/releases).
2. Install it the same way — it replaces the existing extension.
3. Restart Claude Desktop.

If you'd rather have automatic updates, install the npm package
(`npm install -g @codeyam-editor/codeyam-editor`); the extension will then
prefer that self-upgrading binary.

---

## Uninstall

Claude Desktop → **Settings** → **Extensions** → find **CodeYam Editor** →
remove it. Then restart Claude Desktop.

If an editor server is still running, stop it first with
`codeyam-editor editor stop-server`.

---

## Troubleshooting

**"open codeyam" does nothing / Claude says it has no such tool**
The extension isn't loaded. Check **Settings → Extensions** to confirm CodeYam
Editor is listed and enabled, then fully quit and reopen Claude Desktop (on
Windows, check the system tray).

**The install prompt never appears when I double-click the file**
Open Claude Desktop first, go to **Settings → Extensions**, and drag the `.mcpb`
onto the window instead.

**The editor URL opens but the page won't load**
The editor server may still be starting. Give it a few seconds and reload. If it
persists, ask Claude to check the editor status, or run
`codeyam-editor editor stop-server` and try `open codeyam` again.

**macOS says the binary can't be verified**
Clear the quarantine flag (see [macOS notes](#macos-notes)) and restart Claude
Desktop.

**Windows SmartScreen blocks it**
Expected — see [Windows notes](#windows-notes). Verify the SHA-256, then choose
**More info → Run anyway**, or install via npm.

---

## Links

- **CodeYam** — https://codeyam.com
- **Distribution contract** (how every CodeYam/Claude surface behaves) —
  [docs/claude-distribution-contract.md](https://github.com/codeyam-ai/codeyam-editor/blob/main/docs/claude-distribution-contract.md)

## License

MIT
