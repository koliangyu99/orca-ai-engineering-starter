# Spec NNN — <Feature name>

- **Status:** DRAFT <!-- DRAFT | REVIEWED | APPROVED | IMPLEMENTED | SUPERSEDED | WITHDRAWN; WORKING in autonomous mode -->
- **Version:** 0.x (draft) <!-- v1.0 at first approval; +0.1 per approved SCR or ratified amendment set -->
- **Mode:** STRICT FEATURE MODE <!-- or AUTONOMOUS PROTOTYPE MODE (WORKING spec) -->
- **Owner:** Claude (lead engineer)
- **Approver:** Human owner. Only the human sets APPROVED
- **Approved:** — <!-- "vX.Y by <human>, YYYY-MM-DD; open-question defaults ratified: Q1, Q2 (Q3 changed)" -->
- **Related:** <!-- product scope, architecture, API docs, prior specs, review log -->

See [SPEC_LIFECYCLE](../agent-workflow/SPEC_LIFECYCLE.md) for states and change rules.
Delete any section that doesn't apply. Write "N/A" only when leaving the section out
would look like an oversight.

---

## 1. Goal

What problem this solves, for whom, and how we'll know it worked.

## 2. User experience

What the user sees and does, step by step. For a non-UI feature, describe what the
caller or operator experiences.

## 3. Scope

What this spec delivers.

## 4. Non-goals

What it deliberately doesn't do, even if it's tempting. Later specs lift these
explicitly.

## 5. Functional requirements

Numbered and testable. Give each requirement an ID (FR-1…) that tests and reviews can
cite.

## 6. API contract (where applicable)

Interfaces, parameters, statuses or return codes, response shape, errors, and
idempotency.

## 7. Data model (where applicable)

Types and fields, invariants, fixture or seed rules, and ordering.

## 8. States

- **Loading:** what's shown or returned while work is in progress.
- **Empty:** the message or result, and any next action.
- **Error:** what's shown or returned, retry behaviour, and what's never exposed.

## 9. Validation

Input rules, limits, normalisation order, and where each error appears.

## 10. Security and privacy (where applicable)

Exposure, untrusted input, secrets, and what must never be logged or shown.

## 11. Accessibility (where applicable)

Structure, names and roles, focus, keyboard use, announcements, contrast, and motion.

## 12. Acceptance criteria

Numbered (AC-1…). Each one is observable by a human or a test. Say which are manual
checks.

## 13. Testing and verification gates

- **Required tests:** numbered (T-1…), with the tooling to use.
- **Verification gates:** the project-defined gates this feature must pass, for
  example tests, typecheck, lint, build, smoke, static analysis, notebook
  validation, or package checks. Name the command for each, or reference the
  project-wide list.
- **Manual acceptance:** what's checked by hand, and where the results are recorded.

## 14. Assumptions

Numbered (A1…). If one turns out false, that may need a spec change.

## 15. Open questions

Every question needs a **proposed default**. Before asking for approval, Claude lists
each unresolved question and its default in the approval request. Approving the spec
ratifies the defaults the human was shown, unless the human changes them. The
implementer doesn't choose between alternatives.

- **Q1. <question>** Options… *Proposed default:* …

## 16. Definition of done

- [ ] STRICT: status was APPROVED (at the version implemented) before implementation
      began. Autonomous: the mandate is recorded, and the spec is WORKING or APPROVED
- [ ] All requirements and acceptance criteria are met. Manual checks are recorded
- [ ] Every applicable required verification gate passes, or the human accepted a
      recorded exception
- [ ] Codex review done, report archived, and every finding dispositioned. No open
      BLOCKER, and no open HIGH unless the human accepted it
- [ ] No non-goal implemented, and no unapproved dependency added
- [ ] No silent deviations. STRICT: every substantive change has an approved SCR
      before it's implemented, and a PR note alone isn't enough. Autonomous: every
      change to an APPROVED spec is a numbered amendment that the human ratified
      **before merge**

## 17. Spec change requests and amendments

| ID | Status | Sections / IDs | Change | Reason and evidence | Codex review | Decision (who, when) |
|----|--------|----------------|--------|---------------------|--------------|----------------------|
| — | | | | | | |

<!-- STRICT: SCR-n, PROPOSED | APPROVED | REJECTED. Autonomous: A-n, PROPOSED | RATIFIED | REJECTED.
     The normative text changes only after APPROVED or RATIFIED. -->

## 18. Change history

| Version | Date | Change | By |
|---------|------|--------|----|
| 0.1 | YYYY-MM-DD | Initial draft | Claude |
