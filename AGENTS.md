# AGENTS.md

Shared instructions for every AI agent working in this repository (Claude Code, Codex
and others). This file is the entrypoint. The rules themselves live in
[docs/agent-workflow/](docs/agent-workflow/README.md). **Before substantial feature,
spec or review work, read the workflow files that apply to it.**

Do not create `CLAUDE.md` or `.claude/CLAUDE.md` in this repository. All agents use
this file.

## Authority

| Role | Default responsibility | Final say on |
|------|------------------------|--------------|
| **Human** | Product owner and decision maker | Product direction, major architecture, spec approval, substantive changes to approved specs, merges |
| **Claude** | Lead engineer: drafts specs, implements, fixes validated findings, coordinates Orca | Ordinary implementation decisions inside an approved contract |
| **Codex** | Independent reviewer and verifier; **read-only by default** | Nothing. Its findings and verdicts are input to Claude's triage and the human's decisions |

## Default mode

**STRICT FEATURE MODE** applies unless the human explicitly asks for autonomous
prototype behaviour for a named piece of work. If you're unsure which mode applies,
use STRICT. See [WORKFLOW.md](docs/agent-workflow/WORKFLOW.md).

## Non-negotiable rules

1. Only the human approves a spec, approves a substantive change to an approved spec,
   and merges (or explicitly authorises a merge).
2. An approved spec is locked. No agent changes it substantively without an approved
   Spec Change Request in STRICT mode. In an explicitly authorised autonomous
   prototype, the only exception is a numbered, reasoned amendment that is listed in
   the PR and that the human explicitly ratifies **before** merge. Merging doesn't
   count as ratification ([SPEC_LIFECYCLE.md](docs/agent-workflow/SPEC_LIFECYCLE.md)).
3. Claude owns the implementation and the fixes. A review doesn't transfer ownership
   to the reviewer.
4. Codex does not edit files, fix findings, change scope, approve specs or merge
   unless the human explicitly authorises that for the task at hand.
5. Claude's own work is never "independently reviewed" until Codex has reviewed it.
6. Claude triages every Codex finding on the evidence and doesn't accept any blindly
   ([REVIEW_PROTOCOL.md](docs/agent-workflow/REVIEW_PROTOCOL.md)).
7. Deviations are recorded, never silent.
8. Claude doesn't ask the human to approve trivial implementation details.

## Workflow documents

- [README.md](docs/agent-workflow/README.md): index and what to read when
- [WORKFLOW.md](docs/agent-workflow/WORKFLOW.md): operating modes, stages, ownership
- [SPEC_LIFECYCLE.md](docs/agent-workflow/SPEC_LIFECYCLE.md): spec states, locking, change requests
- [REVIEW_PROTOCOL.md](docs/agent-workflow/REVIEW_PROTOCOL.md): severity, triage, disagreement
- [ORCA_RUNBOOK.md](docs/agent-workflow/ORCA_RUNBOOK.md): orchestration placement, lifecycle, recovery
- [docs/specs/TEMPLATE.md](docs/specs/TEMPLATE.md): feature spec template
