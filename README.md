# oeconomia

A [Claude Code](https://claude.com/claude-code) skill that teaches an orchestrating session to spend its context budget where only it can serve — and to route everything else to subagents.

*Oeconomia* (Greek οἰκονομία): household stewardship. When the main session runs on a scarce, rate-limited model and strong workhorse models are available for subagent dispatch, the orchestrator's budget is dominated by one thing: re-reading its own accumulated context window on every turn. This skill is a delegation contract built on that fact — which work the session keeps, which work it dispatches, what shape the handoff takes, and how returned work is verified without re-doing it.

## The core ideas

- **The window is the cost.** Measured across ~600 working sessions: orchestrator input outweighs output ~263:1, and ~98.5% of input is cache-read of the accumulated window. The true cost of a bulk read is `freight × remaining-turns` — so delegate bulk, early; a subagent's window is disposable.
- **Classify by judgment-density, not token count.** Delegate work whose output is mechanical given the available substrate; keep work whose output must be originated. The boundary is where verification economics invert: below it, checking is cheaper than doing; above it, checking *is* re-doing.
- **Delegation is substrate-extension.** Write the criteria down once — a versioned protocol file, a schema, a worked exemplar — and work that was undelegatable judgment becomes protocol-application a subagent carries safely.
- **Verify mechanically, never trust.** Constrain the output's shape until its gate is mechanical: tests for code, schema validation for structured output, and exhibit anchors (every claim carries a verbatim source quote, machine-checked at ingest) for judgment work.
- **Economy licenses errands, never avoidance.** Genuinely pipeline-shaped problems — several independent concerns needing parallel investigation and synthesis — deserve a real multi-agent pipeline. Saving tokens is not a reason to collapse one.

## Install

Clone directly into your skills directory (the repo is the skill folder):

```bash
# personal (all projects)
git clone https://github.com/vincitamore/oeconomia.git ~/.claude/skills/oeconomia

# or per-project
git clone https://github.com/vincitamore/oeconomia.git .claude/skills/oeconomia
```

The skill's `description` frontmatter acts as an always-loaded reflex (explicit `model:` on every dispatch; delegate bulk + early; keep judgment; respect the floor). The body — the full contract, verdict table, handoff shapes, hazards, and floors — loads only when invoked with `/oeconomia` or when the session reaches for it before heavy work.

## Calibrate it to your environment

The mechanisms are general; the constants are local. Session transcripts carry per-message `usage` fields — measure your own input:output ratio and freight distribution before trusting any number in the skill, and run the retrofit test described in its final section against a real past session. The skill tells you how.
