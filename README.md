# CodeYam Editor — Claude Desktop Extension

The `.mcpb` bundle that adds **CodeYam Editor** to Claude Desktop.

Build beautifully designed and highly maintainable apps — web, mobile, desktop,
backend, and more — directly from Claude Desktop, just by describing them.

Once installed, tell Claude:

> **open codeyam**

…and CodeYam opens in your browser, ready to build.

**Version:** 0.1.7 &nbsp;•&nbsp; **Platforms:** macOS (Apple Silicon + Intel), Windows, Linux

---

## Download

**[⬇ Download codeyam-editor.mcpb](https://github.com/jaredcosulich/codeyam-editor-mcpb/releases/latest/download/codeyam-editor.mcpb)** (87 MB)

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
22f6b06df102e07610bb6f54042adbfe6ce14473647681d5c0805735195cd56c
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

Claude will call CodeYam's `launch_editor` tool, start the editor, and hand you
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
| `get_started` | A warm intro to CodeYam, with an offer to open it. |
| `launch_editor` | **The main one.** Opens CodeYam in your browser. Triggered by "open codeyam". |
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
   [Releases](https://github.com/jaredcosulich/codeyam-editor-mcpb/releases).
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
