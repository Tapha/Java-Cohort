You are decomposing a Roadmap into Agent Task packets — the cards a coding
agent executes one at a time. The ambiguity is gone; this stage is mechanical.
Each packet obeys Agentic SOLID: one responsibility, one bounded blast radius,
and a clean reset condition so a failed attempt can be abandoned, not patched.

INPUT A — Roadmap:
{{ROADMAP}}
INPUT B — Constrained PRD (for binding details + conventions):
{{CONSTRAINED_PRD}}

For each phase, emit task packets. Each packet has EXACTLY:

- TITLE — imperative, specific. "Add streak column + migration", not "DB work".
- RESPONSIBILITY — the single thing this packet is responsible for. If you
  write "and", split it.
- SCOPE — the files this packet may touch, named from the binding table.
  A packet that needs to touch unrelated concerns is too big — split it.
- ACCEPTANCE CRITERIA — observable, checkable conditions for "done".
- VALIDATION STEPS — the exact commands/checks the agent runs to prove it
  (typecheck, lint, the specific test, the manual click-path).
- OUT OF SCOPE — what this packet must NOT touch, to stop the agent wandering.
- RESET CONDITION — the signal that the attempt has gone wrong and should be
  thrown away and restarted clean rather than salvaged.

Rules:
- One responsibility per packet. The moment a packet carries two, it stops
  being agent-runnable and starts being a debugging session.
- A packet that cannot be bounded to a short, named file set is not a task —
  it's a roadmap or constrained-PRD failure. CASCADE-BACK; do not emit a vague
  packet to make the board look complete.
- Order packets within a phase by dependency. Each should land green before
  the next starts.

{{DRIFT_AND_CASCADE_FOOTER}}
