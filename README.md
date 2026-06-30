# GRC Engineering Club Week 3: Build the Gate

This project turns the Week 2 policy-as-code checks into a GitHub Actions pull-request gate.

Week 1 produced a Terraform plan JSON file as machine-readable evidence. Week 2 added Rego policies that evaluate that evidence with Open Policy Agent and Conftest. Week 3 wires those policies into CI so every pull request to `main` is checked automatically.

## What the gate enforces

| Control | Namespace | What it checks |
|---|---|---|
| SC-28 | `compliance.sc28_aws` | S3 buckets must have matching server-side encryption configuration. |
| AC-3 | `compliance.ac3_aws` | S3 buckets must have public access blocks with all four public access block flags set to `true`. |
| CM-6 | `compliance.cm6_aws` | Taggable resources must include `Project`, `Environment`, `ManagedBy`, and `ComplianceScope`. |

## Repository structure

```text
terraform/                     # Week 1 Terraform source
policies/                      # Week 2 Rego policies and tests
plan.json                      # Terraform plan JSON evidence from Week 1
.github/workflows/grc-gate.yml # Week 3 pull-request gate
```

## How the gate works

The workflow runs on every pull request to `main`.

It checks out the repository, installs a pinned Conftest version, runs the three control namespaces against `plan.json`, and uploads the results as a GitHub Actions artifact.

If any policy reports a violation, the workflow exits non-zero and the PR check fails.

That means a broken control blocks the merge instead of becoming a meeting topic later.

## Evidence

The workflow creates an `evidence/` directory during the GitHub Actions run and uploads it as the `grc-gate-evidence` artifact.

The artifact includes:

```text
gate-summary.txt
conftest-results.json
sc28.json
ac3.json
cm6.json
sc28.stderr.txt
ac3.stderr.txt
cm6.stderr.txt
```

The artifact upload uses `if: always()`, so evidence is retained even when the gate fails.

## Demonstration plan

This project is demonstrated with two pull requests:

1. A green PR using the compliant Week 1 plan. The `grc-gate` check passes and the PR is mergeable.
2. A red PR using a plan where SC-28 is broken. The `grc-gate` check fails and, with branch protection enabled, the PR is blocked.

## Why this matters

A control that only exists in a spreadsheet depends on someone remembering to review it.

A control wired into a pull-request gate runs every time, the same way, before the change can merge.

That is the difference between documenting governance and engineering it.
