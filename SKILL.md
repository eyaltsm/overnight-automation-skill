---
name: overnight-automation
description: Standing operating mode for continuing an already-scoped build autonomously while the founder is away or asleep, with Codex (or an Opus-class agent as fallback) standing in for the founder's own approval on in-flight decisions. Use when the founder says something like "I'm going to sleep/heading out, keep working," "full automation," "run this overnight," or otherwise hands off an already-agreed direction and will not be reachable for a while. Does not license starting new, unscoped initiatives on your own - see Scope below.
---

# Overnight Automation

A way to keep making real progress on a mission the founder already agreed to, for the stretch where they
are not reachable to approve each step - without freezing at every checkpoint a normal session would stop
at, and without ever silently assuming authority the founder didn't grant.

## When this applies

The founder has to do two things for this mode to activate:

1. **Name or confirm the mission.** "Continue the Launch Factory roadmap," "keep building PROJECT_DNA,"
   "finish what we were doing" - some concrete direction that already exists, even if only as a plan in this
   conversation. A bare "work autonomously" with no named mission is not enough to invent new work from
   nothing; if nothing is in flight and no mission was named, say so instead of guessing one.
2. **Say they're stepping away and want work to continue.** "I'm going to sleep," "I'm heading out," "run
   this overnight," "I don't need to be here for this," or equivalent. This is what distinguishes overnight
   automation from a normal working session where the founder is present and would otherwise be asked.

Both together activate the mode. Either alone does not: a named mission with the founder still present is
just normal work (ask as you normally would); the founder leaving with nothing in flight is idle time, not
a standing instruction to start something new.

## What changes while this mode is active

**Normal session default:** architectural decisions, design choices, and anything the
`superpowers:brainstorming` skill would gate get the founder's own sign-off before proceeding.

**Under this mode:** that sign-off is obtained from Codex or an Opus-class agent instead, and work continues
without waiting for the founder. Concretely:

- **Every decision that would otherwise need the founder's approval** - a spec before implementation, a
  design review before a plan, a non-obvious architectural fork, a scope call the plan itself doesn't
  resolve - gets a real consultation instead, not a self-approval:
  - **Primary: Codex.** Write the question to a file, run it via
    `codex exec --sandbox read-only --skip-git-repo-check -c model_reasoning_effort=<level> "$(cat prompt.md)"`
    in the background, read the result when it completes. Use `low` effort for routine scoping/review
    questions, `medium` for a real design or architecture review, matching this session's own established
    convention. Treat the result as the actual decision, not a rubber stamp to skim past - if it finds a
    real problem (see this session's own Launch Factory B0-B3 history for worked examples: a Critical
    exemption-rule bug, a path-traversal bug, a snapshot-consistency race - all caught this way), fix it
    before moving on, exactly as if the founder had caught it themselves.
  - **Fallback: an Opus-class agent.** If Codex's usage is exhausted or it's unavailable, dispatch a
    subagent on the most capable available model (e.g. `opus`) for the same role - architecture review,
    challenging the design, looking for overengineering - via the `Agent` tool. This is a real second
    opinion, not a formality: brief it with the actual question and the actual stakes, the same way you'd
    brief Codex.
  - Record which one was used and its outcome in the project's own status file (see Record-keeping below).
- **Routine implementation questions never stop the work.** Naming, schema shape, which file a function
  goes in, whether to add a test - decide with your own best judgment, document the decision inline (a
  comment, a ledger entry, a spec's own "ambiguity resolution" note), and keep going. This is not new under
  this mode - it's already how `superpowers:subagent-driven-development` and
  `superpowers:executing-plans` expect rulings to work - but under this mode there is no founder to escalate
  to even for the judgment calls those skills would otherwise let you raise, so the bar for "decide it
  yourself" is lower and the bar for "this needs a human" is correspondingly higher.
- **The full pipeline still runs, just with the gate swapped.** Brainstorm → design → written spec → design
  review (Codex/Opus in place of the founder) → `superpowers:writing-plans` → implementation
  (`superpowers:subagent-driven-development` or `superpowers:executing-plans`) → final review → status
  update. Skip none of these stages - the swapped gate is still a real gate, not a license to go straight
  from idea to code.

## What does NOT change

- **The four hard stops are still hard stops, with no Codex/Opus override:** an irreversible or destructive
  operation, a security-sensitive action, a side effect outside the workspace that norms say to ask about
  first (pushing to a shared branch the founder hasn't pre-authorized, publishing, deploying to production,
  spending money), and a fork so fundamental that every path forward is a real guess about what the founder
  wants, not an implementation detail. For these, stop and wait for the founder - Codex/Opus approval does
  not substitute for the founder's own judgment on these specifically, because the whole premise of this
  mode is "decide the things a careful engineer would decide without asking," and these are exactly the
  things a careful engineer still asks about.
- **Starting a genuinely new, unscoped initiative still needs the founder.** This mode continues an agreed
  mission; it does not grant standing authority to pick the next one. If the current mission finishes with
  time still available, say so and stop, or pick up the next item from an explicit backlog the founder
  already named - don't invent a new direction and call it continuity.
- **Quality bar is unchanged.** No fake evidence, no silent fallbacks, no skipped tests, no claiming
  something works without having verified it. Working slower and correct beats fast and wrong - this mode
  buys continuity through the founder's absence, not permission to cut corners that wouldn't be cut with
  them in the room.

## Record-keeping

The founder will read what happened after the fact, not live. Make that review cheap:

- Keep a single running status file per mission (this session's own convention: a top-level
  `OVERNIGHT_STATUS.md` in the relevant repo) updated continuously, not just at the end - a session that
  stops partway through still leaves a readable record.
- For every Codex/Opus consultation: what was asked, which one answered, the substance of the answer, and
  what was done about it.
- For every ruling made without consultation (the routine-decision case above): what was decided and why,
  inline where the decision lives (commit message, spec note, ledger line) - not just in the status file.
- At natural milestones (a phase/task/plan completing), commit and push if the founder has already
  standing-authorized pushes for this repo; otherwise leave the commits local and say so in the status file.
- Never schedule a check-in with the founder as if they were present (no "let me know if this looks right"
  aimed at someone who said they're asleep) - the status file is the communication channel until they
  return.

## Worked precedent

This session's own Launch Factory build (chaos-os, 2026-10-03) is the reference implementation of this
mode: B1 and B2 were each scoped via a Codex consultation, specced, design-reviewed by Codex (catching real,
reproducible bugs each time - not rubber-stamps), planned, and implemented via
`superpowers:subagent-driven-development`, all without the founder present, with every decision and its
reasoning recorded in `OVERNIGHT_STATUS.md` for review on return. Use that as the shape to match, not this
skill's prose alone.
