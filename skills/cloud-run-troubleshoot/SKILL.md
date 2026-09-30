---
name: cloud-run-troubleshoot
description: Diagnose runtime, availability, latency, and connectivity problems in an existing Google Cloud Run service.
---

# Troubleshoot Cloud Run services

Use this skill for an existing Cloud Run service that is failing, slow, or unexpectedly unreachable. For a requested deployment or rollout, use `google-cloud-deploy` instead. Keep investigation read-only unless the user explicitly asks for remediation; apply the deployment skill's preflight before any deployment or traffic change.

## Establish the incident scope

1. Get the exact project ID, region, service, affected time window and timezone, observed symptom, and a representative request or trace ID when available. Do not infer a project or region from local defaults. If a necessary target is unknown, ask before querying.
2. Inspect the service's current revision and traffic allocation, recent revisions and configuration changes, ingress and authentication settings, and relevant health or startup configuration. Use available Cloud Run tools only when present, and inspect their actual descriptions and schemas. Otherwise use read-only `gcloud` or console inspection with explicit project and region.
3. Compare the failure window with request, container, and system logs and Cloud Monitoring metrics. Filter to the service and revision, align timestamps and timezone, and avoid dumping unrelated log entries or secret-bearing environment and metadata values. Use traces when available to separate service latency from upstream dependency latency.
4. Classify evidence before proposing a cause:
   - Startup or readiness failures: inspect revision/system logs, container startup, listening port, health checks, and resource limits.
   - HTTP errors: compare request logs and container logs by revision, status, request ID, and time; distinguish application errors from platform or upstream errors.
   - 429s, timeouts, or latency: inspect request volume, instance counts and limits, concurrency, startup/cold-start behavior, request duration, and dependency traces.
   - Connectivity or authorization failures: inspect ingress, IAM/authentication, VPC egress, DNS, and the caller's identity and path. Do not make a service public as a diagnostic shortcut.
5. Present the strongest evidence, competing explanations, and a specific next check or low-risk fix. Mark conclusions as hypotheses when evidence is incomplete. Check Google Cloud known issues or service health when platform-side symptoms are plausible.

## Remediation and closeout

- Do not change traffic, service configuration, IAM, networking, secrets, or revisions during diagnosis. If the user requests a fix, state the exact proposed change and impact before the first write; proceed only when the request clearly covers that change. If a deployment or traffic rollback is needed, follow `google-cloud-deploy` and its Cloud Run reference.
- After an authorized change, check the new revision's readiness, traffic allocation, relevant error/latency metrics, and an appropriate health signal over a comparable interval. Stop and report if the signal worsens; do not make repeated speculative changes.
- Summarize the time window and target, evidence inspected, confirmed findings versus hypotheses, any action taken, and remaining uncertainty. Never include tokens, secret values, or unredacted sensitive logs.

## Official references

- [Troubleshoot Cloud Run issues](https://docs.cloud.google.com/run/docs/troubleshooting)
- [Logging and viewing logs in Cloud Run](https://docs.cloud.google.com/run/docs/logging)
- [Monitor health and performance](https://docs.cloud.google.com/run/docs/monitoring)
- [Cloud Run known issues](https://docs.cloud.google.com/run/docs/known-issues)
