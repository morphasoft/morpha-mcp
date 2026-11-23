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

1. In Morpha, open **Settings → External integration** and turn on **Enable external integration (API)**. It is off by default.
2. Click **Copy settings** on the same screen. This copies the entry for your AI app, including the location of `morpha-mcp.exe` on your PC.
3. Paste it into your AI app's MCP settings (`mcpServers`) and restart the AI app.

```json
{
  "mcpServers": {
    "morpha": { "command": "C:\\…\\morpha-mcp.exe" }
  }
}
```

Then try: *“Make a 100 × 60 mm plate, 8 mm thick, with M6 tapped holes in the four corners.”*

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
