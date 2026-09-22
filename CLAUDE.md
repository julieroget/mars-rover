# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project state

This repository is currently in the **Design** phase of a three-phase workflow (Intent → Design/Spec → Build). No implementation code exists yet — only the intent and specification documents for the first feature (`mars-rover-simulator`). There are no build, lint, or test commands to run yet; once the Build phase starts, the accepted spec mandates a dependency-free HTML/JS/CSS artifact (see below), so expect it to run directly in a browser with no compilation step.

## Workflow: Intent → Spec → Build

Work is driven by two custom skills that must be followed exactly as written — read `.claude/skills/intent/SKILL.md` and `.claude/skills/spec/SKILL.md` before acting on either phase. Both skills and their documents are written in French; keep new intent/spec content in French to match.

- **Intent** (`/intent`, or invoked implicitly to clarify a need): produces `intent/<slug>/intent.md`. The skill must not make product decisions on the author's behalf, must ask one clarifying question at a time, and must leave anything undecided in a `Questions ouvertes` section rather than inventing an answer.
- **Spec** (`/spec intent/<slug>/intent.md`, model-invocation disabled — must be called explicitly): produces `intent/<slug>/spec.md` from an *accepted* intent (accepted = merged to `main` via PR, verified against the PR decision, not just presence on `main`). It derives numbered requirements (`EX-01`, `EX-02`, …) with scenario/behavior/origin, flags open questions as reserves (`R-01`, …) with origin/impact/decision-owner, and must record its own generation context (exact invocation prompt, skill file paths + git commit hashes used) at the end of the document. It must not write code or a build plan — that belongs to Build.

Key procedural rules shared by both skills:
- Never commit, push, or open a PR without explicit human confirmation of the drafted content first.
- Work happens on a dedicated branch created from the latest `main`, never directly on `main`. Intent branches follow `claude/intent-<slug>`.
- A human (the Product Owner) accepts intents and specs by merging their PR; the skill itself never merges.
- When revising an existing spec, preserve previously recorded human decisions and only re-open the reserves the revision request names.

## Repository layout

- `intent/<slug>/intent.md` — the accepted problem statement for one feature (problem, proposed outcome, stakeholders, constraints, open questions).
- `intent/<slug>/spec.md` — the derived specification for that same feature (requirements, design, reserves/decisions, open questions, generation context). References its source intent by path.
- `.claude/skills/intent/SKILL.md`, `.claude/skills/spec/SKILL.md` — the process definitions above; treat them as authoritative over any paraphrase here.

## Accepted design for `mars-rover-simulator`

`intent/mars-rover-simulator/spec.md` is the accepted spec for the first (and currently only) feature, and is the reference for its eventual Build phase:

- Rover state = position `(x, y)` + orientation `N/S/E/W`; map = grid of free/obstacle cells.
- Commands (advance, turn right/left 90°) are interpreted one at a time, in order; advancing checks the target cell first and leaves the rover in place if that cell is an obstacle or is outside the map bounds (off-map treated identically to an obstacle — decision R-01).
- Map cells accept two interchangeable symbol pairs with identical semantics: 🟩 (free) / 🌳 (obstacle), or 🟫 (free) / 🪨 (obstacle).
- Unrecognized commands and unrecognized map symbols are ignored (simulation continues, state unchanged) rather than raising an error — decision R-03.
- Implementation target: a self-contained HTML/JavaScript/CSS artifact compatible with Claude Code web artifacts — no server dependency, no build step, runnable directly in a browser — decision R-04. Input (start point, map, command list) and output (final position/orientation) go through a web form/text-area UI in that same artifact, not a CLI or file format — decision R-02.
- The simulation logic should be kept separable from the web I/O layer so it stays independently testable (proposed, not yet finalized).
