# Morpha MCP

**Morpha** is a parametric 3D CAD for Windows that AI apps can operate through the [Model Context Protocol (MCP)](https://modelcontextprotocol.io).
Ask in plain words, and the AI builds the model, edits it and checks it, using the same operations as the Morpha screen.

- Website: https://morphasoft.com/en/
- Download the Beta (free until March 31, 2027, including business use): https://morphasoft.com/en/beta/
- 日本語の説明: [README.ja.md](README.ja.md)

> This repository holds the documentation for connecting AI apps to Morpha. Morpha itself is a commercial desktop application and is not open source. The MCP server (`morpha-mcp.exe`) is installed together with Morpha.

## What it looks like

A recording of Morpha (left) and Morpha Agent (right, in development, Japanese UI). Asked to check for interference, the AI runs the check and shows the overlap in red.

https://morphasoft.com/assets/video/agent-interference.mp4

## What the AI can do

The MCP server exposes about 270 operations. They call the same code as the screen, so everything the AI does appears in the feature history and can be undone, edited or fixed by a person.

| Area | Examples |
| --- | --- |
| Sketches | lines, arcs, splines, dimensions and geometric constraints |
| Features | extrude, revolve, sweep, loft, holes and threads, fillets, chamfers, shell, draft, patterns, booleans |
| Surfaces and sheet metal | offset, trim, blends; flanges, bends and flat patterns |
| Assemblies | place parts, mates, arrangements, exploded views, interference check (results shown in the 3D view) |
| Drawings | JIS-style sheets, projected and section views, dimensions, export to PDF and DXF |
| Checking | mass properties, measurements, screenshots of the 3D view so the AI can look at the result |
| Files | open and save Morpha files, import and export STEP, IGES, STL, DXF |
| Parameters | named parameters and expressions that drive feature values |

Operations for paid features (CAE and Assembly Animation, part of the Pro plan, in development) are listed only when you have the license. They are not included in the Beta.

## Requirements

- Windows 10 or 11 (64-bit), graphics that support Direct3D 11
- Morpha installed and running
- An AI app that supports MCP servers over stdio (for example Claude Desktop or Claude Code)

## How to connect

First, in Morpha, open **Settings → External integration** and turn on **Enable external integration (API)**. It is off by default. Keep Morpha running while the AI app uses it.

What you register in the AI app is `morpha-mcp.exe`, which is installed together with Morpha. Its location is shown on the same settings screen (by default `%LocalAppData%\MorphaCAD\current\morpha-mcp.exe`).

### Claude Desktop: add the bundle (easiest)

1. Download `morpha-mcp-<version>.mcpb` from the [latest release](https://github.com/morphasoft/morpha-mcp/releases/latest).
2. In Claude Desktop, open **Settings → Extensions** and add the file.
3. Leave the location of `morpha-mcp.exe` empty. Fill it in only if you installed Morpha somewhere else.

The bundle does not contain the MCP server. It starts the `morpha-mcp.exe` installed with Morpha, so the server always matches your Morpha.

### Other AI apps: register the location

Register `morpha-mcp.exe` as an MCP server that runs as a **command** (stdio). It cannot be registered in a field that takes a URL.

| AI app | Where | What to write |
| --- | --- | --- |
| Claude Desktop (by hand) | Settings → Developer → Edit Config (`claude_desktop_config.json`) | the `mcpServers` entry below |
| Cursor | `~/.cursor/mcp.json` (all projects) or `.cursor/mcp.json` (one project) | the `mcpServers` entry below |
| Claude Code | a terminal | `claude mcp add morpha -s user -- "C:\…\morpha-mcp.exe"` |
| VS Code | Command Palette → **MCP: Open User Configuration** (`mcp.json`) | the `servers` entry below |

```json
{
  "mcpServers": {
    "morpha": { "command": "C:\\…\\morpha-mcp.exe" }
  }
}
```

VS Code uses a different key and needs the type:

```json
{
  "servers": {
    "morpha": { "type": "stdio", "command": "C:\\…\\morpha-mcp.exe" }
  }
}
```

- In JSON files, write each `\` of the location as `\\`.
- In Claude Code, `-s user` makes the server available in every folder. Without it, the server is registered only for the folder where you ran the command.
- Restart the AI app after registering.

The formats above were checked against each app's documentation on November 23, 2026. Apps change their settings from time to time (VS Code is moving to a newer format), so follow the app's own documentation if it differs.

Then try: *“Make a 100 × 60 mm plate, 8 mm thick, with M6 tapped holes in the four corners.”*

### If it does not connect

1. Is Morpha running, and is **Enable external integration (API)** on?
2. Did you restart the AI app after registering?
3. Is `morpha-mcp.exe` registered as a command, not as a URL?
4. Does the location match the one shown in Morpha's settings? In a JSON file, is each `\` written as `\\`?
5. Still not working? Please open an [issue](https://github.com/morphasoft/morpha-mcp/issues) with the AI app's name and the error message.

## Where your data goes

- Morpha accepts connections only from inside your PC (127.0.0.1, with a token). External integration stays off until you turn it on.
- Morpha never sends your model or drawing files to our servers.
- When you use a cloud AI app, what you ask and the model information the AI reads (dimensions, screenshots) are sent to the provider of that AI app. Take care with designs that must not leave your company.
- **Morpha Agent** (in development, free) is our own AI app that runs entirely on your PC with a local model, so nothing leaves your company.

## Python

The same operations are available from Python (`morpha` package, installed with Morpha), so routine work can be scripted. Morpha can also record what you do on screen as a Python macro.

## Status

Morpha is in Beta. It has bugs and missing features, and the API may change between versions (0.x). AI does not always get a model right; check the result before you rely on it.

## Feedback

Questions and bug reports: support@morphasoft.com, or open an issue in this repository.

## License

The documentation in this repository is © the operator of Morpha. Morpha and `morpha-mcp.exe` are distributed under the [Morpha Terms of Use](https://morphasoft.com/en/legal/terms/) (the Japanese text governs).
