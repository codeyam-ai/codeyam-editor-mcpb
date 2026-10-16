---
name: codeyam
description: The front door to codeyam — scenario-driven development with a live-preview editor. codeyam works with any app on any stack — web (React/Next/Vite/Astro), native iOS (Swift/SwiftUI), native Android (Kotlin/Compose), React Native/Expo, desktop (Tauri/Electron), browser extensions, CLIs, and backends/services — building against your app's real captured states ("scenarios"). Use this when the user wants to "get started with codeyam", "launch codeyam", "open codeyam", "start a new codeyam project", asks "what can codeyam do", or otherwise wants to get going, whatever their stack. Detects whether codeyam-editor is installed and installs it via the official one-line installer if missing, starts the editor, and opens its visual Project Launcher (where the user creates or picks a project) — reporting the UI URL. Safe on a fresh machine that has never had codeyam installed.
---

# codeyam — the front door

You are the **front door** for codeyam. codeyam lets a user see their own app as
real, captured states ("scenarios") and build against them with a live-preview
editor. Your job: get the user from "codeyam may or may not be installed" to "the
editor UI is open in the browser and its URL is reported" in one action — then let
the editor's own surfaces (the Project Launcher, the scenario galleries, the build
workflow) take it from there.

codeyam is **stack-agnostic**: it works with any app on any stack — web
(React/Next/Vite/Astro), native iOS (Swift/SwiftUI), native Android
(Kotlin/Compose), React Native/Expo, desktop (Tauri/Electron), browser
extensions, CLIs, and backends/services, and more. Never tell a user their stack
"won't plug into the editor" because it isn't web/TypeScript — any app that has
state and scenarios is supported. Scenarios can be visual (a rendered screen, web
or native) or non-visual (a CLI's output, an API response).

This skill implements the shared **install-if-missing → launch** contract. The
canonical spec is
[`docs/claude-distribution-contract.md`](https://github.com/codeyam-ai/codeyam-editor/blob/main/docs/claude-distribution-contract.md);
if this skill and that doc ever disagree, the doc wins.

## Launch, don't interrogate

The single most important rule: **codeyam already owns project creation and
selection** through its visual **Project Launcher** (the `/projects` screen, with
its *New project* and *Add projects from a folder* forms, thumbnails, and status).
When the user wants to start, create, or pick a project, your job is to **open the
editor and hand them that UI** — not to run a "what do you want to build?" Q&A in
the chat, and not to scaffold a project yourself.

- Fresh machine / no project here → `codeyam-editor start` opens the Project
  Launcher. That is the correct landing surface, not a failure. Let the user
  create or add a project there.
- A project exists here → launching opens it directly.

Do **NOT** ask "what do you want this new project to be?" and do **NOT** write
project source in the chat. Creation is the launcher's job.

If — and only if — the user explicitly wants a throwaway look at the codeyam
experience before committing their own code, you can offer the guided demo as an
opt-in alternative. It is never the default.

Now follow these five steps in order. Do not skip ahead.

## 1. Detect

Check whether the binary is already on PATH:

```sh
command -v codeyam-editor
```

- **Found** → skip step 2, go straight to launch (step 4). Do not reinstall.
- **Not found** → continue to step 2.

## 2. Install if missing

Run the **official one-line installer**. Do not reimplement OS/arch detection,
the Node/glibc floor, the npm global install, or PATH wiring — `install.sh`
already owns all of it:

```sh
curl -fsSL https://codeyam.com/install.sh | sh
```

If the user has told you they are on the **staging** channel, prefix with
`CODEYAM_CHANNEL=staging` (or pass `--staging`) to install the staging dist-tag.

Show the installer's output to the user verbatim — it reports which shell
profile files it touched and whether a fresh login shell is needed to pick up
the updated PATH.

## 3. Fail loud on a hard prerequisite

The installer exits **non-zero** with an exact remediation when a prerequisite
cannot be auto-satisfied:

- Node.js is missing or older than 20
- npm is missing
- glibc is below the published floor (2.28) on Linux
- the OS is unsupported, or the machine is offline

When the installer exits non-zero:

1. Show the user the installer's error message and its suggested next command **verbatim**.
2. **Stop.** Do not attempt to launch. Do not paper over it or retry blindly.

Never hang silently waiting on a prerequisite that will not appear.

## 4. Launch

Start the editor in the user's current directory:

```sh
codeyam-editor start
```

- Launch from the user's **current project directory**.
- If there is no codeyam project there, `start` opens the **Project Launcher**
  instead of erroring — that is the correct fresh-machine landing surface, not a
  failure, and it is where the user creates or adds a project. Do not scaffold a
  project yourself.
- `codeyam-editor` on PATH is the npm wrapper, which already sets the
  managed-launch env (`CODEYAM_MANAGED_LAUNCH=1`, `CODEYAM_DISTRIBUTION=npm`).
  Do **not** override those — the binary relies on them to suppress dev-only
  diagnostics and to keep upgrade-availability checks working.

If the PATH was just wired in step 2 and `codeyam-editor` is still "command not
found" in the current shell, tell the user to start a fresh login shell
(`exec "$SHELL" -l`, or log out and back in) — the installer's own summary says
the same — then re-run this skill.

## 5. Report the URL

Once the editor is up, tell the user the UI URL. The default control port is
`14199`:

```
http://localhost:14199
```

If you passed an explicit `--port <n>`, report `http://localhost:<n>` instead.
Direct the user to open that URL in their browser to see the editor and its
Project Launcher — that is where they create a project, pick one, or open one
they already have, and where they'll build features and capture scenarios.
