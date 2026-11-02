# Agent recovery

After five minutes without observable progress, inspect `state.md`, the active
artifact, scoped repo status/diff, agent status, and available active tool/test
status and output. Check whether a command is still running, whether its output
is advancing, and its stated timeout or expected duration if known. Do not rerun
checks to obtain status. Lack of visibility alone does not establish a stall.

If progress is visible, record it and continue observing in intervals of at most
45 seconds. Otherwise send one short nudge requesting the active operation,
progress, blocker, and next deliverable; use `followup_task` only when the agent
is idle. Allow at least another five minutes without substantive progress before
considering retirement. Do not let repeated unchanged status messages reset this
window indefinitely. A demonstrably progressing tool/test may continue beyond
ten minutes; elapsed time alone is not a reason to interrupt it.

For every recovery nudge, interruption, replacement, or continuation, update the
`state.md` execution-ledger row with pair, phase, phase-attempt number, role,
agent ID, model, reasoning level, and status. Preserve the prior row when a new
agent or attempt starts.

Before replacement, inspect active tool/test status again. If a command has
exceeded its own timeout without progress, use available process/tool evidence
and its supported cancellation mechanism. Retire only after the extended window
and evidence of a stall; an explicit crash/error, unsafe work, or user instruction
can justify earlier intervention. Record the evidence, interrupt, and confirm the
agent and any tool operation that could still edit files are inactive before
launching a replacement. If inactivity cannot be confirmed, report that blocker
rather than risk concurrent edits. Preserve the partial checkpoint and worktree.

For an implementation stall, spawn one fresh Engineer with:
`recovery attempt 2: reconcile implementation.md and the scoped diff; preserve
partial and unrelated work; finish only pending approved items`. It audits before
editing and never resets the worktree. After a failed Luna attempt, use
`gpt-5.6-terra` at low reasoning for this replacement and retain it for later
remediation. A stalled planning agent can be replaced the same way, but no product
edits should exist.

Automatic recovery limit: two Engineer agents per approved synchronized
`R<n>/I<n>`. If the replacement stalls, interrupt it and preserve completed work,
pending items, validation state, and blocker evidence. Follow "Continuation after
automatic limits" in [../SKILL.md](../SKILL.md): request explicit user approval
for one additional bounded attempt with a fresh `gpt-5.6-terra` Engineer at medium
reasoning, then independent review. Do not spawn a third automatically or treat
the limit alone as terminal failure. Record each approval and attempt; if still
incomplete, ask again with updated evidence. A responsive
`CHECKPOINTED_INCOMPLETE` may continue once on the same agent automatically when
the remainder is explicit and bounded.
