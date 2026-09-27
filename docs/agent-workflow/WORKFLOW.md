# Workflow: modes, stages and ownership

## 1. Roles

**Human.** Owns product direction, major architecture decisions, spec approval,
approval of substantive changes to approved specs, and every merge.

**Claude (lead engineer).** Drafts specs, triages reviews, implements, writes tests,
fixes validated findings, archives review reports, records deviations, and
coordinates Orca. Claude makes ordinary implementation decisions inside the approved
contract without asking. Claude must not:

- change a locked spec silently, or substantively without human approval (an SCR in
  STRICT mode, or an explicitly ratified amendment in autonomous mode)
- make a product or major architecture decision that belongs to the human
- describe its own work as independently reviewed
- merge, or push to the main branch, without explicit human authorisation

**Codex (independent reviewer).** Reviews specs and code, verifies fixes, and reports.
It is **read-only by default**. It may read files, diffs and history, run the
project's verification gates, and inspect runtime or browser behaviour. Unless the
human explicitly authorises it for the task at hand, Codex must not edit
implementation or spec files, fix its own findings, change scope, approve a spec, or
merge. Build output and a report written outside the repository are not edits.

A "READY" verdict from Codex means "no open findings". It is not approval.

## 2. Choosing the mode

| | STRICT FEATURE MODE | AUTONOMOUS PROTOTYPE MODE |
|---|---|---|
| When | **Default.** All real product work | Only when the human explicitly asks for it for named work |
| Contract | Human-approved, locked spec | Human's intent and boundaries, plus Claude's working specs |
| Changes to an approved spec | Spec Change Request, human approves | Numbered amendments, human ratifies them **before** merge |
| Human gates | Spec approval, each SCR, merge | Mandate, anything outside its boundaries, amendment ratification, merge |

Rules for choosing:

- Autonomous mode starts only when the human says so, and applies only to the work
  named (a branch, a prototype, an experiment). Record the mandate and its boundaries
  in the scope doc or the PR.
- It never carries over to the next piece of work. When a new request doesn't name
  the mode, it's STRICT.
- Autonomous output doesn't become production-approved when it's merged. To build on
  a prototype feature as product, its spec goes through STRICT approval.

## 3. STRICT FEATURE MODE

| # | Stage | Owner | Output / gate |
|---|-------|-------|---------------|
| 1 | Intent | Human | Goal and constraints for the feature |
| 2 | Draft spec | Claude | `docs/specs/NNN-*.md` from [TEMPLATE](../specs/TEMPLATE.md), status DRAFT. Every open question has a proposed default |
| 3 | Spec review | Codex | Findings per [REVIEW_PROTOCOL](REVIEW_PROTOCOL.md) |
| 4 | Triage and revise | Claude | Dispositions recorded, spec revised |
| 5 | Final spec verification | Codex | **Required if stage 4 made any substantive change.** Passes when no BLOCKER or HIGH is left open. Any other unverified item is listed in the approval request. The human may waive this stage for a specific spec |
| 6 | Mark REVIEWED | Claude | Only after stage 5 passes, is waived, or wasn't needed |
| 7 | **Approve** | **Human** | Claude first presents every open question with its proposed default ([SPEC_LIFECYCLE §1](SPEC_LIFECYCLE.md#1-states)). Status APPROVED, v1.0. The spec is now **locked** |
| 8 | Implement | Claude | Code and tests on the feature branch, meeting the acceptance criteria |
| 9 | Code review | Codex | Findings against the spec and the diff |
| 10 | Triage and fix | Claude | Dispositions recorded, fixes committed |
| 11 | Verify | Codex | Each finding VERIFIED or not, plus any new findings from the fix diff |
| 12 | **Merge decision** | **Human** | Merge, request changes, or reject |

- Implementation doesn't start before stage 7.
- If implementation shows that the spec must change substantively, Claude stops the
  affected work and raises a Spec Change Request
  ([SPEC_LIFECYCLE §4](SPEC_LIFECYCLE.md#4-spec-change-request-scr)). Work the change
  doesn't touch can continue.
- The PR lists the spec version implemented, the review log, any approved SCRs, the
  verification-gate results, and any deviations.

## 4. AUTONOMOUS PROTOTYPE MODE

The human gives broad intent and boundaries. Inside them, Claude may on its own:
define scope, write the architecture, draft working specs, split the work into
slices, implement all layers, write tests, and make reasonable low-risk product and
engineering decisions. Claude records each such decision (scope doc, architecture
decision log, or spec) so the human can see it.

Required even in this mode:

- **Independent review at meaningful checkpoints.** At minimum: architecture and specs
  before most of the implementation, the integrated build, and verification of the
  fixes. Triage follows REVIEW_PROTOCOL in full.
- **Amendments to approved specs are ratified before merge.** Claude writes numbered,
  reasoned amendments inside the approved spec. Codex reviews them at the normal
  checkpoints. The PR lists every amendment and flags any that reverse a decision the
  human ratified. The human explicitly ratifies or rejects each amendment. Claude
  records the ratification and the version change. **Only then** does the human
  merge. Ratification and merge are separate decisions, and a merge alone ratifies
  nothing ([SPEC_LIFECYCLE §6](SPEC_LIFECYCLE.md#6-specs-in-autonomous-prototype-mode)).
- **Stop at the boundaries.** Anything outside the mandate goes to the human first:
  new paid services, real credentials or user data, security posture, deployment,
  destructive operations, or a product direction the mandate doesn't cover.
- **Human merges.** Same as STRICT.

## 5. What does not need the human

Inside an approved contract, and inside the mandate in autonomous mode, Claude
decides and doesn't ask about: internal structure, naming, helpers, refactors that
keep behaviour, test organisation, styling technique, and dependency versions within
already-approved dependencies. It also decides on accepting, partially accepting or
rejecting review findings that don't need a spec change, and on editorial spec fixes.

If Claude is unsure whether something is an implementation detail or a substantive
change, it treats it as substantive
([SPEC_LIFECYCLE §3](SPEC_LIFECYCLE.md#3-substantive-vs-implementation-detail-vs-editorial)).

## 6. Verification gates

**Project-defined verification gates** are the automated or scripted checks a change
must pass. Examples: tests, typecheck, lint, build, smoke, static analysis, notebook
validation, package checks. The project instructions (for example the README) define
the project-wide gates, and each spec lists the gates that apply to it. If neither
defines any, Claude proposes gates in the spec and the human approves them with the
spec.

- Before an implementation review is requested, and before handing over for merge,
  every applicable required gate passes. The only exception is one the human
  explicitly accepts, and it's recorded in the PR.
- Spec, planning and docs-only reviews don't need executable gates. The request lists
  any known unrelated failures.

## 7. Branches, commits and PRs

- One feature branch per spec, or per prototype. Claude commits there in coherent steps.
- Claude archives each review report and records its dispositions
  ([REVIEW_PROTOCOL §5](REVIEW_PROTOCOL.md#5-archiving-and-ownership)).
- Claude may prepare a PR. Only the human merges it, or explicitly authorises Claude
  to merge it.
