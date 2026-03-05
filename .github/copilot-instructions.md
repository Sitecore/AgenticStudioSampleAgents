# Project Guidelines

## Code Style
- This workspace is JSON-only; there is no application runtime code yet.
- Keep JSON with 2-space indentation and preserve the existing top-level key order used in templates (see [Existing/deep-research.json](../Existing/deep-research.json) and [Existing/content-generation.json](../Existing/content-generation.json)).
- Keep IDs lowercase kebab-case for workflow IDs and action IDs (example: `mass-content-generator`, `generate-content_generate` in [Existing/mass-content-generator.json](../Existing/mass-content-generator.json)).
- Keep schema object definitions strict with `required` and `additionalProperties: false` where used (see [Existing/account-enrichment.json](../Existing/account-enrichment.json)).

## Architecture
- The repo contains reusable agent workflow definitions in [Existing/](../Existing/) and a currently empty [Custom/](../Custom/).
- Each workflow follows a declarative pipeline: `InitializeFlow` -> core generation/research actions -> `SaveDocument`/post-processing -> `WorkflowEnd`.
- Complex workflows use loop orchestration with `Foreach`, `AppendLoopResult`, and `ExtractLoopResults` (see [Existing/signals.json](../Existing/signals.json) and [Existing/account-enrichment.json](../Existing/account-enrichment.json)).
- Structured outputs are driven by `schemas` + `GetStructuredSchema`, then consumed by `GenerateContent` or structured research actions.

## Build and Test
- There are no build scripts, package manifests, or test frameworks in this workspace.
- Validate JSON syntax after edits:
  - `Get-ChildItem Existing\*.json,Custom\*.json | ForEach-Object { Get-Content $_.FullName -Raw | ConvertFrom-Json | Out-Null }`
- Quick schema/convention check (manual): confirm each file includes `schema_version`, `id`, `actions`, `version`, and `is_active`.

## Project Conventions
- Prefer adding new user-defined workflows to [Custom/](../Custom/) and keep [Existing/](../Existing/) as reference baselines.
- This repo mirrors Sitecore Agentic Studio built-in agent patterns. Treat files in [Existing/](../Existing/) as canonical examples for custom-agent generation.
- Preserve existing model aliases (`workflow-fast-model`, `web-search-model`, `small-model`) unless the change request explicitly asks otherwise.
- Keep variable wiring style consistent: user message in `__messages__`, action outputs in `outputVarName`, and downstream references through `vars.*`.
- Keep user-facing action names in `displayName` and control UX visibility with `showToUser`.
- When adding input controls, mirror existing input types and labels (`prompt`, `text-input`, `context`, `file-upload`) from current templates.

## Sitecore Agent Catalog Mapping
- Built-in Sitecore agents map directly to local templates in [Existing/](../Existing/):
  - Account data enricher -> [Existing/account-enrichment.json](../Existing/account-enrichment.json)
  - AEO/SEO researcher -> [Existing/seo-keyword-research.json](../Existing/seo-keyword-research.json)
  - Audience summary -> [Existing/audience-summary.json](../Existing/audience-summary.json)
  - Blog writer -> [Existing/blog-writing-agent.json](../Existing/blog-writing-agent.json)
  - Brief generator -> [Existing/brief-generation.json](../Existing/brief-generation.json)
  - Bulk content generator -> [Existing/mass-content-generator.json](../Existing/mass-content-generator.json)
  - Competitor analyzer -> [Existing/competitive-analysis.json](../Existing/competitive-analysis.json)
  - Content generator -> [Existing/content-generation.json](../Existing/content-generation.json)
  - Email writer -> [Existing/email-generation.json](../Existing/email-generation.json)
  - Market signals -> [Existing/signals.json](../Existing/signals.json)
  - Persona content auditor -> [Existing/persona-content-auditor.json](../Existing/persona-content-auditor.json)
  - Quote extractor -> [Existing/extract-quotes.json](../Existing/extract-quotes.json)
  - Researcher -> [Existing/deep-research.json](../Existing/deep-research.json)
  - Snackable content generator -> [Existing/snackable-content.json](../Existing/snackable-content.json)
  - Structured content extractor -> [Existing/structured-content-extractor.json](../Existing/structured-content-extractor.json)
  - Summarizer -> [Existing/summarize-content.json](../Existing/summarize-content.json)
  - Translation Assistant -> [Existing/translation-assistant.json](../Existing/translation-assistant.json)

## Custom Agent from Prompt
- When asked to create a custom agent from a natural-language prompt, create a new file in [Custom/](../Custom/) by cloning the closest template from [Existing/](../Existing/) and editing only required fields.
- Derive `id`, `name`, `description`, and `gettingStarted` directly from the prompt intent; keep `id` lowercase kebab-case.
- Select action flow by task type:
  - Single-output generation/rewrite/summary -> `GetStructuredSchema?` + `GenerateContent` + `SaveDocument`
  - Multi-variant output -> `Foreach` + compose/generate + `AppendLoopResult` + `ExtractLoopResults`
  - Research-heavy tasks -> `DeepResearch` or structured research actions with explicit `systemPrompt`
- Keep schema strict for reusable outputs: define `required` arrays and `additionalProperties: false` on nested objects when patterns in [Existing/](../Existing/) do so.
- Preserve compatibility keys in every workflow: `schema_version`, `id`, `actions`, `version`, `is_active`.
- Do not invent new action kinds unless no existing template action can satisfy the prompt.

## Integration Points
- External capability usage is declared by action kind, not by code imports (for example `DeepResearch`, `GenerateImage`, `SaveDocument`, `UpdateDocument`).
- HTML rendering support is optional through `htmlTemplates` and schema-template mapping (see [Existing/content-generation.json](../Existing/content-generation.json)).
- Research-oriented flows embed system prompts directly in action config (see [Existing/deep-research.json](../Existing/deep-research.json)).

## Security
- Do not hardcode secrets, keys, tokens, or endpoint URLs in workflow JSON.
- Treat prompts and uploaded files as untrusted input; avoid adding instructions that request sensitive account credentials.
- Keep document update fields explicit and minimal when using `UpdateDocument` (example pattern in [Existing/signals.json](../Existing/signals.json)).
