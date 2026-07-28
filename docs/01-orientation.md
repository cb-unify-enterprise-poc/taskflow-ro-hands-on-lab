# 01 — Orientation: How Unify Is Put Together

Let's spend some time learning the concepts before we start clicking, so the rest of the workshop makes sense as you go.

## The building blocks

**Organization** — the top-level container for your CloudBees Unify account. Everything below lives inside one.

**Component** — a Component wraps a single Git repository and is the container for that repo's CI **Workflows**, its automation config, and its security scanning. One repo = one Component. TaskFlow will have three: `taskflow-backend`, `taskflow-frontend`, `taskflow-db`.

**Workflow** — a YAML file that lives in a repo under `.cloudbees/workflows/`, defining jobs and steps (checkout, build, test, etc.). Authored either as raw YAML or in the visual workflow composer — they stay in sync. Workflows run automatically on commits (or manually, or on a schedule).

**Application** — groups several Components together (e.g. your backend + frontend + db) plus a set of **Environments**, so you can orchestrate and track a *release* that spans all three, rather than just one repo's CI run at a time. This is the "release orchestration" part of Unify.

**Environment** — a named deployment target (e.g. `dev`, `staging`, `production`) linked to an Application. Deployer/staged workflows promote artifacts through environments in order, optionally pausing for a manual approval gate.

**Feature flags** — created and evaluated in the context of an **Application** (not the whole Organization). A flag can be Boolean, String, or Number, and its value can differ per Environment. Your code calls an SDK to ask "is this flag on right now?" — Unify answers that in real time, without a new deploy.

## How this maps onto TaskFlow

```
Organization: your-org
└── Application: TaskFlow
    ├── Component: taskflow-backend   (Flask API)
    ├── Component: taskflow-frontend  (React UI)
    ├── Component: taskflow-db        (Postgres schema)
    ├── Environments: dev, staging, production
    └── Feature flags: show_due_date_banner, task_priority_label
```

Each Component gets its own CI workflow (Module 2 and 3). The Application ties them together for release orchestration (Module 4) and is the scope for feature flags (Module 5).

Next: [02-first-workflow.md](02-first-workflow.md)
