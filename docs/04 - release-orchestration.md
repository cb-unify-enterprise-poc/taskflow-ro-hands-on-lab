# 04 — Release Orchestration: A Staged Release with an Approval Gate

Goal: add the release workflow to `app-taskflow`, then actually run a release: generate a manifest, watch it deploy to DEV, approve the gate, and watch it continue through STAGING and PROD.

## Step 1 — Add the release workflow

Add `.cloudbees/workflows/release-wf.yaml` to `app-taskflow`:

```yaml
apiVersion: automation.cloudbees.io/v1alpha1
kind: workflow
name: Release WF

on:
  workflow_dispatch:
    inputs:
      manifest:
        type: string
        required: true
        description: System-generated manifest for releases

metadata:
  stages/v1alpha1:
    - name: DEV
      jobs:
        - DEV
        - Approve
    - name: STAGING
      jobs:
        - STAGING
    - name: PROD
      jobs:
        - PROD

jobs:
  DEV:
    environment: DEV
    uses: ./.cloudbees/workflows/deployer.yaml
    vars: inherit
    secrets: inherit
    with:
      manifest: ${{ inputs.manifest }}
      environment: ${{ job.environment }}

  Approve:
    needs: DEV
    timeout-minutes: 4320
    delegates: cloudbees-io/manual-approval/custom-job.yml@v1
    with:
      disallowLaunchByUser: false
      notifyAllEligibleUsers: false
      approvers: Admins (System)
      approvalInputs: |
        Comment:
          type: string

  STAGING:
    needs: Approve
    environment: STAGING
    uses: ./.cloudbees/workflows/deployer.yaml
    vars: inherit
    secrets: inherit
    with:
      manifest: ${{ inputs.manifest }}
      environment: ${{ job.environment }}

  PROD:
    needs: STAGING
    environment: PROD
    uses: ./.cloudbees/workflows/deployer.yaml
    vars: inherit
    secrets: inherit
    with:
      manifest: ${{ inputs.manifest }}
      environment: ${{ job.environment }}
```

For Unify to recognize this as a **release workflow** (selectable when creating a release), it must have:
- a `workflow_dispatch` trigger
- a `manifest` input (`type: string`, `required: true`)
- `metadata.stages/v1alpha1` defining the stages
- sequential `needs` dependencies between the stage jobs

Commit and push. If it doesn't show up as a release workflow option right away, give Unify 2–3 minutes to rescan.

## Step 2 — Create a release

1. **Applications > app-taskflow > Releases > Create Release**.
2. Release Name: `Release 0.1`.
3. Release Workflow: select `Release WF`.

## Step 3 — Generate the manifest

1. Select **Generate Manifest**.
2. Check both — all three — components (`taskflow-db`, `taskflow-backend`, `taskflow-frontend`).
3. Select the version you built in Modules 2–3 (e.g. `0.1`) for each.
4. Select **Apply Manifest**.

## Step 4 — Save and run

1. Review the release configuration.
2. Select **Create Release**.
3. Select **Run Release**.

## Step 5 — Watch it move through the stages

1. Open the running release.
2. Watch: DEV deploys automatically → the release pauses at the approval gate → after approval, STAGING deploys → then PROD deploys.

## Step 6 — Approve the gate

When it pauses at `Approve`:

1. Select the **Approve** job.
2. Review the DEV deployment results.
3. Select **Approve**, enter a comment (e.g. "DEV deployment successful"), and select **Submit**.

The release continues on to STAGING and PROD on its own.

## Step 7 — Check the deployment inventory

**Applications > app-taskflow > Inventory** now shows what's deployed where:

| Component | DEV | STAGING | PROD |
|---|---|---|---|
| taskflow-db | v0.1 | v0.1 | v0.1 |
| taskflow-backend | v0.1 | v0.1 | v0.1 |
| taskflow-frontend | v0.1 | v0.1 | v0.1 |

Worth saying out loud to the room: **Release Orchestration coordinates deployment execution — it doesn't perform the deployment itself.** The actual deploy logic lives entirely inside each component's `deploy.yaml`, which means you can point it at any deployment platform or tooling you already use.

## Troubleshooting

**Workflow not appearing as a Release Workflow option** — check it has `workflow_dispatch`, a required `manifest` string input, and give Unify 2–3 minutes to rescan.

**Component versions not appearing in the manifest generator** — confirm the build workflow actually ran, and check its logs for the `register-build-artifacts` step. Re-run the build if needed.

**Stages not executing in order** — check the `needs:` chain (`STAGING needs Approve`, `PROD needs STAGING`).

**No one can approve the release** — confirm the user is in the approvers group referenced in `Approve` (`Admins (System)` here), and check group membership under **Settings > Teams**.

## Checkpoint

You've now run one release across all three components, through three environments, with a real human approval gate before production. Next up: making one piece of behavior controllable live, completely outside this release pipeline.

Next: [05-feature-flags.md](05-feature-flags.md)
