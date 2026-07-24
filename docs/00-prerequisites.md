# 00 — Prerequisites & Setup

Do this **before** the workshop starts (or in the first 15 minutes as a group).

## Accounts

- A CloudBees Unify account (access will be provided by your instructor).
- A GitHub account, with the ability to create new public or private repos.

## Local tools

- Git
- Docker + Docker Compose
- Python 3.11+ and `pip`
- Node.js 18+ and `npm`

Check versions:

```bash
git --version
docker --version
docker compose version
python3 --version
node --version
npm --version
```

## Get the TaskFlow app onto your machine

You've been given (or cloned) this workshop repo, which contains the `app/` folder with four sub-projects: `backend`, `frontend`, `db`, and `app-taskflow` (the Application-level repo — no source code, just orchestration workflows).

CloudBees Unify Components (and Applications) connect 1:1 to a repository, so before Module 2 you need to split each sub-project into its **own** GitHub repo:

```bash
# from the root of this workshop repo
for name in backend frontend db app-taskflow; do
  dir=$([ "$name" = "app-taskflow" ] && echo "app-taskflow" || echo "taskflow-$name")
  mkdir -p ~/$dir
  cp -r app/$name/. ~/$dir/
  cd ~/$dir
  git init -b main
  git add .
  git commit -m "Initial commit: $dir"
  cd -
done
```

Then create four new **empty** repos on GitHub named `taskflow-backend`, `taskflow-frontend`, `taskflow-db`, and `app-taskflow`, and push each folder to its matching repo:

```bash
cd ~/taskflow-backend
git remote add origin https://github.com/<your-username>/taskflow-backend.git
git push -u origin main
```

Repeat for `taskflow-frontend`, `taskflow-db`, and `app-taskflow`.

You'll also need to replace the `<YOUR_GITHUB_ORG>` placeholder in `app-taskflow/.cloudbees/workflows/deployer.yaml` with your actual GitHub username/org once you've pushed — that comes up again in Module 3.

## Sanity-check the app runs locally (before touching Unify)

From the original `app/` folder:

```bash
cd app
docker compose up --build
```

- Frontend: http://localhost:5173
- Backend API: http://localhost:8000/api/tasks
- Postgres: localhost:5432 (user/pass/db: `taskflow`/`taskflow`/`taskflow`)

Add a task in the UI. If it shows up and you can check it off, you're ready for Module 1. Leave this running or stop it with `docker compose down` — either is fine, you won't need it again until Module 5.

Next: [01-orientation.md](01-orientation.md)
