# SampleAgents

> **This repository is for training purposes only.**

A collection of [Sitecore Agentic Studio](https://www.sitecore.com) workflow definitions used in training and demos. All files are JSON — there is no application runtime code.

## Structure

```
Existing/   — agents exported directly from Sitecore Agentic Studio
Custom/     — sample workflows used as teaching examples
.vscode/    — VS Code MCP server configuration
```

### Existing/

These files are real agent definitions exported from a live Sitecore Agentic Studio environment. They represent the built-in agent catalog as-is and serve as authoritative reference templates. Do not modify them — treat them as read-only baselines.

### Custom/

These are hand-crafted sample workflows created for training purposes. They demonstrate how to build custom agents by following the patterns established in `Existing/`. Use them as teaching examples when learning how to compose new workflows.

## MCP Server Configuration

The `.vscode/mcp.json` file configures two MCP servers used during development:

| Server | URL |
|---|---|
| `marketer` | Sitecore Agentic Studio MCP (external auth — no credentials stored here) |
| `sitecore-docs` | Sitecore documentation MCP (SSE) |

Authentication for the `marketer` server is handled externally — no credentials are committed to this repo.

## Validating Workflow JSON

```powershell
Get-ChildItem Existing\*.json,Custom\*.json | ForEach-Object { Get-Content $_.FullName -Raw | ConvertFrom-Json | Out-Null }
```

Each workflow must include the top-level keys: `schema_version`, `id`, `actions`, `version`, `is_active`.
