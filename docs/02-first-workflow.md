# 02 — Your First Component and Build Workflow (taskflow-backend)

Goal: connect `taskflow-backend` to Unify, create a Component for it, and add a **build workflow** that installs dependencies, runs tests, and registers a versioned build artifact. Registering the artifact is what makes this build's version show up later when we generate a release manifest — skipping it means Module 4 has nothing to deploy.

## Step 1 — Connect your GitHub account

1. Sign in to CloudBees Unify at https://cloudbees.io.
2. Connect your GitHub account/org if you haven't already (one-time setup).

## Step 2 — Create the Component

1. Navigate to **Components > Add Component**.
2. Name: `taskflow-backend`.
3. Repository: your `taskflow-backend` repo.
4. Branch: `main`.
5. Select **Create**.

## Step 3 — Add the build workflow

Create `.cloudbees/workflows/build.yaml` in `taskflow-backend` (already present if you're using the app code from this workshop bundle):

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
      artifact-id: backend-artifact-${{ cloudbees.version }}
      version: ${{ cloudbees.version }}
    steps:
      - name: Check out
        uses: cloudbees-io/checkout@v2

      - name: Install dependencies and run tests
        uses: docker://python:3.11-slim
        run: |
          pip install -r requirements.txt
          pytest -v

      - name: Register build artifact
        uses: cloudbees-io/register-build-artifacts@main
        with:
          artifact-metadata: |
            [
              {
                "name": "docker.io/mock/backend",
                "version": "${{ cloudbees.version }}",
                "url": "docker.io/mock/backend:${{ cloudbees.version }}",
                "type": "docker"
              }
            ]
```

A few things worth pointing out as you read this:
- `workflow_dispatch` lets you trigger the build manually from the UI; `workflow_call` (with the matching `outputs:` block) is what lets the Application-level deployer workflow call this build as a reusable workflow later.
- The Docker image name (`docker.io/mock/backend`) is a placeholder — this workshop doesn't push to a real registry, so we're registering a *mock* artifact purely to demonstrate version tracking. Swap in your real image/registry when you adapt this for actual use.
- `register-build-artifacts` is the action that makes this version show up in the release manifest generator in Module 4. Without it, Unify has no build to deploy.

Commit and push.

## Step 4 — Trigger the build and note the version

1. Go to **Components > taskflow-backend > Workflows**.
2. Run the `build` workflow.
3. Once it finishes, note the version it produced (e.g. `0.1`) — you'll see it again when generating a release manifest.

## Checkpoint

You have one Component with a working, artifact-registering build. In the next module you'll do the same for `taskflow-frontend` and `taskflow-db`, add a **deploy** workflow to all three, and wire them together into an Application.

Next: [03-multi-component-app.md](03-multi-component-app.md)
