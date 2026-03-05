# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A collection of **Sitecore Agentic Studio** workflow definitions. All files are JSON — there is no application runtime code. `Existing/` holds canonical reference templates mapping to Sitecore's built-in agents. `Custom/` holds user-defined workflows.

## Validating JSON

No build or test framework exists. After editing, validate JSON syntax:

```powershell
Get-ChildItem Existing\*.json,Custom\*.json | ForEach-Object { Get-Content $_.FullName -Raw | ConvertFrom-Json | Out-Null }
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

1. Clone the closest template from `Existing/` into `Custom/`
2. Derive `id`, `name`, `description`, `gettingStarted` from the intent; keep `id` kebab-case
3. Edit only required fields; preserve compatibility keys

## Sitecore Agent → Template Mapping

| Built-in Agent | File |
|---|---|
| Account data enricher | [Existing/account-enrichment.json](Existing/account-enrichment.json) |
| AEO/SEO researcher | [Existing/seo-keyword-research.json](Existing/seo-keyword-research.json) |
| Blog writer | [Existing/blog-writing-agent.json](Existing/blog-writing-agent.json) |
| Brief generator | [Existing/brief-generation.json](Existing/brief-generation.json) |
| Bulk content generator | [Existing/mass-content-generator.json](Existing/mass-content-generator.json) |
| Competitor analyzer | [Existing/competitive-analysis.json](Existing/competitive-analysis.json) |
| Content generator | [Existing/content-generation.json](Existing/content-generation.json) |
| Email writer | [Existing/email-generation.json](Existing/email-generation.json) |
| Market signals | [Existing/signals.json](Existing/signals.json) |
| Persona content auditor | [Existing/persona-content-auditor.json](Existing/persona-content-auditor.json) |
| Researcher | [Existing/deep-research.json](Existing/deep-research.json) |
| Snackable content generator | [Existing/snackable-content.json](Existing/snackable-content.json) |
| Structured content extractor | [Existing/structured-content-extractor.json](Existing/structured-content-extractor.json) |
| Summarizer | [Existing/summarize-content.json](Existing/summarize-content.json) |
| Translation Assistant | [Existing/translation-assistant.json](Existing/translation-assistant.json) |

## Integration

- External capabilities are declared by action `kind`, not code imports
- HTML rendering uses `htmlTemplates` + schema-template mapping (see [Existing/content-generation.json](Existing/content-generation.json))
- The MCP server (`marketer`) is configured in [.vscode/mcp.json](.vscode/mcp.json) with external auth — no credentials in workflow JSON
