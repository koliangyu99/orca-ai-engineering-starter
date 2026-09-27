# Review protocol

Codex reviews independently and read-only. Claude triages, owns every fix, and keeps
the review record.

## 1. Requesting a review

Claude (as Orca coordinator, [ORCA_RUNBOOK](ORCA_RUNBOOK.md)) sends Codex a
self-contained task:

- **Target:** branch, **pinned commit SHA** (or, for uncommitted work, the exact
  working-tree files), and the files or diff range in scope
- **Contract:** the spec version (or working spec) and any earlier findings being verified
- **Focus:** what this review is for, such as spec coherence, final spec verification, integrated implementation, or fix verification
- **Allowed commands:** the applicable project-defined verification gates and any
  probes, plus anything forbidden (for example installs or starting servers)
- **Boundaries:** read-only; the report is written outside the repository or returned in the completion message
- **Output:** the report format in §2

Claude doesn't edit files in scope while the review runs. Otherwise line references
drift, and the reviewer ends up judging a moving target.

## 2. Review report

Each finding has:

- **ID** (`<review-id>-<nn>`, for example `CR2-03` for finding 3 of code review 2) and **severity** (§3)
- **Location**: file and line, or spec section and requirement ID
- **Evidence**: the code or text quoted, and whether it's *observed at runtime* or
  *static*
- **Failure scenario**: concrete inputs or steps, and the wrong result. Required for
  BLOCKER, HIGH and MEDIUM
- **Minimum fix**: the smallest change that resolves it, with alternatives if the spec
  could reasonably be amended instead
- **Verification**: how Claude, or a later verification round, can confirm the fix

The report also includes:
- an overall verdict and severity totals
- the commands run and their results
- **evidence boundaries**: what Codex verified itself, and what it took from someone
  else's record

Don't make findings out of style preferences. Put suggestions that are neither a
defect nor a risk under "Notes", with no severity.

## 3. Severity

| Severity | Meaning | Merge gate |
|----------|---------|------------|
| **BLOCKER** | Breaks a core flow, loses or corrupts data, is a security hole, or makes the spec or implementation unusable as a contract | Must be resolved. Only the human can waive it |
| **HIGH** | A real defect or contract violation on a likely path, with no reasonable workaround | Resolved, or explicitly accepted by the human |
| **MEDIUM** | A bounded defect, contract gap or missing test with a concrete failure scenario | Fixed, or rejected with a recorded reason |
| **LOW** | A minor inconsistency, an edge case, or a small doc or test gap | Claude's call. Record the disposition |

## 4. Claude's triage

Claude checks every actionable finding against the code, the spec and, where it's
cheap, a reproduction. Then Claude records exactly one disposition:

- **ACCEPTED**: the finding is correct. Fix it as proposed or in an equivalent way.
- **PARTIALLY ACCEPTED**: the problem is real, but part of the analysis or the proposed
  fix is wrong. Say which part, and what was done instead. A common case: the code is
  right and the spec is wrong, so the fix is a spec change instead of a code change.
- **REJECTED**: the finding is wrong or doesn't apply. Give the evidence.

Claude doesn't accept a finding just because Codex raised it, and doesn't reject one
without evidence. If a fix would change a locked spec substantively, follow
[SPEC_LIFECYCLE §5](SPEC_LIFECYCLE.md#5-found-a-problem-mid-implementation): an SCR in
STRICT mode, or a numbered amendment in autonomous mode.

## 5. Archiving and ownership

- **Codex** stays read-only. It writes its report outside the repository, or returns
  it in its completion message. It never writes repository files.
- **Claude**, once the review has settled (the completion is accepted):
  - archives the full report without changes as `docs/reviews/<review-id>-<slug>.md`
  - records every disposition in the `docs/reviews/README.md` log (ID, severity,
    disposition, resolution)
  - creates that README for the first review if it doesn't exist yet
- **Fix ownership.** A completed review-only task authorises Claude to synthesise and
  triage the findings. It doesn't transfer the fixes to the reviewer. Claude stays the
  implementation owner and makes the fixes, unless the human explicitly reassigns
  ownership.

## 6. Disagreement

- **MEDIUM/LOW:** Claude's recorded disposition stands. Codex may note that it
  disagrees during verification, and the human sees it in the log.
- **BLOCKER/HIGH:** there is at most **one** clarification or rebuttal cycle. Claude
  sends its evidence, and Codex either withdraws, downgrades or maintains the finding,
  with evidence. If they still disagree after that, Claude escalates to the human with
  both positions in a few lines each, and the human decides. Neither agent reopens it.

## 7. Verification

Verification covers fixes after a code review, and it is also the **final spec
verification** after a substantive spec revision
([SPEC_LIFECYCLE §1](SPEC_LIFECYCLE.md#1-states)). Codex marks each finding
**VERIFIED** or **NOT VERIFIED** (with the reason), reviews the fix diff or revised
text for regressions, and raises any new findings with new IDs.

- One verification round is the default.
- A second round happens only if a BLOCKER or HIGH wasn't verified, or a new BLOCKER
  or HIGH appeared. After that, remaining disagreement or unverified BLOCKER/HIGH
  items go to the human.
- A MEDIUM or LOW that isn't verified doesn't trigger another round. Claude records
  it and the human sees it.
- **Spec text changed after verification.** The round cap limits rechecking
  *findings*. It doesn't let unverified text through. Suppose Claude makes a further
  substantive spec change after final spec verification, whatever the severity that
  prompted it. Then Codex does a focused verification of just that change before the
  spec can be REVIEWED, unless the human records a waiver for that spec. An editorial
  change needs no new round.

A finding the human waives or accepts as-is counts as closed for the merge gate, and
the log records it as waived by the human. The review stage ends with a verdict, no
open BLOCKERs, and every finding dispositioned. Then the human decides.
