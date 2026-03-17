# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A collection of **Sitecore Agentic Studio** workflow definitions. All files are JSON — there is no application runtime code. `Custom/` holds sample and user-defined workflows used for training and demos.

## Validating JSON

No build or test framework exists. After editing, validate JSON syntax:

```powershell
Get-ChildItem Custom\*.json | ForEach-Object { Get-Content $_.FullName -Raw | ConvertFrom-Json | Out-Null }
```

Manual check: confirm each file includes the required top-level keys: `schema_version`, `id`, `actions`, `version`, `is_active`.

## Workflow Architecture

Each workflow is a declarative pipeline:

```
InitializeFlow → [DeepResearch | GetStructuredSchema] → GenerateContent → SaveDocument/UpdateDocument → WorkflowEnd
```

**Action flow selection by task type:**
- Single-output generation/summary → `GetStructuredSchema?` + `GenerateContent` + `SaveDocument`
- Multi-variant output → `Foreach` + compose/generate + `AppendLoopResult` + `ExtractLoopResults`
- Research-heavy tasks → `DeepResearch` with explicit `systemPrompt`

**Available action kinds** (do not invent new ones): `InitializeFlow`, `WorkflowEnd`, `DeepResearch`, `GenerateContent`, `GetStructuredSchema`, `ComposeMessage`, `SaveDocument`, `UpdateDocument`, `Foreach`, `AppendLoopResult`, `ExtractLoopResults`, `GenerateImage`.

## Conventions

- **IDs:** lowercase kebab-case (`weather-events-report`, `generate-content_generate`)
- **Indentation:** 2-space JSON; preserve top-level key order from templates
- **Model aliases:** always use `workflow-fast-model`, `web-search-model`, or `small-model` — never hardcode model names
- **Variable wiring:** user input → `__messages__`; action outputs → `outputVarName`; downstream references → `vars.*`
- **UX:** user-facing names go in `displayName`; visibility controlled by `showToUser`
- **Input types:** mirror existing patterns — `prompt`, `text-input`, `context`, `file-upload`
- **Schemas:** define `required` arrays and `additionalProperties: false` on nested objects for reusable structured outputs

## Creating Custom Agents

1. Create a new JSON file in `Custom/` following the workflow architecture below
2. Derive `id`, `name`, `description`, `gettingStarted` from the intent; keep `id` kebab-case
3. Use existing `Custom/` agents as reference examples; preserve compatibility keys

## Sample Agents in Custom/

| File | Description |
|---|---|
| [Custom/alien-story-teller.json](Custom/alien-story-teller.json) | Generates alien-themed stories |
| [Custom/boston-sports-writer.json](Custom/boston-sports-writer.json) | Writes Boston sports content |
| [Custom/translate-press-release.json](Custom/translate-press-release.json) | Translates press releases |
| [Custom/weather-reporter.json](Custom/weather-reporter.json) | Produces weather reports |

## Integration

- External capabilities are declared by action `kind`, not code imports
- HTML rendering uses `htmlTemplates` + schema-template mapping
- The MCP server (`marketer`) is configured in [.vscode/mcp.json](.vscode/mcp.json) with external auth — no credentials in workflow JSON
