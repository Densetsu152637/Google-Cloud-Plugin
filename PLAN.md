# Google Cloud plugin implementation plan

## Scope

Build a local Codex plugin for application deployment to Cloud Run and VM-based deployments on Compute Engine. These are the first two supported services because Google provides managed MCP servers for both. Do not add generic wrappers for other Google Cloud APIs.

## Work sequence

1. Package a valid plugin manifest and connect the official Cloud Run and Compute Engine remote MCP endpoints.
2. Add shared guidance for authentication, explicit project and location selection, preflight checks, execution, and post-deployment verification.
3. Add separate Cloud Run and Compute Engine workflows with service-specific checks and rollback guidance.
4. Document local setup, token renewal, limitations, and example prompts. Validate the plugin and review the final Git diff.

## Acceptance checks

- The plugin validator accepts the manifest and its referenced files.
- No credentials, tokens, project IDs, or deployment defaults are committed.
- Every deployment workflow requires a concrete project and region or zone, checks the existing resource, and verifies the resulting service or VM.
- Live deployment remains an explicit user-directed action. The plugin itself does not deploy anything during validation.
- Changes are recorded as small local commits; no remote operations are needed.

## Integration note

Google's remote MCP servers use OAuth and currently do not support dynamic client registration. The first version uses a bearer token supplied through an environment variable in a local Codex session. The token must be refreshed when it expires. The plugin does not manage credentials or promise an unattended authentication flow.
