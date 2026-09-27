# Failure history

Canonical workflow docs answer **"What should agents do now?"** This directory answers
a different question: **"What happened in a specific environment and version, what did
we learn, and how sure are we?"**

Entries are evidence, not rules. A canonical doc can link to an entry for provenance,
but a rule must stand on its own. Nothing in an entry is current behaviour unless a
canonical doc says so.

## When to add an entry

Add one when a tool, agent or orchestration step fails in a way that cost real time,
needed a non-obvious recovery, or led to a workflow rule. Claude writes the entry.
The human or a reviewer may ask for one.

## Rules

- **Scope every claim.** Name the exact versions and environment. Don't generalise one
  version's behaviour into a claim about the product.
- **Label each statement** as one of:
  - **FACT OBSERVED:** seen directly in a receipt, log, screen or recorded artifact.
    Cite the source.
  - **INFERENCE:** a reasoned interpretation. Say what it rests on.
  - **UNKNOWN:** not established. State it, so nobody fills the gap with an
    assumption.
- **Keep past records as they were.** Don't rewrite the original observations. Add
  later findings, fixes or status changes as dated updates at the end.
- **Record what wasn't proven.** This matters as much as what was.
- **Derive rules carefully.** A lesson that becomes a rule is written in the
  canonical docs in version-independent terms, and the entry links to it.

## File naming

`YYYY-MM-DD-<component>-<short-slug>.md`, using the date of the first observation.

## Entry template

```markdown
# <Short title>

- **Date:** YYYY-MM-DD (first observed)
- **Status:** OPEN | MITIGATED | RESOLVED | OBSOLETE
- **Component:** <tool / subsystem>
- **Exact component version:** <version>
- **Agent / tool versions:** <coordinator, worker, CLI versions>
- **OS and environment:** <OS build, shell, local/remote>
- **Repository / worktree context:** <repo, branch, worktree, placement>
- **Root-cause confidence:** CONFIRMED | PROBABLE | UNKNOWN
- **Known affected version(s):** <only versions actually observed>
- **Known fixed version(s):** <only if verified; otherwise "none verified">
- **Reproduction status:** <reproduced on demand / intermittent / not reproduced / not attempted>
- **Current relevance:** <whether it still matters, and why>

## Goal / operation
## Observed symptom
## Authoritative lifecycle evidence
## Other observations
## What was NOT proven
## Recovery actions attempted
## What worked
## What did not work
## Root cause
## Lessons and derived governance rules
## Related current runbook sections
## Updates
```

## Entries

| Date | Entry | Component | Status |
|------|-------|-----------|--------|
| — | *No incidents recorded yet.* | | |
