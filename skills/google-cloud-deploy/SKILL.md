---
name: google-cloud-deploy
description: Deploy or update an application on Google Cloud Run or Compute Engine when the user requests a Google Cloud deployment.
---

# Google Cloud deployment

Use this skill only for Cloud Run services and Compute Engine VMs. Read the matching service reference below after the shared preflight. Use a Google Cloud integration only when its tools are present in the current session; inspect their actual descriptions and schemas. Never invent tool names or assume an MCP server is connected. If no suitable integration is available, use `gcloud` only when it is installed and authenticated, otherwise explain the concrete blocker.

## Shared preflight

1. Establish the requested operation and exact target from the user's request: project ID, service or VM/group name, and Cloud Run region or Compute Engine zone (or the correct regional scope for a regional managed instance group). Do not infer a target project from local defaults or credentials. If any required target is missing or ambiguous, ask for it before mutation.
2. Check which Google account/session is active without printing, copying, or requesting access tokens. Check the active project and location context, but pass the user's explicit project and location on every operation. A mismatch is a stop condition until resolved; never silently switch projects or deploy to another project.
3. Inspect the existing target and relevant configuration before changing it. For a new resource, check whether its name already exists and inspect the proposed image/artifact and required network, identity, and access settings. Confirm required APIs and permissions through available read-only evidence where possible.
4. Summarize the exact project, location, resource, and material impact to the user before the first write. If the user explicitly requested the live deployment and preflight matches that request, proceed without asking them to authorize it again. Stop and ask only when the target/impact differs from the request, preflight reveals a material destructive change, or an essential choice remains unresolved.
5. Keep credentials, tokens, and private configuration out of files, logs, and replies. Do not introduce project IDs or deployment defaults into this skill.

## Service workflows

- For a Cloud Run service, follow [Cloud Run](references/cloud-run.md).
- For a Compute Engine VM or managed instance group, follow [Compute Engine](references/compute-engine.md).

After execution, verify the live resource state and application-level health, report the concrete result, and describe the available rollback action. Do not claim success based only on an accepted API/CLI operation.
