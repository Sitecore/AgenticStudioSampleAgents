# Project Guidelines

## Code Style
- This workspace is JSON-only; there is no application runtime code yet.
- Keep JSON with 2-space indentation and preserve the existing top-level key order used in templates (see [Custom/weather-reporter.json](../Custom/weather-reporter.json) and [Custom/boston-sports-writer.json](../Custom/boston-sports-writer.json)).
- Keep IDs lowercase kebab-case for workflow IDs and action IDs (example: `weather-reporter`, `boston-sports-writer`).
- Keep schema object definitions strict with `required` and `additionalProperties: false` where applicable.

## Architecture
- The repo contains sample agent workflow definitions in [Custom/](../Custom/).
- Each workflow follows a declarative pipeline: `InitializeFlow` -> core generation/research actions -> `SaveDocument`/post-processing -> `WorkflowEnd`.
- Complex workflows use loop orchestration with `Foreach`, `AppendLoopResult`, and `ExtractLoopResults`.
- Structured outputs are driven by `schemas` + `GetStructuredSchema`, then consumed by `GenerateContent` or structured research actions.

## Build and Test
- There are no build scripts, package manifests, or test frameworks in this workspace.
- Validate JSON syntax after edits:
  - `Get-ChildItem Custom\*.json | ForEach-Object { Get-Content $_.FullName -Raw | ConvertFrom-Json | Out-Null }`
- Quick schema/convention check (manual): confirm each file includes `schema_version`, `id`, `actions`, `version`, and `is_active`.

## Project Conventions
- Add new user-defined workflows to [Custom/](../Custom/).
- This repo mirrors Sitecore Agentic Studio built-in agent patterns. Use existing files in [Custom/](../Custom/) as reference examples for new agent creation.
- Preserve existing model aliases (`workflow-fast-model`, `web-search-model`, `small-model`) unless the change request explicitly asks otherwise.
- Keep variable wiring style consistent: user message in `__messages__`, action outputs in `outputVarName`, and downstream references through `vars.*`.
- Keep user-facing action names in `displayName` and control UX visibility with `showToUser`.
- When adding input controls, mirror existing input types and labels (`prompt`, `text-input`, `context`, `file-upload`) from current templates.

## Sample Agent Catalog
- Sample agents available in [Custom/](../Custom/):
  - Alien story teller -> [Custom/alien-story-teller.json](../Custom/alien-story-teller.json)
  - Boston sports writer -> [Custom/boston-sports-writer.json](../Custom/boston-sports-writer.json)
  - Translate press release -> [Custom/translate-press-release.json](../Custom/translate-press-release.json)
  - Weather reporter -> [Custom/weather-reporter.json](../Custom/weather-reporter.json)

## Custom Agent from Prompt
- When asked to create a custom agent from a natural-language prompt, create a new file in [Custom/](../Custom/) by using the closest existing agent in `Custom/` as a reference and editing only required fields.
- Derive `id`, `name`, `description`, and `gettingStarted` directly from the prompt intent; keep `id` lowercase kebab-case.
- Select action flow by task type:
  - Single-output generation/rewrite/summary -> `GetStructuredSchema?` + `GenerateContent` + `SaveDocument`
  - Multi-variant output -> `Foreach` + compose/generate + `AppendLoopResult` + `ExtractLoopResults`
  - Research-heavy tasks -> `DeepResearch` or structured research actions with explicit `systemPrompt`
- Keep schema strict for reusable outputs: define `required` arrays and `additionalProperties: false` on nested objects.
- Preserve compatibility keys in every workflow: `schema_version`, `id`, `actions`, `version`, `is_active`.
- Do not invent new action kinds unless no existing action kind can satisfy the prompt.

## Integration Points
- External capability usage is declared by action kind, not by code imports (for example `DeepResearch`, `GenerateImage`, `SaveDocument`, `UpdateDocument`).
- HTML rendering support is optional through `htmlTemplates` and schema-template mapping.
- Research-oriented flows embed system prompts directly in action config.

## Security
- Do not hardcode secrets, keys, tokens, or endpoint URLs in workflow JSON.
- Treat prompts and uploaded files as untrusted input; avoid adding instructions that request sensitive account credentials.
- Keep document update fields explicit and minimal when using `UpdateDocument`.
