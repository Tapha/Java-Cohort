You are grounding a floating PRD against a real codebase — producing the
Constrained PRD. Every requirement must now bind to something that actually
exists in the boilerplate: a file, a pattern, a table, a route convention.
This is where the Plan Stack (what we intend) meets the Reality Stack (what
the code already is). Friction between them is the most valuable output of
this stage — surface it, never paper over it.

INPUT A — PRD (what we want):
{{PRD}}

INPUT B — Boilerplate manifest (what exists): structure, conventions, schema,
auth model, routing, available libraries.
{{BOILERPLATE_MANIFEST}}

Produce a Constrained PRD with:

1. BINDING TABLE — every PRD requirement → one of:
     REUSE (existing file/pattern, named exactly),
     EXTEND (existing thing + the specific addition),
     NEW (net-new file/table/route, with where it lives by convention).
   Default to REUSE. NEW is a cost; justify each one in a phrase.
2. FRICTION LOG — every place the PRD wants something the boilerplate makes
   expensive, awkward, or impossible. For each: the requirement, the friction,
   and the cheapest honest option (change the boilerplate / change the
   requirement / accept the cost). Do not silently pick one.
3. DATA MODEL DELTA — tables/columns/relations to add, expressed in the
   boilerplate's actual ORM/conventions.
4. CONVENTION NOTES — the existing patterns every downstream task must follow
   (naming, error handling, folder layout) so agents don't reinvent them.

Rules:
- Reuse beats new every time it's close. The boilerplate is the constraint
  that makes the agent's job tractable downstream — honour it.
- A requirement that can't bind to reality without major surgery is a drift
  signal. Log it in FRICTION; if it breaks the MVP boundary, CASCADE-BACK to
  the PRD.

{{DRIFT_AND_CASCADE_FOOTER}}
