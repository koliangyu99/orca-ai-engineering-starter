# Agent engineering workflow (v1.1)

**Version:** Workflow v1.1.

This is the canonical workflow for how the human, Claude and Codex build software
together. [AGENTS.md](../../AGENTS.md) summarises the authority model. These files hold
the rules. They're written to be project-independent, so they can be copied unchanged
into a new repository.

## Canonical rules vs history

- **Canonical docs** (this directory's top level, plus the spec template) answer
  *"What should agents do now?"*
- **[failure-history/](failure-history/README.md)** answers *"What happened in a
  specific environment and version, what did we learn, and how sure are we?"*

History informs rules but never is one. If a lesson matters, it's written into a
canonical doc in version-independent terms, and the history entry is linked only as
provenance.

## Files

| File | Covers |
|------|--------|
| [WORKFLOW.md](WORKFLOW.md) | The two operating modes, the stages, who owns each one, human gates, verification gates |
| [SPEC_LIFECYCLE.md](SPEC_LIFECYCLE.md) | Spec states, final verification, approval, locking, versioning, SCRs, amendments |
| [REVIEW_PROTOCOL.md](REVIEW_PROTOCOL.md) | What a Codex review contains, severity, Claude's triage, archiving, disagreement, verification |
| [ORCA_RUNBOOK.md](ORCA_RUNBOOK.md) | Where reviewers run, the Orca task lifecycle, evidence categories, safe recovery |
| [../specs/TEMPLATE.md](../specs/TEMPLATE.md) | Starting point for a new feature spec |
| [failure-history/](failure-history/README.md) | Incident format and recorded incidents (not rules) |

## What to read when

| You are about to… | Read |
|-------------------|------|
| Start any feature, or you're unsure which mode applies | WORKFLOW |
| Draft, revise, approve or amend a spec, or find that the code must diverge from one | SPEC_LIFECYCLE, TEMPLATE |
| Review as Codex, or triage and archive a Codex review as Claude | REVIEW_PROTOCOL |
| Launch, wait for, retry or clean up an Orca worker | ORCA_RUNBOOK |
| Record a tool or orchestration failure | failure-history/README |

## Copying into a new repository

Copy `AGENTS.md`, this directory's canonical files, `failure-history/README.md`, and
`docs/specs/TEMPLATE.md`. **Don't copy a project's `docs/reviews/`** or its incident
entries. Those are that project's provenance. Each repository starts its own review log
at its first review, and its own failure history at its first incident.

## Changing this workflow

These files are governance. Changing them is a substantive change: Claude proposes,
Codex reviews, the human approves. Typo and link fixes don't need that.
