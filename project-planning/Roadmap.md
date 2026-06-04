You are sequencing a Constrained PRD into a phased Roadmap. Every phase must
end in a demonstrable, testable state — something a human can look at and say
"yes, that works" before the next phase begins. Order by dependency, not by
excitement.

INPUT — Constrained PRD (source of truth):
{{CONSTRAINED_PRD}}

Produce a Roadmap:

- PHASES in dependency order. Data model and core API before screens; screens
  before integrations; integrations before polish/payments.
- For each phase: a name, its goal in one line, the constrained-PRD items it
  delivers, and its DEMO STATE — exactly what you can do with the app when the
  phase is done.
- PHASE 1 is the thinnest possible thing that proves the core loop end-to-end.
  If phase 1 is bloated, the MVP boundary upstream is wrong.
- DEPENDENCIES made explicit: phase N may only depend on phases < N.

Rules:
- No phase depends on a later phase. If you can't order something without a
  forward dependency, the constrained PRD has a circularity — CASCADE-BACK.
- Every binding-table item lands in exactly one phase. Nothing orphaned,
  nothing duplicated.

{{DRIFT_AND_CASCADE_FOOTER}}
