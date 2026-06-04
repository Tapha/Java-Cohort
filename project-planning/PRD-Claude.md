# PRD — Fridge → Meal (v1)

**Binding rule:** the Founding Spec is the source of truth. Every requirement below
traces to a spec line (see §6). Anything with no trace is either scope creep (cut) or a
spec gap (cascaded back in §7 — not adopted). This document is implementation-free: no
stack, no surfaces named by technology, no boilerplate. That binds one stage down.

---

## 1. GOALS / NON-GOALS

### Goals (inherited, sharpened)
- **G1 — Intercept the order-out decision at the open fridge.** Win the ~30-second
  standoff against delivery, at the moment of hunger, with no planning assumed. *(Spec §1, §2, §7)*
- **G2 — One casual fridge photo → one cookable meal + time estimate.** No manual
  ingredient entry; no question asked before the answer is delivered. *(§1, §3, §7)*
- **G3 — App-open to first viable suggestion ≤ ~10s.** *(§3, §5)*
- **G4 — Camera live on open.** Zero navigation between launch and capture. *(§3)*
- **G5 — Always return a real, cookable result from what's visible.** "No meal found" is
  a failure state, not an output. *(§7)*
- **G6 — Drive repeat capture across weeks.** Optimise for the tenth photo, not the first. *(§5)*

### Non-Goals (hard, inherited from §6)
- **NG1 — Not a recipe database or browser.** No place to go looking through recipes. *(§6)*
- **NG2 — Not a meal planner.** Knows only *now* — no weekly plans, no "what to eat Tuesday." *(§6)*
- **NG3 — Not an inventory/pantry manager.** The photo is the inventory, from scratch, every
  time. No expiry, no stock levels, no history. *(§6, §7)*
- **NG4 — Not a social network.** Sharing and AI-generated "look at my food" images are cut
  entirely from v1. *(§6, §8)*
- **NG5 — Not a grocery/shopping app.** No "add this ingredient to improve the meal." *(§6, §8)*

No goal is added beyond what the spec supports. Specifically excluded for lack of any
source line: accounts/identity, personalisation, nutrition tracking, ratings.

---

## 2. USER JOURNEYS

### J1 — Happy path (the core loop)
1. User stands at the open fridge, hungry, leaning toward delivery. *(§2)*
2. Opens app → **camera is already live**. No home screen, no menu. *(§3, §4 G4)*
3. Points at the fridge and captures (tap shutter; auto-capture is an open option, not required). *(§3)*
4. Brief processing state inside the ≤~10s budget. *(§3, §5)*
5. Receives **one meal**: title, the from-the-fridge ingredients it uses, the steps, and a
   time estimate. *(§1, §3)*
6. Taps **"Cook this"** → commits → steps view. *(§5 — the cook-it tap)*
7. Optionally marks **"I cooked it"** (the confirmation signal). *(§5)*

### J2 — Thin / near-empty fridge (degenerate inventory)
- Photo yields few or awkward ingredients. The app must still return the **most viable real
  thing** from what's visible — it does not recommend purchases (NG5). *(§7 always-something)*
- If the fridge genuinely cannot produce a cookable meal, the two stated assumptions collide
  ("always something" vs. "real, cookable"). **Unresolved — see CB4.**

### J3 — Misdetection ("that's not what's in my fridge")
- Detection is wrong or incomplete. The recovery is **re-shoot**, *not* a manual ingredient
  editor — the spec's hardest line is that manual correction collapses the loop back into
  data entry and there is no product. *(§7 KILLS-IF-FALSE, §3)*
- Whether *any* lightweight correction affordance is permitted sits directly on that fault
  line. **Not adopted silently — see CB5.**

### J4 — Repeat user, new session
- Returns at 8pm having used it at 1pm. The loop runs cold again: a fresh photo, no memory,
  no saved state required (NG3). This is the intended shape — repeat capture is the product. *(§5, §6)*

---

## 3. SCREENS / SURFACES

| ID | Surface | Purpose (one line) |
|----|---------|--------------------|
| S1 | Live Camera (default) | The app opens here; capture the fridge with zero navigation. |
| S2 | Processing | Holds the ≤~10s gap and sets expectation; a single, honest waiting state. |
| S3 | Meal Result | The one meal: title, used ingredients, steps, time estimate, "Cook this." |
| S4 | Cook / Steps | Steps to follow after committing; carries the "I cooked it" confirmation. |
| S5 | Permission primer + OS prompt | First-run path to camera access (the loop is non-functional without it). |
| S6 | Re-shoot | Return to camera from a bad, wrong, or empty result. |

No settings, profile, saved-recipes, browse, or feed surface exists — each would serve a
non-goal (NG1–NG4).

---

## 4. STATES & EDGE CASES

- **First-run** — camera permission not yet granted → primer (S5) → OS prompt → live camera.
- **Permission denied** — existential: the entire loop is the camera. Needs an unmistakable
  re-grant path, not a dead end. *(§3)*
- **Loading** — the ≤~10s processing window; show progress, never freeze. Overrun is a tracked
  failure against the §5 latency criterion.
- **Error (vision/inference fails)** — offer retry / re-shoot. **Never** drop the user into
  manual entry. *(§7)*
- **Empty / degenerate fridge** — return the best viable real meal; the truly-nothing case is
  unresolved (**CB4**).
- **Bad photo (blurry, dark, not-a-fridge)** — §4 assumes casual badly-lit photos work, so the
  bar to prompt a re-shoot must be high; over-asking is loop death by friction. Re-shoot prompt
  only when the frame is genuinely unreadable.
- **Offline** — spec is silent; reading the fridge likely needs connectivity. Propose a graceful
  "can't read your fridge without a connection" state. **Flagged — CB6.**

---

## 5. MVP BOUNDARY

This line is a commitment.

### Above the line — ships in v1
S1–S6 · G1–G6 · single-meal output · time estimate · "Cook this" + "I cooked it"
confirmation · re-shoot recovery · permission flow · the core states above · **fridge photo only.**

### Below the line — explicitly deferred (named so nothing is lost silently)
- **Multiple meal options** instead of one — v1 commits to one (§1, §3); multi parked as a test, not a v1 feature. *(§8)*
- **AI-generated food images.** *(§6, §8)*
- **Sharing / social / Pinterest–Instagram–TikTok cluster.** *(§6, §8)*
- **Saved recipes / favourites / history.** *(§6 NG3)*
- **Cupboard / pantry / storage photos** beyond the fridge. *(§6, §8)*
- **Ingredient / grocery recommendations** to improve the meal. *(§6 NG5, §8)*
- **Meal planning / weekly plans.** *(§6 NG2)*
- **Manual ingredient editing/correction** — deferred pending **CB5** (it sits on the §7 fault line).
- **Monetisation / paywall** — no source line exists; cannot be written (**CB2**).
- **Dietary / allergy filters** — listed below the line *under protest*: this is safety-adjacent
  and may have to jump the line. The PRD cannot responsibly choose "later" or "v1" without a
  ruling — **CB1**.

---

## 6. REQUIREMENT TRACE

| # | Requirement | Founding Spec line |
|---|-------------|--------------------|
| G1 | Intercept the order-out decision at the open fridge | §1 (product truth), §2 ("win that thirty-second standoff"), §7 (interception not inspiration) |
| G2 | One photo → one cookable meal + time estimate, no manual entry, no question before the answer | §1, §3 ("get one meal… with a time estimate"; "asks a question before answering, the product is dead") |
| G3 | ≤ ~10s app-open to first suggestion | §3, §5 ("under ~10 seconds") |
| G4 | Camera live on open | §3 ("camera is already live") |
| G5 | Always return a real cookable result; no-meal = failure | §7 ("returns *something* to cook… 'no meal found' is a failure state") |
| G6 | Repeat capture across weeks | §5 ("Repeat photographing… the tenth is a product") |
| NG1 | Not a recipe database/browser | §6 |
| NG2 | Not a meal planner | §6 |
| NG3 | Not an inventory/pantry manager (photo is the inventory) | §6, §7 |
| NG4 | Not a social network; AI food images cut | §6, §8 |
| NG5 | Not a grocery/shopping app | §6, §8 |
| J1 | Core loop flow | §3 (the whole loop) |
| J2 | Degenerate-fridge handling | §7 (always-something) |
| J3 | Re-shoot over manual edit on misdetection | §7 (KILLS-IF-FALSE), §3 |
| J4 | Cold repeat session, no memory | §5, §6 (NG3) |
| S1 | Live Camera default | §3 |
| S2 | Processing state | §3, §5 |
| S3 | Meal Result | §1, §3 |
| S4 | Cook / Steps + confirmation | §5 (cook-it tap, confirmation) |
| S5 | Permission flow | §3 (camera-first necessity) |
| S6 | Re-shoot | §3, J2/J3 |
| Latency tracking | Overrun of 10s budget is a failure | §5 |
| Time-of-day signal | Open clusters at meal times | §5 |
| Cook-it + deferral signals | Sessions end in "I cooked this"; order-out deferral | §5 |

Every above-the-line requirement traces. No orphan requirements were adopted.

---

## 7. CASCADE-BACK (gaps the Founding Spec must close)

These are not decisions the PRD is allowed to make alone. Each is a missing or conflicting
source line; adopting an answer silently would be drift.

- **CB1 — Dietary / allergy filters (§8, unresolved).** Suggesting a meal to someone with an
  allergy is a harm surface, not a feature preference. The spec must rule: either (a) v1 carries
  a minimal allergen-exclusion step, accepting the friction cost, or (b) the spec states the
  accepted risk explicitly. "Defer and decide later" is not a safe default here.
- **CB2 — Monetisation.** Absent entirely from the spec. With no source line, no pricing, paywall,
  or trial requirement can be written. Spec gap, not a PRD omission.
- **CB3 — "Order-out deferral" is not yet measurable (§5).** "Self-reported or inferred" is a
  direction, not a definition. Instrumentation can't bind until the spec gives an operational one.
- **CB4 — "Always something" vs "real, cookable" collide on an empty fridge (§7).** When the
  visible ingredients genuinely can't make a meal, the two stated assumptions contradict. The spec
  must rule which yields — return the closest real thing, or admit the degenerate "nothing" state.
- **CB5 — Manual correction on misdetection (§7).** Re-shoot is the spec-pure recovery, but a
  stubborn misread can trap a user. Any tap-to-remove-wrong-item affordance drifts from the spec's
  hardest line. The spec should explicitly permit or forbid it.
- **CB6 — Offline behaviour.** Spec is silent; the loop probably needs connectivity. Needs a
  defined state before it binds.

---

*Footer: `{{DRIFT_AND_CASCADE_FOOTER}}` came through unfilled again — same as in the Founding
Spec. Left out rather than invented. Hand me the template and I'll fit the PRD to it.*
