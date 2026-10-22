# Workflow Export

Place the sanitized workflow export in this directory as `ai_sdr_lead_research_agent.json`.

Before committing the export:
1. Remove API keys, OAuth tokens, passwords, and other secrets.
2. Review account IDs, user IDs, workflow IDs, folder IDs, spreadsheet IDs, and connection/authentication identifiers before publishing.
3. Confirm the workflow JSON remains valid and imports correctly after any redaction.
4. Keep the workflow disabled until credentials, field mappings, and end-to-end tests have been checked.

The source export was reported as disabled/off, with all nodes paused and some issue flags. Do not describe it as production-ready until those issues are resolved and tests are recorded.

See [workflow documentation](../docs/workflow-documentation.md) for the intended stages, qualification model, setup checklist, and test plan.
