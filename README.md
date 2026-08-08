# Career-Ops

> Personalized role-discovery and triage system for finding realistic, worth-it jobs.

![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat&logo=node.js&logoColor=white)
![Playwright](https://img.shields.io/badge/Playwright-2EAD33?style=flat&logo=playwright&logoColor=white)
![Go](https://img.shields.io/badge/Go-00ADD8?style=flat&logo=go&logoColor=white)
![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)

## What This Version Does

This fork is not being used as a general AI job-search operating system.

In this implementation, `career-ops` is primarily a targeted role-finding and triage workflow for Nickolas Glanzer's search. The goal is to surface jobs that are:

- actually reachable from the current background and transition story
- aligned with target role families
- compatible with location constraints
- financially worth pursuing

The active workflow is:

1. Scan direct company boards and selected sources for relevant roles
2. Deduplicate and append new matches into a local pipeline
3. Classify roles by strategic fit, hireability, and compensation viability
4. Render a dashboard for quick human review

This repo does not auto-apply for jobs in the intended workflow. It is a filter, not a spray engine.

## Current Focus

The local configuration is tuned for:

- senior TPM / technical operations / systems modernization roles
- workflow automation / AI workflow implementation roles
- mission integration / defense / training systems adjacent roles
- TS/SCI-clearance leverage where useful
- Colorado Springs, Buckley, or true US-remote roles
- compensation that clears a hard minimum floor

The main question this fork asks is not "is this interesting?"

It is:

- is this believable?
- is this strategically useful?
- is this worth the time?

## Main Workflow

### 1. Scan company boards

```bash
npm run scan:boards
```

This runs [`scan-company-boards.mjs`](E:/Projects/career-ops/scan-company-boards.mjs) and:

- reads local search configuration from [`portals.yml`](E:/Projects/career-ops/portals.yml)
- scans supported ATS/company boards directly
- applies title and location filters
- deduplicates against prior pipeline/history data
- appends new matches to [`data/pipeline.md`](E:/Projects/career-ops/data/pipeline.md)

### 2. Classify candidates

```bash
npm run classify:top
```

This runs [`classify-candidates.mjs`](E:/Projects/career-ops/classify-candidates.mjs) and:

- reads pending roles from the pipeline
- scores strategic fit, hireability, and blended priority
- tags roles into buckets like `both`, `systems-escape-velocity`, `clearance-leverage`, `watch`, and `noise`
- writes ranked output to [`data/candidate-classifications.tsv`](E:/Projects/career-ops/data/candidate-classifications.tsv)

### 3. Build the fit dashboard

```bash
npm run dashboard:fit
```

This runs [`generate-fit-dashboard.mjs`](E:/Projects/career-ops/generate-fit-dashboard.mjs) and writes the current static review dashboard to [`dashboard/fit-dashboard.html`](E:/Projects/career-ops/dashboard/fit-dashboard.html).

## Quick Start

```bash
git clone https://github.com/Oracle1267/Career-Ops.git
cd career-ops
npm install
npx playwright install chromium
```

Then make sure these local files exist and reflect your actual search:

- [`config/profile.yml`](E:/Projects/career-ops/config/profile.yml)
- [`portals.yml`](E:/Projects/career-ops/portals.yml)
- [`data/pipeline.md`](E:/Projects/career-ops/data/pipeline.md)

Run the active workflow:

```bash
npm run scan:boards
npm run classify:top
npm run dashboard:fit
```

Open the generated dashboard:

- [`dashboard/fit-dashboard.html`](E:/Projects/career-ops/dashboard/fit-dashboard.html)

## Source Of Truth Files

[`config/profile.yml`](E:/Projects/career-ops/config/profile.yml)
- Candidate-specific targets, narrative, compensation floor, and constraints

[`portals.yml`](E:/Projects/career-ops/portals.yml)
- Tracked companies, title filters, location filters, and search-source settings

[`data/pipeline.md`](E:/Projects/career-ops/data/pipeline.md)
- Pending/discovered role queue

[`data/scan-history.tsv`](E:/Projects/career-ops/data/scan-history.tsv)
- Scanner memory and dedupe history

[`data/candidate-classifications.tsv`](E:/Projects/career-ops/data/candidate-classifications.tsv)
- Ranked triage output

[`dashboard/fit-dashboard.html`](E:/Projects/career-ops/dashboard/fit-dashboard.html)
- Static review dashboard

[`state.md`](E:/Projects/career-ops/state.md)
- Current handoff describing what this local fork actually does

## Project Structure

```text
career-ops/
├── config/                      # Candidate-specific scoring and targeting config
├── data/                        # Local pipeline, history, and classifications
├── dashboard/                   # Static fit dashboard + Go TUI code
├── docs/                        # Scanner/classifier/architecture docs
├── scan-company-boards.mjs      # Direct board scanner
├── classify-candidates.mjs      # Deterministic role classifier
├── generate-fit-dashboard.mjs   # Static HTML dashboard generator
├── portals.yml                  # Local tracked-company universe
└── state.md                     # Current local implementation summary
```

## Other Repo Capabilities

This repo still contains broader upstream `career-ops` machinery for evaluation, resume generation, packet prep, and other workflows.

That code exists, but it is not the primary story for this implementation. If you update this README later, keep the emphasis on the actual active workflow unless the operating model changes.

## Documentation

- [`docs/COMPANY_BOARD_SCANNER.md`](E:/Projects/career-ops/docs/COMPANY_BOARD_SCANNER.md)
- [`docs/CANDIDATE_CLASSIFIER.md`](E:/Projects/career-ops/docs/CANDIDATE_CLASSIFIER.md)
- [`docs/ARCHITECTURE.md`](E:/Projects/career-ops/docs/ARCHITECTURE.md)
- [`state.md`](E:/Projects/career-ops/state.md)

## License

MIT
