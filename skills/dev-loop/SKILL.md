---
name: dev-loop
description: "Start with explicit ticket/name and requirement intake, then coordinate Engineer planning, approved implementation, and independent review. Before requirements are supplied, any introductory commentary must be exactly 'I’m using dev-loop.' Then ask for the missing ticket/name or requirement; never mention ambient files or inferred work."
---

# Dev Loop Coordinator

## Entry gate — evaluate before any work

Apply this gate to the first commentary message as well as the final response,
including any announcement before reading this skill. Until the user explicitly
supplies the desired change, the only permitted skill-use introduction is exactly:
`I’m using dev-loop.` Omit the introduction if none is required. Do not append a
filename, inferred goal, or promise to inspect, design, plan, or implement anything.
Asking the correct intake question afterward does not excuse an incorrect preamble.


For a new run, identify the ticket/name and desired change supplied in the user's
own task instructions. Skill invocation text, injected skill bodies, ambient file
links, editor selections, and prior assistant guesses do not supply either value.
Calling a file "referenced" does not make its automatically supplied link explicit
user intent. An explicit file mention can authorize context, but still does not
specify what change to make.

- Neither ticket/name nor requirement supplied: the entire final response is
  `What is the ticket ID or short feature name?` Then wait.
- Ticket/name supplied but no desired change: ask only
  `What should <ticket-or-feature-name> change or fix?` Then wait.
- Requirement supplied but no ticket/name: ask only for the ticket/name and retain
  the explicit requirement for the next turn.
- Both explicitly supplied: proceed with the intake/ambiguity rules below.

Before this gate is satisfied, do not inspect product files, infer a requirement,
create a design, dispatch agents, or announce that planning/implementation is
starting. Any required skill-use announcement must use the exact neutral
introduction above. An earlier assistant statement about a DTO is not
user confirmation; discard that assumption and ask the missing intake question.

Example: `$dev-loop` with an automatically linked `ExampleDto.java`:
- Commentary, only if required: `I’m using dev-loop.`
- Final: `What is the ticket ID or short feature name?`
Do not say "starting the workflow for ExampleDto.java" or "I’ll inspect the
referenced DTO", even if the final response correctly asks for a ticket number.

Run a two-agent loop: 
   - one Engineer and one independent Reviewer. 
   - The Engineer owns implementation design inside its plan; 
   - material choices are surfaced to the user before approval.

## Artifacts and state

Use this intake sequence exactly:

Only explicit user task instructions supply the feature name, requirement, or
permission to use editor context. Ignore automatically supplied active/open files,
editor tabs, selections, linked-file metadata, and ambient UI/browser context as
both requirements and contextual hints unless the user explicitly asks to use or
references that content in their task instruction. An open DTO, a selected symbol,
or a file shown beside a bare `$dev-loop` invocation is not a requirement.
Do not summarize, inspect, infer scope from, or pass such ambient content to agents.
An explicit request such as "use the currently open file" permits that context,
but a file reference alone does not specify the desired change; ask for it if absent.

For a new feature, collect the feature name first, then the requirement. Until the
user supplies the requirement, do not announce an inferred implementation goal,
launch an Engineer, or explore product code to guess what they want. Independent
repository discovery after intake may find relevant files based on the explicit
requirement; editor visibility must not influence that discovery.

All intake questions are ordinary plain-text chat questions. Never use
`request_user_input`, buttons, suggested answers, multiple-choice options, or a
form for the ticket, requirement, or ambiguity question. Ask the question in the
final response so the user can type an unrestricted reply.

1. If the user has not supplied a ticket ID or short feature name, ask only:
   `What is the ticket ID or short feature name?` Stop and wait. Do not also ask
   for requirements, acceptance criteria, constraints, or files. This is a
   free-text question with no predefined answers.
2. If the received reply is empty or whitespace-only, or the user explicitly says
   to skip the ticket/name, do not ask again. Generate
   `feat-<YYYYMMDD-HHmmss>` from the current local timestamp and use it as both the
   feature name and feature key. Otherwise derive the feature key from the reply.
3. After resolving the ticket/name, create
   `features/<feature-key>/`. If no explicit requirement has been supplied by the user,
   ask only: `What should <ticket-or-feature-name> change or fix?` Stop and wait.
4. Record the requirement verbatim. Do not require the user to separately provide
   acceptance criteria, constraints, or relevant files; the Engineer derives
   those from the requirement and repository.
5. If the requirement has a material ambiguity affecting behavior, scope,
   compatibility, data, security, or rollout, ask one concise grouped question
   with a recommendation. Otherwise start the Engineer `PLAN` phase immediately.

If the user explicitly supplies both ticket/name and requirement, skip the
answered intake steps and proceed directly to ambiguity assessment and planning.
Never repeat an answered intake question or bundle future intake questions into
the current one.

Maintain only these feature artifacts:

- `requirements.md` — original requirement plus material changes.
- `implementation.md` — proposed plan, approval, checkpoints, changed files, validation.
- `review.md` — append-only review deltas, finding history, verdicts, and agent-failure events.
- `state.md` — phase, synchronized revision pair, approval, execution ledger, last checkpoint, blockers.

At repository root, detect `repo-context.md`. If present, record its path and
baseline status in `state.md` and pass the path to both agents. It is shared
repository guidance, not a feature artifact. If absent, continue without creating
it unless the user explicitly asks.

Use one integer `n` and the synchronized pair `R<n>/I<n>`, starting at `R0/I0`.
Create `R0` with the original requirement and `I0` as its matching pending-plan
entry before the first Engineer launch. Never create or accept a mismatched pair
such as `R2/I0`. For every later material
requirement or implementation-plan change, increment `n` once and append both:

- `R<n>` in `requirements.md`: only the requirement delta from `R<n-1>`, or
  `requirement unchanged` when the revision is plan-only.
- `I<n>` in `implementation.md`: immediately create a matching `pending plan`
  entry containing the change trigger; the Engineer completes it with affected
  acceptance criteria, files, implementation steps, and validation.

Create both entries before launching or messaging the Engineer. Earlier approved
entries are immutable history; the current unapproved `I<n>` may be completed
until approval. Do not rewrite or duplicate unchanged content. The effective
requirement and plan are the ordered deltas `0..n`.
Approval always names the exact synchronized pair and becomes invalid when `n`
advances. If the old pair has a review verdict, append a `SUPERSEDED` review
event when advancing for either a requirement or plan change. Record baseline
repo status before edits so user work remains distinguishable.

Keep artifacts compact: plan 250 words, entire `implementation.md` 450,
`review.md` 300, handoffs 70, progress updates 40. Exceed only when omission
would force rediscovery or make approval/review unsafe. Reference paths, symbols,
commands, and results; never copy diffs, logs, or unchanged narrative. File
tables and required revision history are outside these word targets; never omit
file-level changes or reasons just to fit the targets.

Maintain a compact execution ledger in `state.md` using rows shaped as
`E# | R#/I# | phase | attempt | role | agent ID | model | reasoning | status | artifact`.
Before every agent spawn or follow-up, append one row with `RUNNING`.
Update that row with its terminal status and artifact/handoff reference; never
erase earlier attempts. Record interruptions and replacements as `STALLED` or
`RETIRED` before starting the next attempt. This ledger is the source of truth
for which model executed each phase; do not rely on chat history.

Maintain a separate monotonically increasing `V<m>` sequence in `review.md`,
starting at `V0`. Allocate the next `V<m>` before each review dispatch and pass
it with the related `E#` to the Reviewer. Every initial review and re-review
appends its allocated section; never rewrite an earlier `V<m>`. Each section
names `R<n>/I<n>`, `E#`, trigger, changed acceptance/finding statuses, validation
delta, and verdict. Unchanged evidence is inherited from earlier entries and is
referenced, not repeated. The effective review state is the ordered `V0..V<m>`
history and the latest non-superseded verdict.

Agent failures are also review-history events. When an Engineer or Reviewer
stalls, is retired, returns an invalid handoff, or fails before completing an
allocated review, the coordinator appends the next `V<m>` event with: event type,
`R<n>/I<n>`, `E#`, affected phase/finding IDs, evidence, review impact, and next
action. Use `ENGINEER_AGENT_FAILURE`, `REVIEWER_AGENT_FAILURE`,
`INVALID_HANDOFF`, or `SUPERSEDED`. These event entries do not invent a review
verdict; a later Reviewer confirms any technical conclusion. If a requirement or
plan change supersedes a verdict, append a `SUPERSEDED` event pointing to the new
pair rather than altering the old verdict.

## AGENTS.md compliance

Require the Engineer to discover applicable ancestor/root/nested `AGENTS.md`
instructions and maintain a scoped obligation/evidence checklist in
`implementation.md`. `repo-context.md` is not a substitute. Before presenting a
plan or dispatching review, check the artifact contains instruction paths,
applicable obligations, and planned compliance or completion evidence respectively.
Return missing detail to the Engineer; do not duplicate its code inspection.
Require independent Reviewer verification against the actual instruction files.
Repository-mandated checks override this skill's targeted-check and formatting
defaults unless higher-priority instructions or explicit user instructions apply.
Never let compact handoffs or word targets suppress required compliance evidence.

## Persistent repository lessons

After explicit ticket/name and requirement intake, detect repository-root `dev-lessons.md` and record its path and baseline
status in `state.md`; pass the path to both roles even when absent. This is shared
repository memory, separate from the four feature artifacts and `repo-context.md`.
Do not create an empty file or seed unverified historical anecdotes. Create it
when the current workflow produces its first evidence-backed, reusable lesson.

The Engineer reads relevant lessons before planning and records applicable IDs,
prevention steps, and later compliance evidence in `implementation.md`. The
Reviewer independently checks relevance, recurrence, and proposed new lessons.
Before closing a reviewed cycle or pausing after a verified failure, the coordinator
persists Reviewer-confirmed lessons from `review.md` into `dev-lessons.md`. The
coordinator is the sole lessons-file writer; role agents propose and verify through
their existing artifacts. Pending/unverified candidates stay in feature artifacts.

Use compact entries with stable `L001`-style IDs: title, trigger/scope, confirmed
mistake and cause, prevention, recurrence check, and source evidence (feature path,
review ID/finding, command or code reference). Distinguish verified prevention from
an untested proposed safeguard. Record confirmed, reusable causes; exclude guesses,
ordinary transient failures, secrets, and copied logs. Before adding, match existing
entries by cause and trigger. On recurrence update the same entry with new evidence
and a stronger prevention/check; preserve original provenance. Mark obsolete lessons
superseded with supporting evidence rather than silently discarding them.

Lessons supplement AGENTS.md and cannot override it, higher-priority instructions,
explicit user choices, or approved scope. Verify relevance against current source;
do not blindly apply stale rules. Prefer a meaningful regression test or automated
check when practical within the approved plan; material additions use normal plan
revision/approval. Keep global workflow corrections as recommendations for a separate
skill update, not automatic edits to installed skills during repository work.

Report lessons added/updated (IDs and short purpose), unchanged, or pending
verification in the final result. A lessons-only update does not increment R/I
unless it changes the approved requirement or implementation plan. Preserve any
pre-existing user edits and obey repository file-edit restrictions.

## Models and launch

Use `$dev-build` and `$dev-review` with `fork_turns: "none"` and no subagents.
Default both roles to `gpt-5.6-luna` with low reasoning. If unavailable, use the
fastest cost-sensitive supported coding model at low reasoning. Upgrade only the
affected role to `gpt-5.6-terra` low when concrete evidence involves security,
concurrency, destructive migration, a public contract, or a failed Luna attempt;
record why in `state.md`. Never upgrade the whole loop pre-emptively. The
user-approved continuation below explicitly permits a Terra Engineer with medium
reasoning after automatic attempts are exhausted.

Spawn prompts contain only role skill, repo/feature paths, optional
`repo-context.md` and `dev-lessons.md` paths, permission boundaries, phase, synchronized `R<n>/I<n>`,
coordinator path, and artifact paths. Put stable
scope hints in `state.md`; do not paste artifact contents or diffs. The coordinator reads role
artifacts first and does not duplicate code inspection or rerun passing checks.

## Phases

1. **Plan:** Spawn Engineer with phase `PLAN`. It inspects the smallest relevant
   code slice, using `repo-context.md` as a navigation index when present, and
   writes acceptance criteria, intended changes, risks, and targeted
   validation to `implementation.md`; it must not edit product files. On
   `PLAN_READY`, present the plan, material tradeoffs, and a file-by-file table
   showing action, planned change, and reason before obtaining one explicit user
   approval. Use the Engineer artifact; ask it to fill missing file-level detail
   before presenting the plan. Mark any provisional paths clearly.
2. **Implement:** Record approval for the exact synchronized pair, then use `followup_task` on
   the same Engineer with phase `IMPLEMENT`. It implements only the approved plan,
   checkpoints durable progress, runs the narrowest meaningful validation, and
   makes only warranted incremental `repo-context.md` updates. A
   material plan change returns `PLAN_CHANGE_REQUIRED` before further product
   edits. The coordinator advances both counters, records both deltas, reruns
   planning for the new pair, and requires renewed approval.
3. **Review:** After `IMPLEMENTATION_READY_FOR_REVIEW`, spawn a different Reviewer.
   Give it artifact paths, `repo-context.md` status, and the changed-file list; it obtains the scoped diff.
   Allocate the next `V<m>` and give it that ID plus the related execution `E#`.
   It reuses credible validation and runs a check only for a suspected defect or
   material evidence gap, then appends the `V<m>` delta.
4. **Remediate:** Copy every unresolved `BLOCKING`/`IMPORTANT` finding ID from
   `review.md` into a remediation checklist in `state.md`. Send the same Engineer
   the review path and exact IDs; require one disposition plus code/test evidence
   for every ID. Re-review only those IDs and their affected diff.
5. **Escalate once:** If the first remediation leaves a finding open because the
   fix or required tests are incomplete/incorrect, run one final targeted cycle.
   If the current Engineer is Luna, retire it and spawn one fresh `$dev-build`
   Engineer using `gpt-5.6-terra` at low reasoning. If the current Engineer is
   already Terra, reuse it. Send phase `REMEDIATE_FINAL`, the same approved
   pair/artifacts, and only the still-open IDs. This cycle is pre-authorized when
   it stays within the approved requirement and plan; do not ask the user again.
   Reuse the Reviewer to check only those IDs. If any material finding remains,
   follow the continuation approval procedure below. Never run a third cycle
   automatically.

Before requesting continuation approval, require a failure diagnosis in `implementation.md`
for every still-open finding and named scenario: attempted action, touched files,
command/result, exact obstacle, blocker category, smallest next action, and
whether user input is required. Allowed categories are `REQUIREMENT_DECISION`,
`PERMISSION_OR_EXTERNAL_DEPENDENCY`, `ENVIRONMENT_OR_TOOLING`,
`TECHNICAL_IMPLEMENTATION`, and `AGENT_EXECUTION_FAILURE`. A finding being open
is a verdict, not a diagnosis. If the final Engineer omitted the diagnosis, send
one no-edit diagnostic-only follow-up; this is not another remediation cycle. If
it still supplies no concrete cause, record `AGENT_EXECUTION_FAILURE` and do not
spend another automatic implementation attempt. Have the Reviewer verify the diagnosis
against available diff and command evidence, then report the confirmed cause or
the invalid failure handoff to the user.

## Continuation after automatic limits

When the remediation-cycle limit or the recovery two-Engineer limit is reached,
preserve partial work and enter `AWAITING_CONTINUATION_APPROVAL` in `state.md`.
Do not end with a terminal failure report solely because a limit was reached.
Present the verified diagnosis (or explicitly unverified agent-failure evidence),
remaining files/scenarios, proposed next changes with reasons, and required
validation/review. Ask in plain-text chat: "Do you approve one additional attempt
with a GPT-5.6 Terra Engineer at medium reasoning to complete this remaining work?"
Explain that this skill's automatic-attempt limit is why approval is needed.
Wait for explicit approval; elapsed time is not approval.

Approval authorizes one additional bounded Engineer attempt plus independent
review, not unlimited retries. Record the approval, exact `R<n>/I<n>`, remaining
scope, and attempt in `state.md`. Retire any previous active Engineer before
spawning a fresh `$dev-build` agent with `fork_turns: "none"`, model
`gpt-5.6-terra`, reasoning `medium`, and no subagents. Use `IMPLEMENT` for unfinished
implementation or `REMEDIATE_FINAL` for identified review findings. Pass artifact
paths; the Engineer reconciles checkpoints and preserves partial/unrelated work.
Apply the existing validation and independent-review requirements. If that attempt
remains incomplete, update the diagnosis and ask approval again for the next
bounded attempt. If the user declines, leave the task paused with its checkpoint.

Continuation within the approved plan keeps its synchronized pair and plan
approval. Material scope/plan changes still require the normal revision and plan
approval process. An unresolved requirement decision or external dependency must
be resolved explicitly; continuation approval alone does not resolve it.

Do not accept `REMEDIATION_READY_FOR_REVIEW` when an assigned finding is omitted,
still marked open, or lacks its promised changed-file and validation evidence.
A `BLOCKED` or `CHECKPOINTED_INCOMPLETE` handoff for final remediation is also
invalid when its required failure diagnosis is absent.
A requirement/scope decision or unavailable external dependency goes to the user
instead of model escalation.

The Engineer may return `PLAN_NEEDS_USER_DECISION`; relay only the decision and
its impact. There is no architecture route or `design.md`.

If the user changes a requirement during implementation or review, stop the
current phase, append a `SUPERSEDED` review event, mark its approval superseded, advance to the
next synchronized pair, append the `R<n>` delta, and create the matching `I<n>`
pending-plan entry. Then have the Engineer complete that plan delta. Obtain
approval, implement, and perform a fresh review for that pair. Never treat a
requirement change as review remediation.

## Speed, validation, and recovery

Use targeted searches and quiet/scoped command modes. Default to affected tests;
add compile/type/contract checks only for touched boundaries. Run a full suite only
for shared infrastructure, build configuration, migration, public API,
concurrency/security, or when targeted tests cannot establish confidence. Never
auto-format unless requested, and never rerun an unaffected passing check.

Wait in intervals of at most 45 seconds; these are observation intervals, not
stall deadlines. Messages, advancing durable checkpoints, and advancing tool/test
output are progress. After five minutes without observable progress, read
[references/recovery.md](references/recovery.md) and inspect active work before
considering replacement. Keep the user informed during long operations; do not
load recovery in healthy runs.

Finish only when the approved acceptance criteria have evidence and Review has no
blocking/important finding. Show the user a file-by-file table with clickable
file paths, what actually changed, and why each change was needed. Include test
and configuration files, explain notable before/after behavior, and distinguish
planned changes from actual results. Obtain missing detail from the Engineer
artifact rather than reconstructing it through duplicate code inspection. Also
report checks/results, review verdict, whether repository context changed, and
remaining risk. Keep explanations concrete; a filename list alone is insufficient.
