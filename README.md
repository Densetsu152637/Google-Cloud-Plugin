# Google Cloud Plugin

This local Codex plugin connects to Google's remote MCP servers for Cloud Run and Compute Engine. It lets Codex inspect resources and, when you explicitly request it, perform supported deployment actions through those services.

## Requirements

- A current Codex installation that can load a local plugin.
- Google Cloud CLI (`gcloud`) installed and authenticated as the identity you intend to use.
- A Google Cloud project with the Cloud Run Admin API and/or Compute Engine API enabled, and IAM permissions for the requested operations. Google documents the MCP Tool User role (`roles/mcp.toolUser`) for MCP tool access; service-specific IAM permissions are also required.
- A bearer token supplied to the local Codex session as `GOOGLE_CLOUD_MCP_TOKEN`.

Google's remote MCP servers currently do not support Dynamic Client Registration (DCR) or OAuth Client ID Metadata Documents (CIMD). This plugin does not provide an interactive OAuth login or renew tokens. Create a token with `gcloud auth print-access-token`, set it in the environment before starting Codex, and load this plugin from the local repository using Codex's local plugin installation flow. For PowerShell:

```powershell
$env:GOOGLE_CLOUD_MCP_TOKEN = (gcloud auth print-access-token)
codex
```

The token is short lived (normally one hour). When it expires, get a fresh token, update the environment, and restart the Codex session so the MCP connection uses it. Do not paste tokens into prompts, commit them, or store them in this repository. The plugin configuration expects the bearer token in `GOOGLE_CLOUD_MCP_TOKEN`.

## Install from this local checkout

From the repository root, register the included local marketplace and install the plugin:

```powershell
codex plugin marketplace add .
codex plugin add google-cloud --marketplace google-cloud-local
```

The marketplace points to this checkout, so Codex loads the plugin files directly from this directory. The plugin includes the `google-cloud-deploy` skill for Cloud Run and Compute Engine deployments and the `cloud-run-troubleshoot` skill for read-only diagnosis of existing Cloud Run services. Review each skill before authorizing changes. To confirm the installation, run `codex plugin list`.

The official MCP setup guidance also shows an optional `x-goog-user-project` header for quota attribution. Use a project you are authorized to bill for quota when the request requires it; this does not select the target deployment project. See Google's [MCP authentication setup](https://docs.cloud.google.com/mcp/set-up-authentication-mcp-servers).

## Endpoints and location selection

The plugin uses Google's global endpoints:

- Cloud Run: `https://run.googleapis.com/mcp`
- Compute Engine: `https://compute.googleapis.com/mcp`

For each task, give Codex the target **project ID** and **location** explicitly. Cloud Run needs a region (for example, `us-central1`); Compute Engine needs a zone (for example, `us-central1-a`). A quota project, if configured, is separate from this target project. Before a deployment, ask Codex to inspect the existing service or instance, confirm the proposed settings, and verify the resulting resource after the operation.

## Example requests

Replace the placeholders with your own values; the plugin has no built-in project, region, or zone defaults.

- “Inspect Cloud Run service `SERVICE` in project `PROJECT_ID`, region `REGION`, then deploy the current source there after showing me the planned changes.”
- “Check whether VM `INSTANCE` exists in project `PROJECT_ID`, zone `ZONE`. If I approve, create it with the specified machine type and image, then verify the operation and instance.”
- “List Cloud Run services in project `PROJECT_ID` and region `REGION`.”

Cloud Run MCP setup and endpoint details are in Google's [Cloud Run MCP guide](https://docs.cloud.google.com/run/docs/use-cloud-run-mcp). Compute Engine endpoint and available tools are in the [Compute Engine MCP guide](https://docs.cloud.google.com/compute/docs/use-compute-engine-mcp) and [MCP reference](https://docs.cloud.google.com/compute/docs/reference/mcp).

## Validation and live operations

Local plugin validation checks the package and configuration without deploying cloud resources. It does not prove that credentials, IAM roles, APIs, billing, quotas, project selection, or live endpoint access are correct. No live deployment test is performed as part of validation; that requires valid credentials and an explicit deployment request.
