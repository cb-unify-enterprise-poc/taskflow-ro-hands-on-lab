# 00 — Prerequisites & Setup

Do this **before** the workshop starts (or in the first 15 minutes as a group).

## Accounts

- A [CloudBees Unify](https://cloudbees.io/signup) account, or a sub-org your facilitator has already provisioned for you.
- A GitHub account.

## Local tools

**None required.** Everything in this workshop runs either in CloudBees Unify's own build/deploy containers, in your browser, or (for Module 5's live flag demo) on an environment your facilitator hosts and shares a link to. 

If you'd like to also run TaskFlow on your own machine for further exploration after the workshop, see the note at the end of this doc — but it's optional, not something you need today.

## Get your own copies of TaskFlow

Your facilitator will point you to a "signpost" repo with links to four **GitHub template repositories**: `taskflow-frontend`, `taskflow-backend`, `taskflow-db`, and `app-taskflow`.

For each one:
1. Open the repo link.
2. Select **Use this template > Create a new repository**.
3. Choose **your own GitHub account** as the owner.
4. **Keep the repository name exactly as given** — `app-taskflow`'s workflow references the other three by name, so renaming any of them breaks that reference.
5. Select **Create repository**.

This all happens in the GitHub website — no local git required.


## Optional: running TaskFlow on your own machine later

If you want to explore further after the workshop — say, to see how the local Postgres/Flask/React setup fits together, or to poke at the code more freely — you'd need Docker, Python 3.11+, Node.js 18+, and git installed locally, and you'd clone your own copies of the four repos rather than using them only through GitHub's web UI. That's a "take this home and keep learning" path, not something today's modules depend on.

Next: [01-orientation.md](01-orientation.md)
