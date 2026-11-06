# Galvanize Everything

**See how any website, app or game works, then have AI build your own version, in modern,
memory-safe C++ by default.**

Point it at a target and say what you want. It studies the original with real inspection tools,
has a coding agent build a new project, checks the result against the original and refines it.

![A finished build in Galvanize Everything: the build feed with its checks passed, and the
project panel showing Study, Build, Check and Ready done, built with C++ and Qt 6](docs/images/screenshot.png)

> **Status:** an early fork, built and run on macOS (Apple silicon) only so far. The code for
> Windows, Linux and the web viewer is there, but this fork hasn't built or tested it on those
> systems. There are no prebuilt downloads yet: build it from source (see
> [Get started](#get-started)).

Galvanize Everything is a modified version of Craft Anything, by the ArtCraft team, which is
MIT OR Apache-2.0 licensed. It is not made, endorsed or supported by them. It differs from Craft
Anything in that:

- **Builds are C++.** Desktop apps default to C++20 with Qt 6 Widgets, console programs to plain
  C++20, and "…in C++" or "with raylib" gives a C++ game. Dear ImGui (SDL3) is there on request,
  and Rust still is when a request says "in Rust". Every C++ build is a CMake project that bans
  manual memory management and owning raw pointers, checks indexes from input, builds with
  warnings as errors and standard-library hardening, and has an ASan/UBSan option.
- **Nothing calls home.** There's no telemetry, crash reporting or update check (Craft Anything
  had none either), and this version also drops the attribution headers sent to OpenRouter, the
  Help menu's links to the upstream repository, the web viewer's download link, the installers'
  homepage and support links, and the upstream CI and review-bot configuration. The network is
  used only for what you start: AI providers you set up, engines you choose to install, and the
  targets you study.
- **Its own name and icon,** with its own bundle id, config folder and environment variables
  (`GALVANIZE_EVERYTHING_*`), so it can sit beside an installed Craft Anything.

Qt 6 has to be installed for the default desktop-app stack (`brew install qt`, the Qt Online
Installer, or `qt6-base-dev`); CMake 3.25+ and a C++20 compiler for every C++ build.

## How it works

Point Galvanize Everything at a target, say what you want, and press Start. It works in four steps,
asking you in plain words when a decision is yours:

1. **Study:** inspect the original: its pages at every width, its code, its files, how it runs.
2. **Build:** a coding agent builds a new project, in the language, framework or engine your
   request names (or a sensible default).
3. **Check:** compare the build with the original and list what doesn't match yet.
4. **Ready:** open, play or preview the result, or ask for changes and it refines it.

## Two ways to use it

- **Simple** (default): "What do you want to make?" Pick a website, app or game, describe it,
  press Start. A project page shows the four steps (Study → Build → Check → Ready), what the
  agent is doing in plain words, question and approval cards, how alike the result is, and
  buttons to open the result or its folder. Ask for changes in the box at the bottom.
  "Just look inside" opens a target for questions without building anything (in Deep mode).
- **Advanced**: the full workbench: every tool (Ctrl/Cmd+K), functions, strings, pseudo-C,
  disassembly, hex, call graphs, evidence, unknowns, jobs, logs and the agent console.

Both modes drive the same engine.

## Clean room or Deep

- **Clean room** (the default): nothing inside the original is read, not its code, files or page
  source. Galvanize Everything works from what you can see: pictures you add, the website as a visitor
  sees it (screenshots, visible text, measured layout, colours, fonts and spacing, menus opened)
  and, when you allow it, a picture of the app's window. Tools that would look inside are refused
  and say how to turn on Deep mode.
- **Deep**: every tool is used, for your private use. A program's code and files are read with the
  built-in engines (and Ghidra when it's installed), a website's JavaScript is read and its start
  page watched, and the builder may use all of them.

Clean room is a rule about what Galvanize Everything offers and refuses. It is not an operating-system
sandbox: it doesn't isolate your computer. A coding agent you run still acts as you, and what it can
read, write or run depends on its own permissions. Which of an agent's own tools Galvanize Everything can
refuse differs by agent; for Claude Code's tools in chats and questions, see
[`docs/claude-code.md`](docs/claude-code.md).

Switch in the title bar or Settings, with `settings_set deep_mode true`, or for one CLI run with
`--deep` (or `--clean-room`).

## AI

Building needs a coding agent on your computer. **Claude Code** is supported first: Galvanize
Everything runs your own, unmodified `claude` program, which uses your own sign-in; Galvanize Everything
never sees or handles your account. Your use of it falls under your own agreement with
Anthropic. See [docs/claude-code.md](docs/claude-code.md) for how and why. A Codex adapter
exists but hasn't been verified on a real install yet. **GitHub Copilot** can use your installed
Copilot CLI through ACP: sign in separately with `copilot login`. Other agents that speak ACP (Gemini CLI,
Qwen Code, opencode, goose, Hermes, Maki) can be switched to it per backend; of those, only Maki has
been run live. Anthropic, OpenAI, OpenRouter and local
OpenAI-compatible servers are available for questions and analysis (Settings → AI).

## Built for agents

Every tool is also a CLI subcommand and an MCP tool, so an agent can study a target or run a
whole rebuild without the app:

```sh
galvanize-everything-cli commands                                   # every tool, by toolset
galvanize-everything-cli mcp                                        # serve them to any MCP client
galvanize-everything-cli rebuild https://example.com "Recreate this website"
```

Results are evidence records with their confidence, coverage and limitations; big ones come back
as a summary first and are paged with `core_evidence_browse`. `galvanize-everything-cli setup`
connects Galvanize Everything to coding-agent apps.

## Under the hood

A pure-Rust reverse-engineering engine with 320 tools (300 in the engine, the rest from the AI and
setup layers) for native programs, .NET, Java, Android, Apple, Electron/JavaScript, websites,
firmware, packet captures, databases and game files. Its own decoders, disassembler and
decompiler work without any outside program; Deep mode uses them all, and Ghidra when it's
installed. It powers the desktop app (egui), the CLI and the MCP server, and parts of it run in
the browser as WebAssembly. The full list is in [docs/tools.md](docs/tools.md).

## Get started

You need:

- Rust **1.97 or newer** to build the app (a dependency needs it; `rustup update`).
- CMake 3.25+ and a C++20 compiler (Xcode's command-line tools, GCC 12+ or Clang 16+) for the
  C++ projects it builds, and Qt 6 for the default desktop-app stack (`brew install qt`).
- A coding agent signed in on your computer, Claude Code first (see [AI](#ai)).
- On Debian or Ubuntu, for the desktop app:

  ```sh
  sudo apt-get install libxkbcommon-dev libwayland-dev libx11-dev libxrandr-dev libxi-dev libgl1-mesa-dev libgtk-3-dev
  ```

Then:

```sh
git clone https://github.com/j0uni/galvanize-everything.git
cd galvanize-everything
cargo run --release -p galvanize-everything                # the desktop app
cargo install --path apps/galvanize-everything-cli         # the CLI, as `galvanize-everything-cli`
galvanize-everything-cli doctor                            # checks the install, coding agents and engines
```

Building needs a coding agent signed in on your computer (see [AI](#ai)); `doctor` says what's
missing. Rebuild from the command line (questions are asked in the terminal):

```sh
galvanize-everything-cli rebuild https://example.com "Recreate this website"
galvanize-everything-cli rebuild - "A snake game in C++" --rounds 1
galvanize-everything-cli rebuild https://example.com "Make the header blue" --continue-in "<project folder>"
galvanize-everything-cli rebuild - "Add a contact form" --project-folder "<your folder>" --existing edit
```

New projects go in `Documents/Galvanize Everything` (change it in Settings → General). Each project
folder holds `craft.json` (the request, target, status and history), `capture/` (what was
recorded from the target), `app/` (the generated project), `checks/` (each comparison against
the original), `investigation.cany/` (evidence) and `inbox/` (open questions).

A project can also start in a folder you pick (Choose a folder… under Start, or `--project-folder`).
If it already has files, you say what they are: the app itself, which the builder edits in place,
or material to start from, with the app built in `app/` beside them (Settings → General can
remember the answer). Settings → General also lists projects from more folders; removing one
only takes it off the list.

### A Mac app

To build `Galvanize Everything.app` (with the CLI inside it, ad-hoc signed) into `dist/`:

```sh
packaging/macos/package.sh --arch aarch64        # or x86_64
```

Without an Apple Developer ID the app is signed only for this Mac; on another Mac, Gatekeeper
asks before opening it (right-click → Open).

## Contributing

Issues and pull requests are welcome. Read [CONTRIBUTING.md](CONTRIBUTING.md) first; the rules
every change follows, for people and AI agents alike, are in [AGENTS.md](AGENTS.md), and where
each kind of addition goes is in [docs/extending.md](docs/extending.md). Report security problems
privately, as [SECURITY.md](SECURITY.md) says.

Before a pull request:

```sh
cargo fmt --all --check
cargo clippy --workspace --all-targets -- -D warnings
cargo test --workspace
cargo xtask ci                                   # everything above, plus the layer, lock and secret checks
```

## License and credits

Dual-licensed under [MIT](LICENSE-MIT) or [Apache-2.0](LICENSE-APACHE), at your option. Based on
Craft Anything, Copyright (c) 2026 ArtCraft Team and the Craft Anything contributors; the
required notices are in [NOTICE](NOTICE). Bundled fonts, icons and other assets keep their own
licenses, listed in [ATTRIBUTION.md](ATTRIBUTION.md).
