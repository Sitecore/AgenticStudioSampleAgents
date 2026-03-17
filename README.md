# SampleAgents

> **This repository is for training purposes only.**

A collection of [Sitecore Agentic Studio](https://www.sitecore.com) workflow definitions used in training and demos. All files are JSON — there is no application runtime code.

## Structure

```
Custom/     — sample workflows used as teaching examples
.vscode/    — VS Code MCP server configuration
```

### Custom/

These are hand-crafted sample workflows created for training purposes. They demonstrate how to build custom agents for Sitecore Agentic Studio. Use them as teaching examples when learning how to compose new workflows.

Current agents:

| File | Description |
|---|---|
| `alien-story-teller.json` | Generates alien-themed stories |
| `boston-sports-writer.json` | Writes Boston sports content |
| `translate-press-release.json` | Translates press releases |
| `weather-reporter.json` | Produces weather reports |

## MCP Server Configuration

The `.vscode/mcp.json` file configures two MCP servers used during development:

| Server | URL |
|---|---|
| `marketer` | Sitecore Agentic Studio MCP (external auth — no credentials stored here) |
| `sitecore-docs` | Sitecore documentation MCP (SSE) |

Authentication for the `marketer` server is handled externally — no credentials are committed to this repo.

## Validating Workflow JSON

```powershell
Get-ChildItem Custom\*.json | ForEach-Object { Get-Content $_.FullName -Raw | ConvertFrom-Json | Out-Null }
```

Each workflow must include the top-level keys: `schema_version`, `id`, `actions`, `version`, `is_active`.
