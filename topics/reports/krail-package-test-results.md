---
title: "KRAIL package test results on Olo company brain"
kind: report
updated: "2026-06-27"
topics:
  - package-testing
  - krail
  - failures
entities:
  - KRAIL
  - company-brain pack
  - graph build
  - verification script
  - deterministic artifacts
entity_metadata:
  - name: KRAIL
    entity_type: Package
  - name: company-brain pack
    entity_type: PackageComponent
  - name: graph build
    entity_type: WorkflowStep
  - name: verification script
    entity_type: WorkflowStep
  - name: deterministic artifacts
    entity_type: QualityCheck
relations:
  - from: KRAIL
    type: includes
    to: company-brain pack
  - from: company-brain pack
    type: fails_at
    to: verification script
  - from: company-brain pack
    type: fails_at
    to: graph build
---

# KRAIL package test results on Olo company brain

## What worked
- `krail init --pack company-brain --mode markdown_graph`
- `krail doctor`
- `krail capture --url ...`
- `krail search ...`
- `krail graph build` after patching captured datetime strings in inbox frontmatter

## 2026-06-27 verification update
- Re-checked the repo with KRAIL 0.2.2.
- `krail --local doctor` now passes for this repo on the upgraded package.
- Remaining doctor output is warning-only: untriaged inbox items and transient runtime state directories.

## Failures found

### 1. `capture` + `graph build` crash on a fresh scaffold
The capture command writes an unquoted ISO timestamp to `captured_at`. YAML parses that into a datetime object, and the graph CLI later crashes while trying to JSON-serialize it.

### 2. Company-brain verification script is scaffolded with irrelevant panel-data checks
The generated `scripts/verify_project_state.py` still requires `topics/data/processed/longitudinal_panel.csv`, which is an econometrics-style artifact and not appropriate for a company-brain repo.

### 3. Graph artifacts are nondeterministic across identical rebuilds
Repeated `krail graph build` calls change the generated timestamp in `graph.json` and `summary.md`, which means clean CI checks will show diffs even when the underlying graph is unchanged.
