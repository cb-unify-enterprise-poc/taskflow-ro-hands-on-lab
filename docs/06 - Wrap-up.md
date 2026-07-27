# 06 — Wrap-Up

## What you built

- Three Unify Components, each with a real CI workflow (`taskflow-backend`, `taskflow-frontend`, `taskflow-db`)
- One Application (`TaskFlow`) grouping them, with `dev`/`staging`/`production` Environments
- A release that moved through all three environments with a manual approval gate before production
- Two feature flags, live-wired into the Flask backend via the official Python SDK, toggled without a redeploy

## Where to go from here

- **Security**: [Get started with security scanning](https://docs.cloudbees.com/docs/cloudbees-unify/latest/application-security/tutorials/add-security-scanning) — SAST/SCA/secret/container scanning on the same Components you just built.
- **Analytics**: [Analyze DORA metrics](https://docs.cloudbees.com/docs/cloudbees-unify/latest/analytics-insights/how-to-guides/use-dora-metrics-dashboard) and [Monitor flow metrics](https://docs.cloudbees.com/docs/cloudbees-unify/latest/analytics-insights/how-to-guides/use-flow-metrics-dashboard) — now that you have real workflow runs, these dashboards have data to show.
- **Targeting**: [Configure feature flag targeting](https://docs.cloudbees.com/docs/cloudbees-unify/latest/feature-management/how-to-guides/configure-feature-flag-targeting) and [Target groups](https://docs.cloudbees.com/docs/cloudbees-unify/latest/api-references/api-target-groups) — percentage rollouts and named cohorts instead of all-or-nothing flags.
- **GitHub Actions integration**: if your org's real repos already run on GitHub Actions, [Understanding GitHub Actions integration](https://docs.cloudbees.com/docs/cloudbees-unify/latest/continuous-integration/explanation/understanding-github-actions-integration) covers running Unify alongside (or triggered by) existing GHA pipelines rather than replacing them outright.
- **Frontend flags**: TaskFlow's frontend today just reflects a flag value returned by the backend. If you want the browser itself to evaluate flags directly, see [Build your first feature-flagged web application](https://docs.cloudbees.com/docs/cloudbees-unify/latest/feature-management/tutorials/web-application-examples) and [Install client-side SDKs](https://docs.cloudbees.com/docs/cloudbees-unify/latest/feature-management/how-to-guides/install-client-side-sdks).

## Feedback

If anything in this workshop didn't match what you saw in your Unify org, that's useful signal — note it down so the materials can be corrected for the next cohort.
