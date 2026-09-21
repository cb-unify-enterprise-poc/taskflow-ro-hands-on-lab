# SE Review — Second Pass: Detailed Live Notes, Modules 00–03

**Reviewer:** Danny Dreakford (SE, CloudBees) — ddreakford@cloudbees.com
**Purpose:** the first live pass through Modules 00–04 (see `2026-09-21-se-review.md`) surfaced issues but didn't capture step-by-step notes for Modules 00–03 — those were deferred to this second, detailed pass. This file is that pass: a fresh walkthrough of Modules 00–03, repo(s) rebuilt from scratch, one entry per step regardless of whether anything went wrong.
**Scope:** Modules 00 (prerequisites) through 03 (multi-component app). Module 05 (feature flags) is intentionally out of scope for this pass — on hold pending 2026-09-22 discussion with the workshop author on how to handle it.

Format: same as the first pass — `Choice made → Observed result → Issue (if any) → Proposed edit (if any)`, severity-tagged `[HIGH]/[MED]/[LOW]` only when there's an issue. Where a step confirms or refines a finding from the first pass, note it as `Relates to: finding #N`. A step with no issues gets a short "Pass — no issues" line rather than being skipped, so the record shows what was actually re-verified.

---

## Module 00 — Prerequisites

### Step —
- **Choice:**
- **Observed:**
- **Issue:**
- **Proposed edit:**

## Module 01 — Orientation

### Step —
- **Choice:**
- **Observed:**
- **Issue:**
- **Proposed edit:**

## Module 02 — First Workflow (taskflow-backend)

### Step 1 — Connect your GitHub account
*Flagged in the first pass (finding #5) as a predicted friction point — confirm or dismiss here.*
- **Choice:**
- **Observed:**
- **Issue:**
- **Proposed edit:**

### Step 2 — Create the Component
- **Choice:**
- **Observed:**
- **Issue:**
- **Proposed edit:**

### Step 3 — Add the build workflow
*The web-UI file-creation flow (finding #2) now has a documented path in `00-prerequisites.md#creating-and-editing-files-without-git` — confirm it actually works as written.*
- **Choice:**
- **Observed:**
- **Issue:**
- **Proposed edit:**

### Step 4 — Trigger the build and note the version
- **Choice:**
- **Observed:**
- **Issue:**
- **Proposed edit:**

## Module 03 — Frontend, DB, Deploy Workflows, and the Application

### Step 1 — Frontend Component + build workflow
- **Choice:**
- **Observed:**
- **Issue:**
- **Proposed edit:**

### Step 2 — DB Component + build workflow
- **Choice:**
- **Observed:**
- **Issue:**
- **Proposed edit:**

### Step 3 — Deploy workflow for each component
*The new verification checkpoint (finding #9 fix) asks participants to confirm `jobs:` is named `deploy` and the `workflow_call.inputs` block is correct before moving on — confirm this actually catches the copy-paste error it's meant to catch.*
- **Choice:**
- **Observed:**
- **Issue:**
- **Proposed edit:**

### Step 4 — Environments
- **Choice:**
- **Observed:**
- **Issue:**
- **Proposed edit:**

### Step 5 — Verify app-taskflow copied over
- **Choice:**
- **Observed:**
- **Issue:**
- **Proposed edit:**

### Step 6 — Create the Application
- **Choice:**
- **Observed:**
- **Issue:**
- **Proposed edit:**

### Step 7 — Add the deployer workflow
- **Choice:**
- **Observed:**
- **Issue:**
- **Proposed edit:**

---

## Summary (fill in once the pass is complete)

- New issues found:
- Findings from the first pass confirmed:
- Findings from the first pass not reproduced / dismissed:
