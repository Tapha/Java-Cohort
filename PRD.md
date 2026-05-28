You are expanding a Founding Spec into a Product Requirements Document. The
Founding Spec is authoritative: every requirement you write must trace to a
line in it. The PRD stays implementation-free — no tech stack, no file names,
no boilerplate. That binding happens one stage down.

INPUT — Founding Spec (source of truth):
{{FOUNDING_SPEC}}

Produce a PRD with:

1. GOALS / NON-GOALS — inherited from the Founding Spec, sharpened. Do not add
   goals the spec doesn't support.
2. USER JOURNEYS — the core loop from the spec, broken into concrete
   step-by-step flows. Cover the happy path first, then the two or three that
   matter.
3. SCREENS / SURFACES — every distinct view the journeys require, with its
   purpose in one line. No visual design.
4. STATES & EDGE CASES — empty, loading, error, offline, permission-denied,
   first-run. The states are where products actually live or die.
5. MVP BOUNDARY — a hard line. Everything above it ships in v1; everything
   below it is explicitly deferred (and named, so it isn't silently lost).
6. REQUIREMENT TRACE — a table: each requirement → the Founding Spec line it
   derives from.

Rules:
- A requirement with no trace to the Founding Spec is either scope creep
  (delete it) or a gap in the spec (CASCADE-BACK it — do not adopt it).
- The MVP boundary is a commitment, not a suggestion. If everything feels
  essential, the Founding Spec's non-goals were too weak — cascade back.

{{DRIFT_AND_CASCADE_FOOTER}}
