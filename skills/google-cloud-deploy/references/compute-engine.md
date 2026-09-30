# Compute Engine deployment

Apply the shared preflight in `SKILL.md` first. A VM deployment can affect persistent disks, service identity, firewall exposure, and application availability; inspect those explicitly.

## Deploy and verify

1. Inspect the VM or managed instance group, machine type, boot and attached disks, image/template, network interfaces, firewall tags/rules, service account and scopes, startup metadata/scripts, and current health. Do not expose metadata values that may contain secrets. Confirm the requested zone or regional scope.
2. For a new VM, check name availability and use the connected integration's documented create operation or `gcloud compute instances create` with explicit `--project` and `--zone`, plus only the reviewed machine, image/template, network, identity, and disk options. For an existing standalone VM, prefer a reviewed replacement or image-based deployment plan over an unreviewed in-place mutation. For a managed instance group, create/use a new instance template and roll it out using the group's actual update policy; preserve its health checks, autoscaling, and disruption constraints.
3. Wait for the operation. Inspect the VM or group instances for expected status, zone, template/image, disks, and network identity. Confirm guest/application readiness using an available health check, service probe, or guest/serial logs appropriate to the workload. `RUNNING` by itself does not prove the application works.
4. Report the created/updated instance or group, image/template, zone/region, readiness evidence, and rollback route.

## Rollback

- For a managed instance group, retain the prior instance template and roll the group back to that template with the group's normal update safeguards. Verify that instances converge to the old template and pass health checks. Google documents rollback as a new rolling update request that names the old template.
- For a standalone VM, rollback depends on how it was deployed. Retain the prior image/template and configuration. If disk data must be restored, use a suitable pre-change snapshot or machine image and restore to a new disk/instance as appropriate; snapshots of running disks may not be application-consistent. Verify data and application health before directing traffic back. Do not delete or overwrite the only recovery copy.
- If no usable prior artifact or backup exists, do not promise an immediate rollback; state the recovery limitation before a risky update.

Official guidance: [Create and start a Compute Engine instance](https://cloud.google.com/compute/docs/instances/create-start-instance), [rolling out updates to managed instance groups](https://cloud.google.com/compute/docs/instance-groups/rolling-out-updates-to-managed-instance-groups), and [disk snapshots](https://cloud.google.com/compute/docs/disks/snapshots).
