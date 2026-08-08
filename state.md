# Career-Ops State
**Written:** 2026-08-08
**Purpose:** Current handoff for this local `career-ops` checkout. Read this before changing scanner logic, classifier rules, dashboards, evaluation flows, or candidate-specific config.

---

## What This Project Actually Is

This repository is a personalized career operating system, not just a job-board scanner.

In this checkout, it is being used as a human-in-the-loop command center for Nickolas Glanzer's job search. The direct company-board workflow is important, but it is only one part of the system.

The repo currently supports five distinct jobs:

1. Discover roles from direct company boards and selected search-style sources
2. Triage discovered roles into a ranked queue before deeper work happens
3. Evaluate individual roles in more depth and generate tailored application materials
4. Track pipeline/application state across markdown and TSV files
5. Render dashboards and reports so the queue can be reviewed quickly

Important behavioral boundary:

- The system helps discover, rank, prepare, and track roles
- The system does **not** auto-submit applications in the intended workflow
- Human review is still the final decision point

There is a vendored `Jobs_Applier_AI_Agent_AIHawk` subtree in the repo, but that is not the main operating model of this checkout. The active project scripts and docs are centered on scanning, classification, evaluation, packet prep, and tracking.

---

## Core Workflows In This Checkout

### 1. Direct board discovery

`npm run scan:boards` runs [`E:\Projects\career-ops\scan-company-boards.mjs`](E:\Projects\career-ops\scan-company-boards.mjs).

What it actually does:

- Reads [`E:\Projects\career-ops\portals.yml`](E:\Projects\career-ops\portals.yml)
- Loads title filters, location filters, and tracked companies
- Scans direct ATS/company boards where possible
- Uses adapter priority roughly as:
  - Greenhouse API
  - Lever inference/API-style access
  - rendered-page extraction with Playwright
  - skip `websearch`-only companies for direct harvesting
- Deduplicates against:
  - [`E:\Projects\career-ops\data\pipeline.md`](E:\Projects\career-ops\data\pipeline.md)
  - [`E:\Projects\career-ops\data\scan-history.tsv`](E:\Projects\career-ops\data\scan-history.tsv)
  - [`E:\Projects\career-ops\data\applications.md`](E:\Projects\career-ops\data\applications.md) when present
- Appends new matching roles into the pending pipeline

This workflow is conservative by design. It is meant to maintain a credible queue, not flood the pipeline.

### 2. Candidate triage and ranking

`npm run classify` and `npm run classify:top` run [`E:\Projects\career-ops\classify-candidates.mjs`](E:\Projects\career-ops\classify-candidates.mjs).

What the classifier actually does:

- Reads pending roles from [`E:\Projects\career-ops\data\pipeline.md`](E:\Projects\career-ops\data\pipeline.md)
- Applies deterministic scoring using:
  - [`E:\Projects\career-ops\config\profile.yml`](E:\Projects\career-ops\config\profile.yml)
  - [`E:\Projects\career-ops\config\resume_families.yml`](E:\Projects\career-ops\config\resume_families.yml)
  - [`E:\Projects\career-ops\config\scan_profiles.yml`](E:\Projects\career-ops\config\scan_profiles.yml)
  - [`E:\Projects\career-ops\config\company_priorities.yml`](E:\Projects\career-ops\config\company_priorities.yml)
  - [`E:\Projects\career-ops\config\rejected_patterns.yml`](E:\Projects\career-ops\config\rejected_patterns.yml)
  - [`E:\Projects\career-ops\config\proof_stories.yml`](E:\Projects\career-ops\config\proof_stories.yml)
  - salary enrichment and manual feedback files when present
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
- Writes ranked output to [`E:\Projects\career-ops\data\candidate-classifications.tsv`](E:\Projects\career-ops\data\candidate-classifications.tsv)

This is not a generic AI ranking engine. It is a personalized rules-driven filter tuned to Nick's transition story, compensation floor, location constraints, and evidence base.

### 3. Static fit dashboard

`npm run dashboard:fit` runs [`E:\Projects\career-ops\generate-fit-dashboard.mjs`](E:\Projects\career-ops\generate-fit-dashboard.mjs).

What it actually does:

- Reads the freshest classification TSV
- Recomputes display-oriented fit/hireability/priority metrics for presentation
- Buckets roles by tag
- Renders a static review dashboard to [`E:\Projects\career-ops\dashboard\fit-dashboard.html`](E:\Projects\career-ops\dashboard\fit-dashboard.html)

This dashboard is a snapshot for review, not a source of truth. The underlying truth remains the pipeline/classification data.

### 4. Deep evaluation and application-material generation

The repo also supports a fuller per-role workflow beyond scanning:

- evaluation reports
- tailored PDF resume generation
- application packet prep
- batch processing of multiple roles

Relevant scripts include:

- [`E:\Projects\career-ops\generate-pdf.mjs`](E:\Projects\career-ops\generate-pdf.mjs)
- [`E:\Projects\career-ops\prep-application-packet.mjs`](E:\Projects\career-ops\prep-application-packet.mjs)
- [`E:\Projects\career-ops\generate-weekly-trends.mjs`](E:\Projects\career-ops\generate-weekly-trends.mjs)
- [`E:\Projects\career-ops\merge-tracker.mjs`](E:\Projects\career-ops\merge-tracker.mjs)

So the repo should not be described as scanner-only. It is a broader search-and-prep system.

### 5. Terminal dashboard / tracker tooling

There is also a separate Go terminal dashboard under [`E:\Projects\career-ops\dashboard`](E:\Projects\career-ops\dashboard) with its own binary and source.

That TUI is for browsing the broader pipeline/tracker state. It is distinct from the static `fit-dashboard.html` generated from classifier output.

---

## What This Local Configuration Is Optimizing For

This checkout is strongly customized for Nickolas Glanzer via [`E:\Projects\career-ops\config\profile.yml`](E:\Projects\career-ops\config\profile.yml).

The local optimization target is roughly:

- senior TPM / technical operations / systems modernization / workflow automation roles
- mission integration, defense, training systems, and operational-planning adjacent roles
- opportunities that leverage TS/SCI clearance and Space Force domain experience when useful
- remote-first or Colorado Springs / Buckley-compatible work
- compensation that materially improves on current pay

Current compensation rules in profile:

- current salary: `$127K`
- preferred floor: `$170K`
- hard minimum: `$150K`
- target range: `$170K-$250K+`

This means the local system is not merely asking "is this interesting?"

It is asking:

- is this strategically aligned?
- is it believable from current title history?
- does it create real leverage?
- does the compensation justify the time?

---

## Source Of Truth Files

[`E:\Projects\career-ops\config\profile.yml`](E:\Projects\career-ops\config\profile.yml)
- Candidate-specific identity, targets, narrative, compensation floor, and decision rules

[`E:\Projects\career-ops\portals.yml`](E:\Projects\career-ops\portals.yml)
- Scan universe, company list, title filters, location filters, and search-source configuration

[`E:\Projects\career-ops\data\pipeline.md`](E:\Projects\career-ops\data\pipeline.md)
- Pending/discovered role queue

[`E:\Projects\career-ops\data\scan-history.tsv`](E:\Projects\career-ops\data\scan-history.tsv)
- Scanner memory and dedupe/status history

[`E:\Projects\career-ops\data\candidate-classifications.tsv`](E:\Projects\career-ops\data\candidate-classifications.tsv)
- Ranked triage output used for the static fit dashboard

[`E:\Projects\career-ops\dashboard\fit-dashboard.html`](E:\Projects\career-ops\dashboard\fit-dashboard.html)
- Generated static dashboard for quick review of ranked roles

[`E:\Projects\career-ops\data\applications.md`](E:\Projects\career-ops\data\applications.md)
- Broader application tracker when active in the workflow

---

## Practical Interpretation

The cleanest mental model for this repo is:

- **scanner** finds fresh candidates
- **classifier** reduces noise and ranks plausibility
- **dashboard** makes ranked output easy to review
- **evaluation/material tooling** supports deeper pursuit of selected roles
- **tracker/integrity scripts** keep the overall search state coherent

So if you are editing this project, do not assume the direct-board workflow is the whole product.

It is more accurate to say:

- this is a personalized career search operating system
- the current most active automation is direct-board discovery and ranking
- all downstream tooling exists to support selective, higher-quality applications rather than high-volume spraying

---

## Current Repo Reality To Preserve

When updating this checkout, preserve these truths unless the user explicitly changes strategy:

- Keep human review in the loop
- Do not turn the system into an auto-apply bot by default
- Treat compensation and location constraints as first-class rules, not cosmetic preferences
- Keep the classifier grounded in believable transition logic, not just keyword enthusiasm
- Treat the static fit dashboard as a reporting artifact, not the canonical data store
- Remember that this repo contains both generalized upstream `career-ops` features and heavily personalized local tuning for Nick

---

## Recommended Next Step When Making Changes

If you are changing behavior, first decide which layer you are actually modifying:

1. discovery coverage in `portals.yml` or scanner adapters
2. ranking logic in the classifier/config files
3. presentation logic in the fit dashboard
4. deeper evaluation / PDF / tracker workflows

Most confusion in this repo comes from mixing those layers together. Keep them separate and update this state file again if the operating model changes.
