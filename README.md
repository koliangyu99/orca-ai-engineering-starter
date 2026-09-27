# Orca AI Engineering Starter

A reusable Human + Claude + Codex engineering workflow for Orca orchestration.

> This is an independent workflow template designed for use with Orca.
> It is not an official Orca or StablyAI project.

## What this is

This repository is a ready-to-use engineering operating model for projects where:
- a human keeps product and approval authority
- Claude acts as lead engineer and implementation owner
- Codex acts as an independent reviewer
- Orca coordinates the agents

It covers five things: disciplined specifications, independent review, explicit
human approval gates, a reusable failure history, and safe multi-agent
orchestration. It contains only workflow documents, with no application code, so it
works for any stack: web apps, backend services, Python, research code, mobile apps,
data projects, Rust, Go, Java, and more.

## Role model

| Role | Responsibilities |
|------|------------------|
| **Human** | Product direction · major architecture decisions · spec approval · approval of substantive changes to locked specs · final merge authority |
| **Claude** | Lead engineer · primary spec drafter · implementation owner · fixes validated findings · Orca orchestration coordinator |
| **Codex** | Independent spec reviewer · independent code reviewer · verifier · **read-only by default** |

**A Codex READY verdict is not human approval.**

## Operating modes

### STRICT FEATURE MODE (default)

```text
Human intent
→ Claude drafts spec
→ Codex reviews
→ Claude triages / revises
→ Codex verifies substantive spec revisions when required
→ Human approves → spec becomes locked
→ Claude implements
→ Codex reviews implementation
→ Claude fixes
→ Codex verifies
→ Human decides whether to merge
```

### AUTONOMOUS PROTOTYPE MODE (explicit opt-in only)

The human provides broad intent and boundaries. Within them, Claude may on its own
define scope, design the architecture, create working specs, implement, test and
coordinate reviews. Codex independently reviews meaningful checkpoints. The human
still owns the final merge decision.

**AUTONOMOUS PROTOTYPE MODE never carries over automatically to later tasks.**

## Quick start: create a private project

```bash
gh repo create my-new-app \
  --private \
  --template koliangyu99/orca-ai-engineering-starter \
  --clone
```

```bash
cd my-new-app
```

This starter repository is public, but a project created from it can be private.

## Quick start: create a public project

```bash
gh repo create my-new-app \
  --public \
  --template koliangyu99/orca-ai-engineering-starter \
  --clone
```

```bash
cd my-new-app
```

Choose `--private` or `--public` to set the new project's visibility.

**Using the GitHub website instead:** on this repository's page, choose **Use this
template → Create a new repository**. The exact wording in GitHub's interface may
change.

## First prompt: normal product work

```text
Use the repository workflow.

STRICT FEATURE MODE.

I want to build:

<describe the product or feature>

Start by establishing the relevant product context and drafting the first
feature specification.

Use Codex as the independent reviewer according to the repository workflow.

Do not begin implementation until the workflow allows it.
Do not merge without my explicit approval.
```

## First prompt: autonomous prototype

```text
Use the repository workflow.

For this task only, use AUTONOMOUS PROTOTYPE MODE.

Goal:

<describe the prototype>

Within my stated boundaries, carry the work through product planning,
architecture, working specs, implementation, verification and Codex review.

Use Orca orchestration for Claude ↔ Codex coordination.

Stop for me only at genuine human decision gates.

Do not merge without my explicit approval.
```

## Recommended Orca usage

1. Open the new repository in Orca.
2. Start Claude in the feature or project worktree.
3. Claude acts as lead engineer and Orca coordinator.
4. When independent review is needed, Claude launches Codex through Orca
   orchestration.
5. A read-only Codex reviewer normally uses the **same worktree**, in a separate
   supervised terminal.
6. Don't create a separate Codex worktree just for read-only review.
7. Use a separate worktree only for a genuine filesystem or write conflict, or
   another concrete reason.

For orchestration policy and recovery principles, see
[`docs/agent-workflow/ORCA_RUNBOOK.md`](docs/agent-workflow/ORCA_RUNBOOK.md). Exact
commands come from the orchestration guide for your installed Orca version.

## Repository structure

```text
AGENTS.md

docs/
├── agent-workflow/
│   ├── README.md
│   ├── WORKFLOW.md
│   ├── SPEC_LIFECYCLE.md
│   ├── REVIEW_PROTOCOL.md
│   ├── ORCA_RUNBOOK.md
│   └── failure-history/
│       └── README.md
│
└── specs/
    └── TEMPLATE.md
```

| File | Purpose |
|------|---------|
| [`AGENTS.md`](AGENTS.md) | Shared entry point and authority model for Claude, Codex and other agents |
| [`docs/agent-workflow/README.md`](docs/agent-workflow/README.md) | Workflow index; tells agents which document to read |
| [`WORKFLOW.md`](docs/agent-workflow/WORKFLOW.md) | STRICT and AUTONOMOUS operating modes |
| [`SPEC_LIFECYCLE.md`](docs/agent-workflow/SPEC_LIFECYCLE.md) | Spec states, locking, approval and Spec Change Requests |
| [`REVIEW_PROTOCOL.md`](docs/agent-workflow/REVIEW_PROTOCOL.md) | Codex review format, severity, Claude triage and disagreement handling |
| [`ORCA_RUNBOOK.md`](docs/agent-workflow/ORCA_RUNBOOK.md) | Orca worker placement, lifecycle and recovery principles |
| [`failure-history/README.md`](docs/agent-workflow/failure-history/README.md) | Schema for recording version-specific failures and lessons |
| [`docs/specs/TEMPLATE.md`](docs/specs/TEMPLATE.md) | Reusable feature-spec template |

## How agents use this repository

1. The agent reads `AGENTS.md`.
2. `AGENTS.md` points it to the workflow documents it needs.
3. STRICT FEATURE MODE applies by default.
4. Claude owns implementation.
5. Codex reviews independently and is read-only by default.
6. The human owns spec approval, substantive changes to locked specs, and merges.

`AGENTS.md` is the shared instruction entry point. **Don't create `CLAUDE.md` for
this workflow** unless you're deliberately changing the workflow.

## Failure history

Record your project's tool and orchestration failures under
[`docs/agent-workflow/failure-history/`](docs/agent-workflow/failure-history/README.md):
- Every incident records its version and environment.
- Every statement is labelled **FACT OBSERVED**, **INFERENCE** or **UNKNOWN**.
- Incidents are evidence, not rules. Current Orca behaviour rules belong in
  `ORCA_RUNBOOK.md`, and an incident can link to the rule it led to.

A new project starts with no incidents, only the schema.

## Updating the workflow

Changes to the workflow and governance are substantive. The normal process is:
Claude proposes → Codex independently reviews → the human approves. Typo, link and
other editorial fixes don't need that process.

## Why this exists

This template comes from repeatedly testing a Human + Claude + Codex workflow with
Orca, then keeping what held up: locked specs, independent read-only review, evidence-based
triage, explicit human gates, and recovery rules that don't mistake silence for
failure.
