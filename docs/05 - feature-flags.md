# 05 — Feature Flags: Wiring TaskFlow's Backend to Unify Feature Management

Goal: create two feature flags in CloudBees Unify, connect `taskflow-backend` to them with the official Python SDK, and change the app's behavior live — no redeploy, no new commit.

TaskFlow's backend already ships with the flag code in place (see `app/backend/flags.py`) so we can focus on the Unify side and the "aha" moment of toggling something live. If you're doing this for real from scratch later, [Install server-side SDKs](https://docs.cloudbees.com/docs/cloudbees-unify/latest/feature-management/how-to-guides/install-server-side-sdks) is the reference.

## Step 1 — Get an SDK key

1. In your `TaskFlow` Application, go to **Feature management** > **Flags**.
2. If prompted, link a `dev` environment for feature management (separate from the release-orchestration Environments you made earlier, though the names can overlap).
3. Copy the **SDK key** shown for that environment.

## Step 2 — Look at the flag code (already written for you)

Open `app/backend/flags.py`:

```python
import os

from rox.server.rox_server import Rox
from rox.server.flags.rox_flag import RoxFlag
from rox.core.entities.rox_string import RoxString


class TaskFlowFlags:
    def __init__(self):
        # Boolean flag: show a banner in the UI, driven from the backend.
        self.show_due_date_banner = RoxFlag(False)
        # String flag: label shown next to each task.
        self.task_priority_label = RoxString("medium", ["low", "medium", "high"])


flags = TaskFlowFlags()


def init_flags():
    """Register flags with Unify. Falls back to the defaults above if
    ROX_SDK_KEY isn't set, so the app still runs before this module."""
    Rox.register(flags)
    sdk_key = os.environ.get("ROX_SDK_KEY")
    if not sdk_key:
        print("ROX_SDK_KEY not set — using local default flag values.")
        return

    try:
        # Rox.setup() returns a Future-like object; .result() blocks until
        # the SDK has fetched its initial flag configuration.
        Rox.setup(sdk_key).result()
    except Exception as exc:  # pragma: no cover
        print(f"Feature flag setup failed, using defaults: {exc}")
```

And in `app.py`, the `/api/meta` endpoint just reads the flag values:

```python
@app.route("/api/meta")
def meta():
    return jsonify(
        show_due_date_banner=flags.show_due_date_banner.is_enabled(),
        task_priority_label=flags.task_priority_label.get_value(),
    )
```

The frontend already calls `/api/meta` and shows a banner / priority label based on the response (see `app/frontend/src/App.jsx`).

## Step 3 — Run the backend with your SDK key

```bash
cd app/backend
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
export ROX_SDK_KEY="<paste your SDK key>"
export DATABASE_URL="postgresql+psycopg://taskflow:taskflow@localhost:5432/taskflow"
python app.py
```

(Keep Postgres running from `docker compose up db` if you stopped it earlier.)

Hit the meta endpoint once so the SDK registers the flags with Unify:

```bash
curl http://localhost:8000/api/meta
```

## Step 4 — Confirm the flags showed up in Unify

Back in **Feature management** > **Flags** for your `TaskFlow` Application, you should now see `show_due_date_banner` (Boolean) and `task_priority_label` (String) — auto-detected the moment the SDK evaluated them for the first time. If you don't see them yet, hit `/api/meta` again; registration happens on first evaluation, per [Build your first feature-flagged backend service](https://docs.cloudbees.com/docs/cloudbees-unify/latest/feature-management/tutorials/backend-service-examples).

## Step 5 — Flip a flag live

1. Next to `show_due_date_banner`, select the menu > **Configure**.
2. Pick your environment, change the value to `true`, select **Save**, and make sure **Configuration status** is **On**.
3. Refresh `curl http://localhost:8000/api/meta` (or reload the frontend at http://localhost:5173 if you're running it too) — the banner value flips within a few seconds, with no restart and no redeploy.
4. Do the same with `task_priority_label`, setting it to `"high"`.

## Step 6 — Talk about why this matters

- The value changed **without a new build, workflow run, or approval gate** — this is the point of separating "is the code deployed" from "is the feature turned on."
- [Configure feature flag targeting](https://docs.cloudbees.com/docs/cloudbees-unify/latest/feature-management/how-to-guides/configure-feature-flag-targeting) lets you go further: turn a flag on for 10% of traffic, or only for a specific target group, instead of all-or-nothing.
- Flags are scoped to the Application and can have different values per Environment — so `production` can stay `false` while you test `true` in `dev`.

## Checkpoint

You've created flags in Unify, connected a real backend service to them with the server-side SDK, and changed live behavior with no deploy. That's the full loop: CI (Module 2–3) → release orchestration (Module 4) → feature management (Module 5).

Next: [06-wrap-up-next-steps.md](06-wrap-up-next-steps.md)
