# 03 — Frontend, DB, Deploy Workflows, and the Application

Goal: bring `taskflow-frontend` and `taskflow-db` up to the same state as the backend (Component + build workflow), add a **deploy workflow** to all three components, then create the **Application** that groups them and can orchestrate a release across all three.

## Step 1 — Create the frontend Component and build workflow

1. **Components > Create Component** → name `taskflow-frontend`, repo `taskflow-frontend`, branch `main`.
2. Add `.cloudbees/workflows/build.yaml`:

```yaml
apiVersion: automation.cloudbees.io/v1alpha1
kind: workflow
name: build

on:
  workflow_dispatch:
  workflow_call:
    outputs:
      artifact-id:
        value: ${{ jobs.build.outputs.artifact-id }}
      version:
        value: ${{ jobs.build.outputs.version }}

jobs:
  build:
    outputs:
      artifact-id: frontend-artifact-${{ cloudbees.version }}
      version: ${{ cloudbees.version }}
    steps:
      - name: Check out
        uses: cloudbees-io/checkout@v2

      - name: Install and build
        uses: docker://node:18-slim
        run: |
          npm install
          npm run build

      - name: Register build artifact
        uses: cloudbees-io/register-build-artifacts@main
        with:
          artifact-metadata: |
            [
              {
                "name": "docker.io/mock/frontend",
                "version": "${{ cloudbees.version }}",
                "url": "docker.io/mock/frontend:${{ cloudbees.version }}",
                "type": "docker"
              }
            ]
```

3. Commit, push, and run the build workflow once so a version exists.

## Step 2 — Create the db Component and build workflow

1. **Components > Add Component** → name `taskflow-db`, repo `taskflow-db`, branch `main`.
2. Add `.cloudbees/workflows/build.yaml` (this one lints the schema rather than compiling code):

```yaml
apiVersion: automation.cloudbees.io/v1alpha1
kind: workflow
name: build

on:
  workflow_dispatch:
  workflow_call:
    outputs:
      artifact-id:
        value: ${{ jobs.build.outputs.artifact-id }}
      version:
        value: ${{ jobs.build.outputs.version }}

jobs:
  build:
    outputs:
      artifact-id: db-artifact-${{ cloudbees.version }}
      version: ${{ cloudbees.version }}
    steps:
      - name: Check out
        uses: cloudbees-io/checkout@v2

      - name: Lint schema
        uses: docker://python:3.11-slim
        run: |
          pip install sqlfluff
          sqlfluff lint --dialect postgres init.sql

      - name: Register build artifact
        uses: cloudbees-io/register-build-artifacts@main
        with:
          artifact-metadata: |
            [
              {
                "name": "docker.io/mock/database",
                "version": "${{ cloudbees.version }}",
                "url": "docker.io/mock/database:${{ cloudbees.version }}",
                "type": "docker"
              }
            ]
```

3. Commit, push, and run it.

**You must build all three components before creating a release** — the `register-build-artifacts` step is what makes a version available in Module 4's manifest generator.

## Step 3 — Add a deploy workflow to each component

Each component also needs `.cloudbees/workflows/deploy.yaml` — a reusable workflow the Application will call once per environment. Backend shown (frontend/db follow the same shape with their own artifact name):

```yaml
apiVersion: automation.cloudbees.io/v1alpha1
kind: workflow
name: deploy

on:
  workflow_call:
    inputs:
      environment:
        type: string
        required: true
      artifact-id:
        type: string
        required: true
      version:
        type: string
        required: true

jobs:
  deploy:
    steps:
      - name: "Deploy backend"
        uses: docker://alpine:latest
        run: |
          echo "Deploying docker.io/mock/backend:${{ inputs.version }} to ${{ inputs.environment }}"
          # Replace with a real deploy step for your infrastructure.

      - name: Publish evidence
        uses: cloudbees-io/publish-evidence-item@v1
        with:
          content: |-
            ## Deployed environment
            **Component:** taskflow-backend
            **Version:** ${{ inputs.version }}
            **Environment:** ${{ inputs.environment }}
          format: MARKDOWN

      - name: Register deployed artifact
        uses: cloudbees-io/register-deployed-artifact@v2
        with:
          artifact-id: ${{ inputs.artifact-id }}
          target-environment: ${{ inputs.environment }}
```

Commit and push `deploy.yaml` to all three component repos.

## Step 4 — Create Environments (No action needed here)

Environments are created once at the **organization** level, then linked to whichever Applications need them — they aren't created "inside" an Application. (THIS STEP HAS ALREADY BEEN COMPLETED PRIOR TO THE LAB)

1. Go to your organization's **Configurations > Environments**.
2. Select **Create environment** and create `DEV`.
3. Repeat for `STAGING` and `PROD`.

If you already have environments with these names from a previous Application in this org, you can reuse them — you don't need a fresh set per Application.

## Step 5 — Verify that the Application repository has been copied over to your local GitHub

1. Quick check to ensure you copied over `app-taskflow` at the start of the session. (It contains a README only, no source — this repo just carries the Application-level orchestration workflows).
2. If you do not have this, follow the steps in this repositories README.md file for instructions on how to copy it.

## Step 6 — Create the Application in Unify

1. **Applications > Create Application**.
2. Name: `app-taskflow`. Repository: `app-taskflow`. Branch: `main`.
3. Select **Add environment**, and add `DEV`, `STAGING`, and `PROD` — one at a time — from the environments you created in Step 4. This is what links them to this Application; without it, they won't show up later when you're setting up feature flags or a release.
4. Select **Create**.
5. Open the `app-taskflow` Application, go to **Components > Add Component**, and add `taskflow-db`, `taskflow-backend`, and `taskflow-frontend`.

A Component can only belong to **one** Application at a time — once assigned here, it can't also be added to a different Application.

If you skip linking environments here, you can still add them later: open the Application, select the **⋮** menu > **Edit** > **Add environment**.

## Step 7 — Add the deployer workflow

`app-taskflow`'s `.cloudbees/workflows/deployer.yaml` is what actually calls each component's `deploy.yaml`, in order, driven by a manifest:

```yaml
apiVersion: automation.cloudbees.io/v1alpha1
kind: workflow
name: deployer

on:
  workflow_call:
    inputs:
      environment:
        type: string
        required: true
      manifest:
        type: string
        required: true

jobs:
  taskflow-db:
    if: ${{ fromJSON(inputs.manifest)['taskflow-db']['docker.io/mock/database'].deploy }}
    uses: <YOUR_GITHUB_ORG>/taskflow-db/.cloudbees/workflows/deploy.yaml
    vars: inherit
    secrets: inherit
    with:
      artifact-id: ${{ fromJSON(inputs.manifest)['taskflow-db']['docker.io/mock/database'].id }}
      version: ${{ fromJSON(inputs.manifest)['taskflow-db']['docker.io/mock/database'].version }}
      environment: ${{ inputs.environment }}

  taskflow-backend:
    needs: taskflow-db
    if: ${{ fromJSON(inputs.manifest)['taskflow-backend']['docker.io/mock/backend'].deploy }}
    uses: <YOUR_GITHUB_ORG>/taskflow-backend/.cloudbees/workflows/deploy.yaml
    vars: inherit
    secrets: inherit
    with:
      artifact-id: ${{ fromJSON(inputs.manifest)['taskflow-backend']['docker.io/mock/backend'].id }}
      version: ${{ fromJSON(inputs.manifest)['taskflow-backend']['docker.io/mock/backend'].version }}
      environment: ${{ inputs.environment }}

  taskflow-frontend:
    needs: taskflow-backend
    if: ${{ fromJSON(inputs.manifest)['taskflow-frontend']['docker.io/mock/frontend'].deploy }}
    uses: <YOUR_GITHUB_ORG>/taskflow-frontend/.cloudbees/workflows/deploy.yaml
    vars: inherit
    secrets: inherit
    with:
      artifact-id: ${{ fromJSON(inputs.manifest)['taskflow-frontend']['docker.io/mock/frontend'].id }}
      version: ${{ fromJSON(inputs.manifest)['taskflow-frontend']['docker.io/mock/frontend'].version }}
      environment: ${{ inputs.environment }}
```

Replace `<YOUR_GITHUB_ORG>` with your actual GitHub username/org in all three `uses:` lines. Note the `needs:` chain — db deploys before backend, backend before frontend — and the `if:` guard, which lets a release deploy only the components actually included in the manifest.

Commit and push.

## Checkpoint

You now have three Components, each buildable and deployable on its own, plus an Application that knows how to call all three deploy workflows together from one manifest. Next: the staged release workflow that drives DEV → approval → STAGING → PROD.

Next: [04-release-orchestration.md](04-release-orchestration.md)
