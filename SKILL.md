---
name: oeconomia
description: >-
  Delegation economy for budget-constrained orchestrator sessions: when the
  main session runs on a scarce, rate-limited model and strong workhorse
  models are available as subagents, keep the orchestrator's compounding
  context window lean by routing high-volume, low-judgment work to
  explicit-model subagents. REFLEX (holds even before the body loads):
  (1) EVERY Agent dispatch carries an explicit `model:` — an unset dispatch
  can inherit the session model and silently spend the budget you are
  protecting; (2) delegate bulk + early — multi-file comprehension reads,
  protocol-driven review sweeps, build/test runs, scripted bulk edits — bulk
  content must never land in the orchestrator's window, where it is re-billed
  on every remaining turn; (3) keep judgment — architecture, which-change
  decisions, synthesis, calibration reads, version-control curation, dialogue
  with the user; (4) floor — a single known-file read, a one-off grep, a
  two-line edit, or any read near session-end stays home. Load the body
  BEFORE: dispatching or inlining a heavy multi-file read; a bounded code task
  mid-conversation; forming a review wave; continuing any session whose
  context is heading past ~300K tokens. SKIP in cheap short sessions (most
  lookup and light research work) — imposing delegation ceremony there is this
  skill's failure mode. Economy licenses errand delegation, never avoidance of
  genuinely pipeline-shaped work: three or more independent concerns needing
  parallel investigation and synthesis deserve a real multi-agent pipeline,
  not one under-powered errand.
allowed-tools: Read, Grep, Glob, Bash, Agent, SendMessage
---

# Oeconomia — a delegation economy for orchestrator sessions

> **This skill is self-updating by design.** A stale contract mis-allocates every future session's budget — worse than no contract. When your environment contradicts a claim here, correct your copy in the same change that surfaces the contradiction, date-stamped; the act of noticing is the trigger. Section 8 carries the full maintenance discipline.

*Oeconomia* (Greek οἰκονομία): household stewardship — the discipline of spending a scarce resource where only it can serve, and routing everything else to where it is plentiful. This skill is that discipline applied to an orchestrating model session: which work the main session keeps, which work it dispatches to subagents, what shape the handoff takes, and how the returned work is verified without re-doing it.

It was distilled from measured evidence: token-accounting across roughly six hundred working sessions in one long-running environment, plus a forensic study of a large delegated-review prototype (about forty subagents across parallel waves) whose choices the finished doctrine reproduces when applied blind. The numbers below are one environment's measurements; the mechanisms are general. Measure your own environment before trusting any constant — the last section shows how.

## 0. Charter and jurisdiction

**Governs**: everyday intra-session work allocation when the orchestrating session runs on a scarce or rate-limited model and capable workhorse models are available for subagent dispatch. Errands, background reads, bounded code tasks, review sweeps.

**Does not govern**: pipeline-shaped work. When a problem has three or more independent concerns that deserve parallel investigation and a synthesis pass, that is a deliberate multi-agent pipeline — a different regime whose primitive is fidelity, not economy. **The border sentence: economy licenses errand delegation; it never licenses avoidance of pipeline-shaped work.** A budget-anxious orchestrator collapsing a genuine five-agent investigation into one under-powered errand is this skill's worst failure mode — the vice wearing the virtue's clothes.

**Regime check**: when the session model IS the workhorse model, the budget argument loses its force. The classification lens below still improves the work; the urgency is what varies.

**Non-hampering clause**: this contract may never degrade the work. Delegation forces crisp specification, which usually raises quality; where it would lower fidelity instead, fidelity wins and the delegation is re-shaped, not forced.

## 1. Why this works: the window is the cost

Measured across ~600 sessions: an orchestrator session consumes input over output at roughly **263:1**, and ~98.5% of that input is cache-read — the accumulated context window re-sent on every turn. Generation is a rounding error against the window, and ~92% of generation is thinking: no-tool reasoning and user dialogue, the one thing that cannot be delegated at all. (If your provider weights cached reads at a tenth, the ratio compresses toward 5:1 — the direction is invariant, and the direction is all the doctrine needs. Instrument note: usage collectors that read only text-final assistant events, or that take the first streamed usage block per message, undercount severalfold — one streamed message fans out per content block, and its usage counters are progressive with only the last block final. Fixing that in the measuring environment made the ratio stronger, not weaker.)

Three consequences:

1. **The true cost of a context-entering read is `freight × expected-remaining-turns`.** A bulk read early in a long session is re-billed hundreds of times before the session ends; the same read near session-end is nearly free. A subagent's window is disposable — it pays for its reading once, across a short life, and dies. So the floor under delegation has a *timing* axis, not just a size axis: **delegate bulk, early**.
2. **Byte-share is the wrong metric.** An environment can already route the majority of raw read-bytes through subagents while the orchestrator still consumes ~96% of the budget. The win is architectural, not volumetric: **stop letting bulk content land in the compounding window** — not "delegate more reading."
3. **A lean window is not merely cheaper — it is sharper.** A context holding distilled summaries, briefs, and verdicts keeps its judgment crisp across hundreds of turns; a context holding raw file dumps makes every subsequent inference compete with noise. The attention argument outlives any pricing regime, which is why this discipline is worth keeping even when tokens are cheap.

One honest ceiling: **delegation halves; it never eliminates.** In the measured exemplar pair, a well-delegated heavy session still cost about half of what a comparable undelegated one cost — not a tenth. Design, calibration, protocol authoring, briefs, and ingest are irreducibly the orchestrator's. This contract mitigates the compounding window; it does not promise salvation.

## 2. The lens: judgment-density, not token count

Classify every action by the **conditional complexity of its output given the available substrate** — how much of what the action produces is fixed by what already exists (codebase, docs, protocols, schemas, briefs) versus originated by the actor's judgment. Never classify by token count: measured across the corpus, bulk reads carry enormous freight and near-zero generation while edits carry near-zero freight and high generation. Context-heavy and judgment-heavy are different *regions* of the action space, not two ends of one scale.

The working ladder:

| Level | Meaning | Examples |
|---|---|---|
| **Stock** | output retrievable from the substrate | mechanical reads, look up a value, run a known command |
| **Implied** | derivable by a known procedure, no choice | sweep-and-report, uniform refactor, run tests and tabulate |
| **Selected** | a choice from a visible option space whose criteria are supplied | judge items against a written protocol, triage into known bins, structured review against a rubric |
| **Original** | the criteria, position, or architecture must be originated | form a position, design the architecture, revise a taxonomy, read the user's intent, invent the rubric itself |

**The delegation boundary sits at the Selected|Original seam, and it is a theorem, not a taste.** For output at Selected and below, verification costs less than production: a machine checks a test suite, a schema, a quote's existence; a spot-read confirms a protocol was followed. For Original output, verification *is* re-production — to check a position or an architecture you must re-form the judgment, so delegating it costs twice instead of half. The delegation economy closes exactly and only where verification is cheaper than production, and that line falls precisely at "the criteria exist outside the agent's head."

**Delegation is substrate-extension.** An action's intensity is not a property of the action alone — it is a property of the action *given the current substrate*, and the orchestrator authors the substrate. Judging against tacit standards is Original and undelegatable: each agent would invent its own incompatible criteria. Write the criteria down once, into a versioned protocol file, and each dispatch's residual task drops to Selected: "apply this protocol to these inputs." The extension artifacts form a ladder:

| Artifact | Deposits | Reduces | Amortizes |
|---|---|---|---|
| Versioned protocol file | the decision criteria, made explicit | Original → Selected | across every dispatch and future run; improves under revision |
| Schema / output contract | the output's structural form | Selected → Implied; *enables the mechanical gate* | across all consumers |
| Worked exemplar | one concrete done example | Selected → Implied (pattern-match beats choose) | cheap — often a byproduct of the first dispatch |
| Per-dispatch brief | this slice's scope, inputs, boundaries | narrows everything | **never** — the brief is each delegation's marginal cost |

The brief deserves a defense: it is a thinking tool disguised as overhead. Writing a good brief forces you to state what the concern is, what would bias it, and what its verification gate will be — before any evidence arrives. Part of the per-dispatch cost is design work you owed anyway, done earlier, where it shapes the work instead of critiquing it afterward. Sharp briefs return sharp work; soft briefs return soft work in exactly the same places.

**The residual-Original diagnostic** — the deepest maintenance tool in the doctrine: Original judgment leaking from a delegated output means the boundary was mis-drawn, and the leak forks two ways. If the leaked judgment *generalizes* — the agent had to invent a rule the protocol should have carried — the substrate was under-extended: fold the leak into the protocol and re-dispatch. The leak is itself the specification of the missing substrate. If the leaked judgment is *terminal* — it IS the deliverable — the class was misclassified: pull it home. And note the epistemic bonus: several independent agents converging on the same abstention or the same invented rule is protocol-falsification evidence of a kind one mind reading alone cannot generate. Fan-out is an instrument, not just an economy.

**The consequence rider, orthogonal to intensity**: a Stock-intensity *delete* is not a Stock-intensity *read*. Side-effects, external reach, and irreversibility gate delegation independently of how mechanical the work looks. Mutating writes want diff review; external or irreversible actions (infrastructure writes, publishes, destructive file ops, history rewrites) stay home or run preview/dry-run first.

Two classes sit outside the ladder entirely: **dialogue with the user** (Original by address — its content lives in their head and the working relationship; no quantity of substrate contains it) and **dispatch itself** (constitutive — the boundary-drawing cannot be handed to the thing being bounded).

## 3. The action-class verdicts

| Action class | Verdict | Extension needed | Gate |
|---|---|---|---|
| Comprehension reads (multi-file code/docs) — the dominant freight class | **delegate-always** when bulk | file paths in the brief; a filter-protocol if the filter recurs | spot-read the returned summary |
| Inspection/search sweeps | **delegate-when** bundled into a task | narrow mandate ("find all callers of X") | self-evident result |
| Single targeted lookup (known file, one grep) | **keep** — below floor | — | — |
| Orientation reads (session-defining context) | **keep-always** | — | — |
| Build/test **runs** | **delegate** the run | none — the failure names itself | exit code / first error line |
| Novel-failure **diagnosis** | **keep** (Original) | — | — |
| Code authoring | **split**: delegate the comprehension reads and boilerplate-from-exemplar; **keep the which-change decision and architecture** | worked exemplar; scoped brief | tests/typecheck/build + a diff read |
| Mechanical edit sweeps (uniform, many files) | **delegate** | pattern + file-set brief | diff review + compile/test backstop |
| Doc/synthesis authoring | **split**: delegate first-draft bulk arrangement; **keep synthesis and any new inference** | outline/protocol + prior summaries | spot-read + citation check |
| Review/judging | **delegate-when** a protocol externalizes the criteria; keep single-item or tacit-criteria judgment | **versioned protocol file** | mechanical exhibit gate + a sampled altitude read |
| External-system operations | **split**: delegate bulk reads; **keep or preview-gate writes** | pointer to the tool's own documentation/skill | reads mechanical; writes dry-run first |
| Version control, workspace bookkeeping | **keep-always** (curation judgment) | — | diff/lint self-check |
| Scripted shell bulk | **delegate** early | brief or script | mechanical; preview anything destructive |
| Dispatch/orchestration, user dialogue, thinking | **keep-always** (constitutive · addressed · irreducible) | — | — |

The empirical shape behind the table: in the measured corpus, bulk comprehension reads alone were ~76% of all orchestrator freight, while edits — the most frequent tool by call count — were ~2% of freight and mostly judgment. The delegable part of coding is the reading coding requires, not the edits. "Delegate the basic coding" mis-aims; "delegate the comprehension, keep the which-change" is what the data supports.

## 4. Handoff shapes — all four, every delegation

1. **Versioned protocol file** for recurring criteria. Author once, point every dispatch at it, never re-inline it per dispatch. It is also where residual-Original leaks get folded back in, so it improves under use.
2. **File-based briefs in, file outputs back.** Payloads stay off the wire and out of the window: the dispatch is a pointer (~a hundred tokens), the agent's reply is one line, the substance is on disk. Have agents write a compressed summary *first*, then the full output — the summary is a checkpoint that survives if the agent dies mid-work.
3. **Mechanical gate at ingest** — the highest-leverage move in the whole discipline: **constrain the output's shape until its gate becomes mechanical.** Schema validation. Tests. And the strongest pattern: an *exhibit anchor* — require every claim to carry a verbatim quote from its source, and machine-check at ingest that the quote exists. A fabricated or paraphrased claim then cannot land, not because anyone is vigilant but because the collector refuses what it cannot find. In the measured prototype this gate saw over 150 proposals and zero rejections — a backstop, not a bottleneck, because strong models quote reliably when the protocol demands it. "Delegate + verify mechanically" is the pattern; "delegate + trust" is the failure mode, and it fails silently.
4. **Transcripts as ground truth.** Subagent transcripts carry the real per-event `model` field and stream live mid-flight; the UI's model label is not evidence. Their location is harness-build-specific and moves across versions — one current build lands each agent's full JSONL transcript under the session's temp directory at `tasks/<agent-id>.output`; an earlier build used a `subagents/` directory of per-agent `.jsonl` files. Locate your build's layout once, then one grep settles what actually ran:
   `grep -o '"model":"[^"]*"' <agent-transcript> | sort | uniq -c`

## 5. Dispatch mechanics — hard rules and known hazards

- **Explicit `model:` on every dispatch, no exceptions.** An unset dispatch can inherit the session model — silently spending the budget you are protecting. Defaults vary across platform versions and configurations; the failure is silent, the rule is cheap. Verify your environment's actual behavior once, via the transcript grep above.
- **Model routing derives from residual intensity, and a cheaper tier is earned by exhibit, never assumed.** Protocol-driven work whose ingest gate is mechanical goes to the cheapest tier that has PASSED an adequacy eval for that specific task-shape: run a blind sample through the candidate model against a stronger incumbent's baseline, measure recall of the incumbent's keeps, the mechanical-gate failure rate, and classification agreement — then promote on the numbers, not on reputation. Judgment-adjacent bounded work (implementation inside a designed frame, comprehension where synthesis quality carries the value) stays on the strong workhorse tier. Original-intensity work is never dispatched at any tier. Two riders: adequacy is per-(task-shape and protocol), never global — a model proven adequate for gated classification says nothing about its adequacy for builds; and every promotion carries downgrade triggers — a gate-rejection spike or drift on an ungated dimension re-opens the eval.
- **A second harness on a disjoint meter changes the routing default.** When the orchestrator's budget is a subscription limit and a *second* agent harness billed to a *separate* subscription is available (a different vendor's coding agent, a second account), bounded mechanically-gated freight prefers the disjoint meter once it has passed the adequacy bar for the task-shape: a failed run then costs review time plus the second meter — never the protected budget. Three riders. (1) The adequacy bar is the same earn-by-exhibit eval as any cheaper tier — and add a probe for *honesty under an unsatisfiable gate*: give the candidate a task whose visible gate cannot legitimately pass and watch whether it games the gate or reports blocked; a gate-gaming model defeats the entire delegate-and-verify economy and no benchmark score compensates (in one long-running environment, the candidate diagnosed the broken fixture, kept the correct behavior, and self-reported blocked — that exhibit carried more weight than its benchmark profile). (2) Cross-harness telemetry is often thinner — some harnesses emit no tool-event stream, so "completed" means only ran-and-reported: the orchestrator re-runs the unit's mechanical gate at ingest, always. (3) The second meter is itself finite — relocating the bottleneck is not removing it; watch both meters and say in the ledger which units rode which.
- **Dispatch in waves of no more than 5–7**, not mega-batches: read the early returns, notice a brief that is misfiring, tune the next wave before it fires.
- **A deliberately stopped agent cannot be resumed — but a fault-killed one often can.** If a background agent is deliberately interrupted or killed, continuation requests are refused and its work is cancelled — re-dispatch is the only recovery there. Design for cheap re-dispatch (versioned protocol + pointer briefs) and checkpoint early (summary first, full artifact second) so partial work survives on disk. Ordinary user messages do not kill background agents; they run across turns by design. **Fault-deaths are the exception:** an agent killed mid-flight by an API error (rate or spend limit, transient provider failure) can — on harnesses that resume from the agent's transcript — be continued with a single message, picking up with full context where it died. Try the resume before re-dispatching: in one long-running environment, two agent waves killed by the same provider spend-limit recovered 9 of 9 agents at one message each, zero re-dispatch tax, all completing clean.
- **Subagent output caps** (~32K tokens is a common ceiling; check your environment): agents producing large artifacts must write incrementally or in part-files, or the final write silently never lands.
- **Each dispatch carries a roughly flat fixed input tax** (tens of thousands of tokens of system prompt and context per agent, workload-dependent). Prefer fewer, fatter units when work items are independent anyway; amortize protocol-authoring over the full multi-round cascade, not one wave.
- **A dispatched batch is not atomic.** Poll the output files; never assume completion because the dispatch returned.
- **Ingest discipline**: default to reading the agent's compressed summary plus surgical spot-dips into the full output only where a claim bears weight for an action you are about to take. Classify claims by evidence tier — direct tool-call results (trust by default), documentation-shaped claims (verify cheaply if load-bearing), judgment (evaluate as argument, not fact). Re-reading everything an agent read defeats the delegation.
- **Long flights turn every agent-landing into a session "stop."** If your environment runs stop-time rituals (capture checks, reflection hooks, cleanup prompts), each background-agent completion re-fires them — a dozen ritual firings per flight is pure overhead. Provide a declared-flight suppression with a built-in expiry: a session-scoped sentinel that auto-re-enables after a bounded period if forgotten, so the guard cannot rot into a permanent bypass. Suppress the prompt, never the practice — the underlying discipline still runs at the flight's natural boundaries.
- **Parallel dispatch into ONE codebase: partition by exclusive file-ownership, declared in every brief.** Each brief names the files that agent owns AND names the sibling-owned files as do-not-touch; a needed-but-forbidden change goes in the agent's summary as a **ready-to-apply patch**, never applied across the boundary — the integrator applies it after the owner finishes. A deferral like that is the discipline working, not a failure. (Field-proven: three concurrent builders in one package, zero clobbers; one agent's cross-boundary fix arrived as a patch and applied clean.) Two corollaries from the same run: **literal test-COUNT gates are moving targets under parallel test-adding agents** — express shared gates as "0 fail, baseline preserved," never an absolute count; and transient editor-diagnostic noise from siblings mid-edit is expected — judge only finished gates (this extends to POST-LANDING diagnostics on your own builder's files, field-proven twice in one campaign: the language-server snapshot lags the builder's final file state, so a "complete, gates green" report followed by a wall of hard type errors is usually both honest — re-run the mechanical gate yourself as the thirty-second arbiter before treating it as a defect or dispatching a fix-round). A third, from a later run: **a seam change flushes out between-lists files** — a test that pins a module's surface against a shared contract breaks the moment the integrator widens that seam, and it naturally lives in a file neither owned nor forbidden; before dispatching, sweep for consumers of any seam you changed and assign each one explicitly (field-exhibited: a contract-compliance assertion fell between the lists; one clarifying message beat a wasted fix-round). A fourth, from a hazard bite: **parallel sessions/agents in one repo share the git INDEX** — `git add <my-paths> && git commit` sweeps whatever a sibling had staged (exhibited: a sibling's staged deletion rode an unrelated commit under the wrong message). Commit with explicit pathspecs (`git commit -- <paths>`), which commits only the named paths regardless of the index, or verify the staged set first. A refinement from a second bite: **the pathspec must be file-granular, not directory-granular** — `git add <dir>/` sweeps a sibling's uncommitted working-tree changes inside that directory (exhibited: a sibling's file-move rode a directory add under the wrong message). Name files when a sibling shares the tree.
- **Fix-rounds route back to the module's builder, never to a fresh dispatch.** When integration testing surfaces a defect in a delegated module, message the agent that built it (if your harness supports resuming completed agents) instead of briefing a new one: the builder still holds the module and its ground-truth sources in context, so the fix costs one message instead of a fresh dispatch tax plus full re-comprehension — and the ownership boundary rides along for free. Give the fix message the same shape as a brief: exhibited evidence, named hypotheses to test (not conclusions), mechanical gates, verification targets. Re-dispatch only when the original agent is gone or the defect crosses ownership boundaries. (Field-proven twice in one integration: both fix-rounds verified exact against the reference on the first try.)

## 6. Floors — when NOT to delegate

One line, three independent derivations that coincide (economic break-even, freight-times-remaining-turns, judgment-density):

> **Delegate bulk + early; keep single-cheap-call, near-session-end, and Original.**

Enumerated keeps: a single known-file read · a one-off grep or glob lookup · a two-line edit (the edit is cheaper than the prose describing it) · any read whose purpose is your own calibration or position-forming · reads in a session about to end · and **when the user is present and waiting, a thirty-second inline read beats a three-minute dispatch** — human latency is a floor the token arithmetic misses.

**The freight-already-paid refinement**: a ready-made patch whose text you already ingested (you read it to review or receive it) is nearly free to apply inline — the window cost was paid at read time; a dispatch would ADD a flat dispatch tax to save nothing. Delegating patch-application pays only when the edit content can stay out of your window end-to-end (possible only where the gate is mechanical enough that you never read the patch at all). The floor variable is whether the freight already crossed, not the edit's size.

**Reduction at source beats relocation**: a targeted semantic or index lookup that avoids a bulk read saves the freight entirely rather than moving it to a disposable window. Measured: semantic search was used two orders of magnitude less than brute-force reads — often the cheapest optimization on the board.

## 7. Session-shape guidance

The contract earns its keep in **heavy sessions** — coding, orchestration, and deep-debug work whose context heads toward the window ceiling. In the measured corpus that was roughly 15% of sessions; the numerous short lookup sessions needed none of this. The floors keep the contract inert in cheap sessions; if you notice it adding dispatch ceremony to a short session, that is the failure mode firing — stop.

## 8. Adopting, calibrating, and keeping it true

**Calibrate first:**

- **Measure your own environment before trusting the constants.** Session transcripts carry per-message `usage` fields (input, output, cache-read); an afternoon of scripting yields your own input:output ratio, your freight distribution by action class, and your heavy-session archetypes. The mechanisms here are general; every constant is local.
- **Run the retrofit test**: apply the verdict table retroactively to a real past session that went well. It should reproduce that session's keep/delegate choices, minus improvements you now see. A reproducible mismatch is a revision trigger for the table — or evidence about the session.

**Then keep it true — update your copy in the same change that creates the fact, not later:**

- **A claim fails to reproduce in your environment** → correct it immediately, date-stamped. The act of noticing is the trigger; a correction deferred is a correction lost.
- **The platform surface moves** (dispatch parameters, model-default behavior, output caps, transcript layout) → re-verify the mechanics section against the live surface before building on it. A claim verified at one version is a starting point at the next, not a guarantee.
- **A verdict flips in live use, or a new action class emerges** → update the table before the session ends, and note what exhibited it.
- **A new handoff shape or verification gate proves itself in use** — twice; once is an anecdote — → fold it in.
- **A floor mis-fires or a hazard bites that section 5 didn't warn about** → sharpen the section at session end, not next quarter.

This file is operational doctrine only — not a changelog. Keep history in version control; do not accrete per-session notes here. End every heavy orchestration session with a thirty-second check: hazard unwarned? floor mis-called? verdict that felt wrong? Fold it in before it fades — every session using the contract should leave the contract sharper.

**Escalate loading only on lived evidence.** This skill's always-loaded description carries the reflex; the body loads on demand. If live use shows the reflex under-firing under pressure — sessions bulk-reading inline when they should not — promote a three-to-five-line core to an always-loaded surface. If that too proves insufficient, add a mechanical nudge at the decision moment (a pre-tool hook on heavy reads past a size floor). Escalate on exhibited need, never anticipation: every always-loaded line spends the very budget this contract protects.

**The keep-column is the job, not the residue.** Thinking is ~92% of what an orchestrator generates and cannot be delegated by definition. The point of the whole discipline is not to do less — it is to ensure the thinking happens over distilled evidence in a lean window, spending the scarce mind entirely on the work only it can do.
