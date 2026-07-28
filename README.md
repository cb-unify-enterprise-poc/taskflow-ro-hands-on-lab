# Welcome to the Unify Release Orchestration Hands-On Lab!
 
In this hands-on lab, you'll build out **TaskFlow** — a small task-management application made up of three components — and use CloudBees Unify to take it through the full release orchestration lifecycle: connecting repos, building and deploying each component, orchestrating a staged release across environments with an approval gate, and flipping a feature flag live without a redeploy.
 
No prior CloudBees Unify experience is assumed.
 
## What you'll do today
 
- Connect a repo to Unify and create your first Component with a real build workflow
- Add the rest of TaskFlow's components and wire them into an Application
- Run a staged release through DEV → STAGING → PROD, approving a gate along the way
- Create a feature flag in CloudBees Unify and flip it live in a running app — no redeploy required
## Before you start
 
Make sure you have:
 
- A **GitHub account**
- A **CloudBees Unify account** (your facilitator will confirm if one's already been provisioned for you)
That's it — no local installs needed. Everything runs in CloudBees Unify's own build/deploy containers, in your browser, or (for the Feature Management module) on a shared environment your facilitator hosts and gives you a link to. You won't need Docker, Python, Node.js, or even git on your own machine to complete this lab.
 
## Get your own copies of TaskFlow
 
Each of the four repos below is a **GitHub template repository** — you're not forking them, you're creating your own independent copy with no shared history back to the original.
 
For each repo:
1. Open the repo link below.
2. Select **Use this template > Create a new repository**.
3. Choose **your own GitHub account** as the owner.
4. **Keep the repository name exactly as listed below** — the Application-level workflow in `app-taskflow` references the other three repos by name, so renaming any of them will break that reference.
5. Select **Create repository**.

| Repo | What it's for |
|---|---|
| [taskflow-frontend](https://github.com/cb-unify-enterprise-poc/taskflow-frontend) | The React UI for TaskFlow — the app you'll be releasing today. |
| [taskflow-backend](https://github.com/cb-unify-enterprise-poc/taskflow-backend) | The Flask API behind TaskFlow, including the feature-flagged endpoint you'll wire up in the Feature Management module. |
| [taskflow-db](https://github.com/cb-unify-enterprise-poc/taskflow-db) | The Postgres schema for TaskFlow's task data. |
| [app-taskflow](https://github.com/cb-unify-enterprise-poc/app-taskflow) | No application source code — this repo holds the Application-level release-orchestration workflows that tie the other three components together into a single release. |


## Where to go next

Follow the modules in order — each one builds on the last:

1. [00-prerequisites.md](docs/00-prerequisites.md) — confirm your setup and get TaskFlow running locally, before touching Unify at all
2. [01-orientation.md](docs/01-orientation.md) — a primer on how Unify's Organizations, Components, Applications, and Environments fit together
3. [02-first-workflow.md](docs/02-first-workflow.md) — connect `taskflow-backend` and run your first build
4. [03-multi-component-app.md](docs/03-multi-component-app.md) — add the rest of the components and create the Application
5. [04-release-orchestration.md](docs/04-release-orchestration.md) — run a staged release with an approval gate
6. [05-feature-flags.md](docs/05-feature-flags.md) — create and flip a feature flag live
7. [06-wrap-up-next-steps.md](docs/06-wrap-up.md) — recap and where to go deeper

*(Ask your facilitator where these are hosted for this session if you don't see them linked directly from here.)*

## Questions during the lab

Don't struggle silently — flag your facilitator. If something in the Unify UI doesn't match a screenshot exactly, that's expected (the product moves fast); the underlying concept will still be right.
