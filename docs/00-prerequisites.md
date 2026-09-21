# 00 — Prerequisites & Setup

Do this **before** the workshop starts (or in the first 15 minutes as a group).

## Accounts

- A [CloudBees Unify](https://cloudbees.io/signup) account, or a sub-org your facilitator has already provisioned for you.
- A GitHub account.

## Local tools

**None required.** Everything in this workshop runs either in CloudBees Unify's own build/deploy containers, in your browser, or (for Module 5's live flag demo) on an environment your facilitator hosts and shares a link to. 

If you'd like to also run TaskFlow on your own machine for further exploration after the workshop, see the note at the end of this doc — but it's optional, not something you need today.

## Get your own copies of TaskFlow

Each of the four repos below is a **GitHub template repository**:

| Repo | What it's for |
|---|---|
| [taskflow-frontend](https://github.com/cb-unify-enterprise-poc/taskflow-frontend) | The React UI for TaskFlow. |
| [taskflow-backend](https://github.com/cb-unify-enterprise-poc/taskflow-backend) | The Flask API behind TaskFlow. |
| [taskflow-db](https://github.com/cb-unify-enterprise-poc/taskflow-db) | The Postgres schema for TaskFlow's task data. |
| [app-taskflow](https://github.com/cb-unify-enterprise-poc/app-taskflow) | No application source code — this repo holds the Application-level release-orchestration workflows that tie the other three components together. |

For each one:
1. Open the repo link.
2. Select **Use this template > Create a new repository**.
3. Choose **your own GitHub account** as the owner.
4. **Keep the repository name exactly as given** — `app-taskflow`'s workflow references the other three by name, so renaming any of them breaks that reference.
5. Select **Create repository**.

This all happens in the GitHub website — no local git required.

## Creating and editing files without git

Later modules will have you add new files, such as `.cloudbees/workflows/build.yaml`, directly to your GitHub repos. You don't need git installed locally to do this — GitHub's web UI handles both creating and committing:

**To create a new file:**
1. Open the repo on github.com and navigate to the folder it belongs in (e.g. `.cloudbees/workflows/`).
2. Select **Add file > Create new file**.
3. Type the full path and filename into the name field (e.g. `.cloudbees/workflows/build.yaml`) — typing a `/` creates any missing folders automatically.
4. Paste in the file contents given in that module.
5. Scroll down to **Commit changes**, leave "Commit directly to the `main` branch" selected, and select **Commit changes**.

**To edit an existing file:**
1. Open the file on github.com.
2. Select the pencil (**Edit**) icon in the top-right of the file view.
3. Make your changes.
4. Scroll down and **Commit changes** the same way as above.

Whenever a later module says "commit and push," this is the flow it means — there's no separate "push" step in the web UI, committing does both at once.


## Optional: running TaskFlow on your own machine later

If you want to explore further after the workshop — say, to see how the local Postgres/Flask/React setup fits together, or to poke at the code more freely — you'd need Docker, Python 3.11+, Node.js 18+, and git installed locally, and you'd clone your own copies of the four repos rather than using them only through GitHub's web UI. That's a "take this home and keep learning" path, not something today's modules depend on.

Next: [01-orientation.md](01-orientation.md)
