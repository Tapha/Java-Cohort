Below is a **dependency-ordered, testable roadmap**. Each phase ends in a **clear demo state** and every binding-table item is assigned exactly once.

***

# ROADMAP — Fridge → Meal (v1)

## PHASE 1 — “One Photo → One Meal (Stubbed Core Loop)”

**Goal:** Prove the end-to-end loop with a fake AI response.

**Delivers (bindings):**

* Backend
  * `MealController` (multipart POST) ✅
  * `MealService` (orchestration) ✅
  * `MealVisionPort` (interface ONLY, stub impl) ✅
* Frontend
  * `CameraScreen` as initial route ✅
  * Capture → upload via axios ✅
  * `ProcessingScreen` ✅
  * `MealResultScreen` (title + steps + time) ✅
* Error basics (minimal)
  * plumbing (no full handler yet)

**Notes:**

* AI adapter returns a **hardcoded meal**.
* No permissions polish, no retries, no real detection.

**DEMO STATE:**
Open app → camera is live → take photo → see loading → receive a fixed meal with steps and time.  
✅ “It feels like the product,” but intelligence is fake.

**Depends on:** none

***

## PHASE 2 — “Real Vision → Real Meal (AI Adapter Integration)”

**Goal:** Replace stub with actual vision + meal generation.

**Delivers (bindings):**

* AI Integration
  * `MealVisionPort` (final shape) ✅
  * `<Vendor>MealVisionAdapter` (SDK integration, composition) ✅
* Backend orchestration decision (**F2 resolved implicitly by implementation**) ✅
* Config
  * `application.yml`, `@ConfigurationProperties` ✅
* Client optimization
  * Image downscale/compress before upload (**F3**) ✅

**Notes:**

* First real evaluation of:
  * ingredient detection reliability (§7 critical assumption)
  * latency against \~10s budget

**DEMO STATE:**
Take a real fridge photo → get a **different meal based on contents** (sometimes wrong, but clearly image-driven).  
✅ “It actually understands the fridge.”

**Depends on:** Phase 1

***

## PHASE 3 — “Core UX Completion (Commit & Repeat Loop)”

**Goal:** Complete the intentional user loop (cook + repeat).

**Delivers:**

* Frontend
  * `CookScreen` ✅
  * "Cook this" CTA ✅
  * "I cooked it" client analytics event ✅
* Navigation
  * Re-shoot flow (nav back to camera) ✅
* Flow completeness
  * full J1 + J4 journeys ✅

**Notes:**

* Still minimal on edge cases.
* No persistence introduced (NG3 enforced).

**DEMO STATE:**
Take photo → see meal → tap “Cook this” → follow steps → tap “I cooked it” → return → take another photo.  
✅ “This is the full intended behavior loop.”

**Depends on:** Phase 2

***

## PHASE 4 — “Robustness & Failure Handling”

**Goal:** Make the loop survivable under real-world conditions.

**Delivers (bindings):**

* Backend
  * `GlobalExceptionHandler` (`@ControllerAdvice`) ✅
  * Domain exceptions extending `RuntimeException` ✅
* Frontend + states
  * Permission primer + OS prompt (S5) ✅
  * Permission denied recovery ✅
  * Error → re-shoot mapping ✅
  * Loading state integrity ✅
* Edge-case behavior
  * Bad photo handling
  * Retry/re-shoot paths

**Notes:**

* Enforces:
  * “Never manual entry”
  * “Error = re-shoot”
* Offline state acknowledged but not deeply implemented (CB6 still open)

**DEMO STATE:**
Deny camera → recover.  
Take bad photo → prompted to retry.  
AI fails → re-shoot path appears.  
✅ “It doesn’t break under stress.”

**Depends on:** Phase 3

***

## PHASE 5 — “Infrastructure, Testing, and Guardrails”

**Goal:** Make the system stable, testable, and runnable in production environments.

**Delivers (bindings):**

* Testing
  * JUnit setup ✅
  * `MealService` tests with mocked `MealVisionPort` ✅
  * Adapter tests ✅
  * `@SpringBootTest` controller tests ✅
* Infrastructure
  * Dockerfile (multi-stage) ✅
  * docker-compose (app + SQL) ✅
* CI/CD
  * Jenkins pipeline (build → test → docker → deploy) ✅

**Notes:**

* SQL DB remains unused (by design, NG3).
* Latency checks should appear in tests (G3 enforcement).

**DEMO STATE:**
Run CI → tests pass → docker build → container runs → app works end-to-end with real AI.  
✅ “This can ship and be iterated safely.”

**Depends on:** Phase 4

***

# DEPENDENCY SUMMARY

| Phase   | Depends on |
| ------- | ---------- |
| Phase 1 | —          |
| Phase 2 | Phase 1    |
| Phase 3 | Phase 2    |
| Phase 4 | Phase 3    |
| Phase 5 | Phase 4    |

No forward dependencies exist. Ordering is valid.

***

# COVERAGE CHECK (Binding Table → Phases)

* Frontend bindings → Phases 1, 3, 4
* Backend controller/service → Phase 1
* AI adapter → Phase 2
* Config → Phase 2
* Error handling → Phase 4
* Navigation reuse → Phase 3
* Infra/Test → Phase 5
* Persistence → intentionally unused (consistent with PRD)

✅ Nothing orphaned  
✅ Nothing duplicated

***

# DRIFT & CASCADE FOOTER

**No drift introduced.** All phases strictly execute the PRD without adding scope.

**Open cascade-backs remain unchanged (not silently solved):**

* CB1 — dietary/allergy handling
* CB4 — empty fridge contradiction
* CB5 — manual correction vs purity
* CB6 — offline UX definition
* CB7 — repeat metrics vs identity
* CB8 — photo retention policy

***

If you want, I can convert this into a **weekly execution plan (who builds what, in what order)** or a **ticket backlog directly aligned to these phases**.
