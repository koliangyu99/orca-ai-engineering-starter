# Orca runbook

How Claude coordinates Codex through Orca, and how to recover safely when a worker
misbehaves. This covers principles and lifecycle. **For exact commands, flags, states
and limits, load the guide that matches the installed Orca version (`orca skills get
orchestration`, plus the reference it names for your situation). Don't rely on
remembered syntax.** Command and state names below are only there for orientation.

## 1. Placement

- **By default, use the same worktree.** A read-only Codex reviewer runs in the same
  feature worktree as Claude, so it reviews exactly what Claude built at the pinned SHA.
- **Use separate terminals.** Each agent runs in its own supervised terminal.
  A fresh reviewer terminal doesn't mean a fresh Git worktree.
- **Create another worktree only for a concrete reason**, such as two agents that must
  write overlapping files at the same time, or a review that must run against a
  different branch. Isolating a read-only reviewer isn't a reason.
- While a reviewer is running, the writer doesn't edit files in scope
  ([REVIEW_PROTOCOL §1](REVIEW_PROTOCOL.md#1-requesting-a-review)).

## 2. Roles in Orca

- **Claude is the coordinator.** It creates the Run, starts workers, waits for their
  results, and processes every message.
- **Codex is a supervised worker.** It receives a self-contained Task spec, works
  read-only, and reports once with `worker_done` (the report, or a path to it outside
  the repository). Claude archives it ([REVIEW_PROTOCOL §5](REVIEW_PROTOCOL.md#5-archiving-and-ownership)).
- **Use Orca's messaging, not the human.** Don't ask the human to copy review text
  between agents. Questions go through Orca ask and reply. Results come back through
  `worker_done`.

## 3. Normal lifecycle

1. Check that Orca is running. Bind **one Run** for the effort.
2. Start the reviewer with `worker-start` and a Task spec that names target, change
   (the report), constraints (read-only), ownership, and observable acceptance.
3. Wait for `worker_done`, a question or an escalation. **A timeout or an empty wait
   is a checkpoint, not a failure.**
4. On `worker_done`, check that it's from the expected active Dispatch. Read the full
   report and triage it (REVIEW_PROTOCOL).
5. **Settle the terminal.** Reuse it for an immediate follow-up (such as a
   verification round), retain it if the human asked, or **release** it. Before
   ending the coordination turn, confirm no terminal still needs a decision.

## 4. Evidence: what counts

Keep four things distinct:

- **Observation or absence.** This is what a screen, a transcript tail, silence or a
  timed-out wait shows. **A blank, idle-looking or empty reviewer terminal is NOT
  proof that task delivery failed.** Nor are unchanged output, `unverifiable`
  liveness or a missing heartbeat. On its own, observation authorises nothing.
- **Authoritative lifecycle state.** This is what Orca records for the Dispatch and
  worker (for example `outcome_unknown`, `ready`, `stopped`, `failed`, or `exited`
  liveness). This, not the screen, decides which actions are allowed.
- **Deliberate cancellation.** Some lifecycle states, such as a start whose turn was
  never observed, have a guide-prescribed route of "inspect, then explicitly stop or
  abandon". Choosing to stop is a governance decision to cancel an uncertain attempt.
  It is **not** evidence that the original task was never delivered.
- **Positive proof of failure or exit.** Examples: `exited` liveness, the worker
  observing its own process exit, or a final agent turn that sent no `worker_done`.
  For a worker that had been running and went quiet, only this authorises stop,
  abandon or retry.

## 5. Recovery

1. **Inspect first.** Use the worker list for the bound Run (authoritative liveness
   and `nextAction`), the worker detail, and the terminal or transcript output.
2. **Act only on lifecycle state or positive proof (§4).**
   - If the start was unconfirmed, follow the installed guide's route and the
     receipt's suggested next commands. If inspection shows any sign of a turn
     (working state, heartbeat, task output), keep waiting instead.
   - If a running worker has gone quiet, wait or inspect until you have positive
     proof.
3. **Never launch a duplicate worker** for a Task that may still be live.
4. **Before a retry, prefer stop over abandon**, using the guide's commands. Stop
   closes the exact agent terminal. Abandon fences the lifecycle but can leave the old
   agent live. Never close the terminal by hand.
5. **Retry the same Task, and only after the prior attempt is confirmed in a state
   that permits a retry** (for example the stop receipt reports it stopped and the
   exact terminal closed, or Orca records it failed). Use the retry mechanism in the
   version-matched guide, which names the existing Task and the prior Dispatch (such
   as `--retry-of`) and states placement explicitly. Don't start a fresh Task for the
   same work.
6. **Keep Task and Dispatch identity.** One Task can have several attempts
   (Dispatches), and only the active one is authoritative. Don't create a new Run or
   an unrelated Task to get around a failure. If the installed version's retry or
   circuit-breaker limit is reached, follow that version's guidance and escalate to
   the human.
7. **Release settled workers cleanly** through the release command, after an
   accepted `worker_done`, or when a failed start's receipt explicitly names release
   for a terminal Orca reports as reclaimable. Never release because of a timeout,
   idleness or a heartbeat. If a release is uncertain, follow its receipt.
8. **Record significant failures** in [failure-history/](failure-history/README.md),
   with exact versions and the facts, inferences and unknowns kept apart.

If the evidence stays ambiguous, keep waiting or keep inspecting, and tell the human
the state is unknown. Don't guess.
