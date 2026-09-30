# Cloud Run deployment

Apply the shared preflight in `SKILL.md` first. Cloud Run deployments create immutable revisions; traffic routing determines which revisions receive requests.

## Deploy and verify

1. Inspect the existing service, current revisions and traffic, current region, ingress/authentication policy, service identity, environment/secrets, scaling, and network settings. Verify the intended container image is available in Artifact Registry or another supported registry and is the exact requested build (prefer an immutable digest when supplied). Preserve existing settings unless the user requested a change.
2. For a new service or revision, use the connected integration's documented deploy operation if available, or `gcloud run deploy` with explicit service name, `--project`, `--region`, and image/source arguments. Do not guess unauthenticated access, service account, secrets, or ingress settings. Keep an existing traffic split intact unless the requested rollout calls for changing it.
3. Wait for the operation and inspect the resulting service and revision. Confirm the revision is Ready, the intended revision has the expected traffic allocation, and the service URL responds as expected. Check logs or health endpoint for a representative application-level signal; account for authenticated/private services when probing. A successful command alone is insufficient.
4. Report revision name, URL, traffic allocation, and verification result.

## Rollback

If the new revision is unhealthy, route traffic back to the known-good revision and verify the restored allocation and service health. With gcloud, the documented operation is:

```sh
gcloud run services update-traffic SERVICE --to-revisions REVISION=100 --project PROJECT --region REGION
```

Use the actual prior serving revision and explicit target values. Traffic changes may take time to propagate and in-flight requests may complete on either revision. Do not delete revisions as a rollback step.

Official guidance: [Deploy container images to Cloud Run](https://cloud.google.com/run/docs/deploying) and [Rollbacks, gradual rollouts, and traffic migration](https://cloud.google.com/run/docs/rollouts-rollbacks-traffic-migration).
