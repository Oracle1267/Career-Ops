# Career-Ops State
**Written:** 2026-08-08
**Purpose:** Public handoff for this `career-ops` checkout. Read this before changing scanner logic, classifier rules, dashboard output, or sample-data expectations.

---

## What This Repository Is

This repository is a career-search operating system with multiple layers:

1. Role discovery from direct company boards and selected search-style sources
2. Deterministic triage and ranking before deeper work happens
3. Optional evaluation and application-material generation workflows
4. Tracker and dashboard tooling for review

Important behavioral boundary:

- The system helps discover, rank, prepare, and track roles
- The default workflow does **not** auto-submit applications
- Human review remains the final decision point

The direct board scanner is important, but it is only one part of the repository.

---

## Core Workflows

### 1. Direct board discovery

`npm run scan:boards` runs [`scan-company-boards.mjs`](./scan-company-boards.mjs).

What it does:

- Reads [`portals.yml`](./portals.yml)
- Loads title filters, location filters, and tracked companies
- Scans direct ATS and company boards where possible
- Deduplicates against:
  - [`data/pipeline.md`](./data/pipeline.md)
  - [`data/scan-history.tsv`](./data/scan-history.tsv)
  - [`data/applications.md`](./data/applications.md) when present
- Appends new matching roles into the pending pipeline

### 2. Candidate triage and ranking

`npm run classify` and `npm run classify:top` run [`classify-candidates.mjs`](./classify-candidates.mjs).

What the classifier does:

- Reads pending roles from [`data/pipeline.md`](./data/pipeline.md)
- Applies deterministic scoring using configuration files under [`config/`](./config/)
- Produces fields such as:
  - `strategic_fit`
  - `hireability`
  - `priority`
  - `readiness`
- Tags each role into buckets such as:
  - `mission-integration-advisory`
  - `both`
  - `systems-escape-velocity`
  - `clearance-leverage`
  - `salary_unknown_review`
  - `watch`
  - `noise`
- Writes ranked output to [`data/candidate-classifications.tsv`](./data/candidate-classifications.tsv)

### 3. Static fit dashboard

`npm run dashboard:fit` runs [`generate-fit-dashboard.mjs`](./generate-fit-dashboard.mjs).

What it does:

- Reads the freshest classification TSV
- Recomputes display-oriented fit, hireability, and priority metrics for presentation
- Buckets roles by tag
- Renders a static review dashboard to [`dashboard/fit-dashboard.html`](./dashboard/fit-dashboard.html)

This dashboard is a reporting artifact, not a source of truth. The underlying truth remains the pipeline and classification data.

### 4. Deeper evaluation and application-material generation

The repo also supports fuller per-role workflows beyond scanning:

- evaluation reports
- tailored PDF resume generation
- application packet prep
- batch processing of multiple roles

Relevant scripts include:

- [`generate-pdf.mjs`](./generate-pdf.mjs)
- [`prep-application-packet.mjs`](./prep-application-packet.mjs)
- [`generate-weekly-trends.mjs`](./generate-weekly-trends.mjs)
- [`merge-tracker.mjs`](./merge-tracker.mjs)

### 5. Terminal dashboard and tracker tooling

There is also a separate Go terminal dashboard under [`dashboard/`](./dashboard/) with its own binary and source.

That TUI is distinct from the static [`dashboard/fit-dashboard.html`](./dashboard/fit-dashboard.html) generated from classifier output.

---

## Public Sample Data Policy

The tracked sample files that ship with this repo are synthetic:

- [`data/pipeline.md`](./data/pipeline.md)
- [`data/candidate-classifications.tsv`](./data/candidate-classifications.tsv)
- [`dashboard/fit-dashboard.html`](./dashboard/fit-dashboard.html)

They are intentionally sanitized to avoid publishing a real candidate pipeline, scan history, or decision trail.

---

## Source Of Truth Files

[`config/profile.yml`](./config/profile.yml)
- Candidate-specific identity, targets, narrative, compensation floor, and decision rules

[`portals.yml`](./portals.yml)
- Scan universe, company list, title filters, location filters, and search-source configuration

[`data/pipeline.md`](./data/pipeline.md)
- Pending or discovered role queue

[`data/scan-history.tsv`](./data/scan-history.tsv)
- Scanner memory and dedupe and status history

[`data/candidate-classifications.tsv`](./data/candidate-classifications.tsv)
- Ranked triage output used for the static fit dashboard

[`dashboard/fit-dashboard.html`](./dashboard/fit-dashboard.html)
- Generated static dashboard for quick review of ranked roles

[`data/applications.md`](./data/applications.md)
- Broader application tracker when active in the workflow

---

## Practical Interpretation

The cleanest mental model for this repo is:

- **scanner** finds fresh candidates
- **classifier** reduces noise and ranks plausibility
- **dashboard** makes ranked output easy to review
- **evaluation and material tooling** supports deeper pursuit of selected roles
- **tracker and integrity scripts** keep the overall search state coherent

So if you are editing this project, do not assume the direct-board workflow is the whole product.
