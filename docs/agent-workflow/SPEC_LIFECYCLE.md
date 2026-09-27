# Spec lifecycle

Applies to feature specs in `docs/specs/` written from [TEMPLATE.md](../specs/TEMPLATE.md).
Working specs in AUTONOMOUS PROTOTYPE MODE use status `WORKING` and are covered in §6.

Specs that predate this workflow keep the status they recorded. The first STRICT work
that builds on one brings it onto this lifecycle (version, header, change history),
and the human confirms its status. Merging prototype work never makes a spec
production-approved.

## 1. States

```
DRAFT ─review─▶ triage/revise ─(final verification if substantive)─▶ REVIEWED ─human approves─▶ APPROVED ─impl merged─▶ IMPLEMENTED
  ▲                                                                     │                       (locked)                (locked)
  └───────────────── substantive revision after REVIEWED ───────────────┘                          │ ▲
                                                                                                   ▼ │ SCR approved → new version
                                                                                              SCR PROPOSED
Any state ──human decides──▶ SUPERSEDED or WITHDRAWN
```

| State | Meaning | Who sets it |
|-------|---------|-------------|
| DRAFT | Claude is writing it. Anything can change | Claude |
| REVIEWED | Codex has reviewed it, Claude has recorded every disposition, and any substantive revision has passed final verification or the human waived it | Claude |
| APPROVED | The human approved this version. **Locked.** It is the implementation contract | **Human only** |
| IMPLEMENTED | The implementation of this version was merged. Still locked | Claude, after the human merges |
| SUPERSEDED / WITHDRAWN | Replaced by another spec, or dropped | **Human only** |

**Getting to REVIEWED.** If Claude's triage makes no substantive change (for example,
every finding was rejected, or the fixes are editorial), the spec goes straight to
REVIEWED. If any substantive or normative text changed, Codex runs a **final spec
verification** of the revision first ([REVIEW_PROTOCOL §7](REVIEW_PROTOCOL.md#7-verification)).
It passes when no BLOCKER or HIGH is left open. Claude lists any unverified MEDIUM or
LOW item in the approval request. A pass covers only the text that was verified. A
later substantive edit needs its own focused verification, or a human waiver.
The human may waive that verification for a specific spec, and the waiver is recorded
in the change history. Claude can't waive it.

**Revising a REVIEWED spec.** A substantive revision sends it back to DRAFT, and it
needs final verification again, unless the human waives it.

**Approval.**
- Before asking for approval, Claude lists **every unresolved open question with its
  proposed default** in the approval request itself, not just in the spec. A question
  with no proposed default must be resolved before approval.
- Approving a spec ratifies the defaults the human was shown, unless the human changes
  them. Claude records which defaults were ratified or changed.
- Approval covers one specific version, and the header records who approved which
  version and when.

**Locking.** Locked means APPROVED or IMPLEMENTED. It isn't a separate step, so
there's no window between approval and locking.

**New versions after implementation.** An approved SCR on an IMPLEMENTED spec returns
it to **APPROVED** at the new version. The change history keeps the version that was
implemented and its merge. The spec becomes IMPLEMENTED again only when the new
version's implementation is merged. A proposed or rejected SCR changes neither the
status nor the version.

**Recording human decisions.** An agent never writes APPROVED, SUPERSEDED or
WITHDRAWN, or ratifies an amendment, on its own initiative. It only records a
decision the human made, in the same commit that applies it.

## 2. Versions

- The first approval is **v1.0**. Each approved SCR or ratified set of amendments
  bumps the minor version (v1.1, v1.2…).
- Editorial fixes don't change the version. They get a line in the change history.
- Code, tests and PRs cite requirement IDs and the spec version they implement.

## 3. Substantive vs implementation detail vs editorial

| Kind | Test | Process on a locked spec |
|------|------|--------------------------|
| **Substantive** | Changes what a user, API client, test or reviewer could observe or rely on: behaviour, UI states, API contract, data model, validation, errors, security or privacy posture, accessibility, scope or non-goals, acceptance criteria, required tests or verification gates, dependencies, prerequisites. Also any reversal of a decision the human ratified | STRICT: an SCR. Autonomous: a ratified amendment (§6) |
| **Implementation detail** | The spec doesn't constrain it, and any choice meets every acceptance criterion: module layout, names, helpers, styling technique, test structure | None. Claude decides. It doesn't go in the spec |
| **Editorial** | Meaning is unchanged: typos, broken links, formatting, a clearer sentence with the same meaning | Claude fixes it and adds a change-history line |

If you're unsure, it's substantive. A clarification that settles an ambiguity in
favour of one observable behaviour is substantive too.

**Why the line sits here.** Some contract changes look like small implementation
choices: reversing an open question the human ratified at approval, lifting a
non-goal because a neighbouring feature needs it, changing a bind address or other
exposure setting, pinning a runtime prerequisite, or amending the spec to match the
code after a review found a mismatch. Each one changes what the human approved, so
each one goes through the change process. Other choices look important but really
are implementation details, because no acceptance criterion depends on them.

## 4. Spec Change Request (SCR)

Required in STRICT FEATURE MODE for any substantive change to a locked spec.

1. **Raise.** Claude stops implementation work that depends on the change. It adds
   an entry to the spec's *Spec change requests* table with status PROPOSED: the ID
   (`SCR-n`), the sections and requirement IDs affected, the proposed text, the
   reason (with evidence: a finding, a failing case, a constraint), and the impact on
   code, tests and schedule. The normative sections stay unchanged.
2. **Review.** Codex reviews the SCR in isolation and in context: is it needed,
   is it complete, does it contradict anything, what does it do to acceptance
   criteria. The usual triage applies.
3. **Decide.** The human approves, rejects or amends it. Nothing is applied before
   that.
4. **Apply.** If approved, Claude edits the normative sections exactly as approved,
   bumps the version, marks the SCR APPROVED with the date and approver, and adds a
   change-history line, all in one commit. If rejected, Claude marks it REJECTED and
   the implementation meets the spec as it stands.
5. **Resume** the paused work against the new version.

Several related changes can go in one SCR. The human can waive the Codex review for a
specific SCR. Claude can't waive it.

## 5. Found a problem mid-implementation?

- **The code can meet the spec:** meet it.
- **The spec is wrong, impossible or ambiguous in a way that affects observable
  behaviour:** in STRICT mode, raise an SCR. Don't implement your preferred reading
  and amend later. In autonomous mode, write a numbered amendment (§6).
- **Only an implementation detail is involved:** decide and move on.
- **Editorial:** fix it and log it.

## 6. Specs in AUTONOMOUS PROTOTYPE MODE

- **WORKING specs.** Claude may create and change `WORKING` specs without an SCR. It
  records changes in the spec's change history, or next to the requirement ID they
  affect, citing the finding that prompted them. Codex reviews them at the
  checkpoints.
- **Amending an already-APPROVED spec.** The spec stays locked, and it changes only
  through this sequence:
  1. Claude writes numbered, reasoned amendments (A-1, A-2…) in the spec's
     *Amendments* section, with status PROPOSED. Each one states what it supersedes.
  2. Codex reviews them at the normal checkpoints.
  3. The PR lists every amendment and flags any that reverse a decision the human
     ratified.
  4. **Before merge**, the human explicitly ratifies or rejects each amendment.
  5. Claude records each decision (RATIFIED or REJECTED, date, approver), bumps the
     version, and adds a change-history line, in a commit on the branch. For a
     rejected amendment, Claude reverts the related spec text and code, or the human
     decides not to merge.
  6. The human merges.

  Merging never ratifies an amendment by implication. If a merge happens without
  recorded ratification, Claude flags it to the human.
- **Moving to STRICT.** Before a WORKING spec can drive STRICT work, it goes through
  REVIEWED → APPROVED.
