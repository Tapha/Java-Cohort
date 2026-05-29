# ROADMAP — Fridge → Meal (v1)

**Source of truth:** the Constrained PRD. Every binding-table item lands in exactly one
phase below; the coverage table (§7) proves nothing is orphaned or duplicated.

**Ordering principle:** dependency, not excitement. No phase depends on a later phase.

---

## ORDERING NOTE — why the default layering bends here

The canonical order is *data model → core API → screens → integrations → polish/payments.*
Two properties of this product reshape it, and both are honest deviations, not drift:

1. **No data-model phase.** The Constrained PRD's data-model delta is **empty** (NG3: the
   photo is the inventory, every time; no accounts; saved-recipes deferred). There is nothing
   to build before the API. A data-model phase would materialise *only* if CB7 is ruled
   server-side — see §6.
2. **The integration is the core loop.** "Photo → AI → meal" is not a later integration hung
   off a working app; it is the product. The single KILLS-IF-FALSE assumption (§7: a casual
   fridge photo yields a cookable meal) lives entirely inside the AI adapter. Stubbing it to
   "build screens first" would defer the only risk worth de-risking first.

So **Phase 1 is a vertical slice** through camera → endpoint → adapter → SDK → result screen,
not a horizontal layer. This is *not* a circularity: every dependency runs backward (the screen
needs the API, the API needs the adapter — none needs a later phase). The demo boundary is
vertical, that's all. The "API before screens" rule is honoured *inside* Phase 1 by a curl
checkpoint: the backend is proven against a real fridge photo before the RN client is wired.

**Phases 3 and 4 are mutually independent** (states vs. measurement — neither depends on the
other). They're ordered by risk: the §7 "never manual entry / always a real result" guarantees
are product *correctness* and ship before *measurement*.

---

## PHASE 0 — SPINE
**Goal:** a green pipeline and a booting shell, so every feature phase lands on rails.

**Delivers (binding items):**
- **Docker** — multi-stage `Dockerfile` (build with Maven/Gradle → run on JRE); `docker-compose.yml` with app + SQL service.
- **JUnit** — test harness + JUnit 5 runner + one passing placeholder test. *Establishes the convention that every later phase ships its own tests* (the per-component tests ride with their phase; they are not separate items).
- **Jenkins** — `Jenkinsfile`: build → test → docker build. (Deploy stage stubbed; activated in Phase 5 — same item, not re-placed.)
- *Scaffold, not binding items:* empty Spring Boot app with a `/health` endpoint; blank RN app that installs.

**DEMO STATE:** push a commit → Jenkins goes green (build + placeholder test + image build);
`docker compose up` boots the backend and `/health` returns OK; the RN app installs on a device
and opens to a blank screen. Nothing does anything yet — but the rails are real and tested.

**Depends on:** — (root)

---

## PHASE 1 — THE LOOP RUNS ONCE  *(thinnest end-to-end slice)*
**Goal:** prove a real fridge photo yields a real, cookable meal end-to-end, under budget.

**Delivers (binding items):**
- **Object Adapter** — `MealVisionPort` (domain interface) + `<Vendor>MealVisionAdapter` holding the SDK client as a field (GoF object adapter) + the AI SDK itself. *This is the risk; it ships first.*
- **`MealController`** — `@RestController`, `@PostMapping(consumes = MULTIPART_FORM_DATA)` taking `MultipartFile`, returns `MealResponse`.
- **`MealService`** — orchestration; depends on the port, never the SDK.
- **Config** — `application.yml` + `@ConfigurationProperties` (AI key/endpoint, multipart limits).
- **J1.3 — capture + upload** — vision-camera capture call + `axios` multipart POST + **client-side downscale** (F3). Triggered by any throwaway control for now — the *entry UX* is Phase 2.
- **S3 — Meal Result screen** — title, used ingredients, steps, time estimate. (No "Cook this" nav yet — that's S4 in Phase 2.)
- *Tests ship here* (Phase 0 convention): `MealService` unit-tested with a Mockito-mocked `MealVisionPort`; adapter contract-tested; `@SpringBootTest` on the controller; **one explicit latency timing assertion** (G3).

**Internal build order (satisfies "API before screens"):** backend first → **curl checkpoint**
(POST a real fridge JPEG, get a cookable-meal JSON back in budget, server-side) → then wire the
RN capture + result screen.

**DEMO STATE:** on a phone, trigger capture, photograph your **actual fridge**, and within
~10 seconds a real, cookable meal — title, the ingredients it's using, steps, time estimate —
appears on screen. Collapses §7 KILLS-IF-FALSE (does the AI read a casual fridge photo?),
**F2** (two hops + upload under 10s?), and **F3** (upload weight) in a single demo.

**Depends on:** Phase 0.

---

## PHASE 2 — THE LOOP AS A PRODUCT  *(real entry + recovery shell)*
**Goal:** turn the one-shot demo into something a person opens and uses repeatedly without
hitting a wall.

**Delivers (binding items):**
- **S1 / G4 — camera live on open** — `CameraScreen` as the **initial route** of the React Navigation stack; zero navigation between launch and capture. *Reuses Phase 1's capture component — not a rebuild, a placement.*
- **S5 — permission primer + OS prompt** — soft-ask primer → OS prompt → and an unmistakable **denied-state re-grant path** (a dead-ended camera is an existential failure).
- **S2 — processing screen** — honest waiting state during the request; progress, never a freeze.
- **S4 — cook / steps + "I cooked it"** — "Cook this" CTA on S3 → `CookScreen` steps view → "I cooked it" button (the confirmation event is *emitted* here; *measured* in Phase 4).
- **S6 — re-shoot** — navigate back to the camera (initial route) from a result.

**DEMO STATE:** open the app — the camera is already live (S1/G4). Shoot → processing state
(S2) → meal → "Cook this" into a steps view (S4) → mark "I cooked it" → "re-shoot" (S6) and go
again. Deny camera permission and you're shown how to re-grant, not dead-ended (S5). The loop is
now genuinely *usable*, not just demonstrable.

**Depends on:** Phase 1 (wraps the working slice). Transitively Phase 0.

---

## PHASE 3 — HONEST UNDER STRESS  *(degenerate + failure states)*
**Goal:** make the loop honest and recoverable when the fridge is thin, the photo is bad, the
AI fails, or there's no connection — enforcing G5 ("always a real result; no-meal = failure")
and §7 ("never drop into manual entry").

**Delivers (binding items):**
- **§7 / S2-error contract** — `@ControllerAdvice GlobalExceptionHandler` + domain exception (EXTENDs `RuntimeException`) + a structured error **code the client maps to S6 re-shoot**. (Phase 1 had throwaway error handling; the real contract lands once, here.)
- **J2 — degenerate fridge** — return the most viable *real* thing from what's visible; never recommend purchases (NG5). ⚠ **CB4-blocked** for the truly-nothing case (closest-real vs. admit-nothing).
- **J3 — misdetection → re-shoot** — policy: re-shoot is the recovery, *not* a manual editor. Re-shoot already exists (S6). ⚠ **CB5-shaped**: ships as re-shoot-only by default; any tap-to-remove affordance is blocked pending the ruling.
- **Bad-photo handling** — prompt re-shoot *only when the frame is genuinely unreadable* (§4: the bar is high; over-asking is loop death by friction).
- **Offline state** — detect no-connection → a defined "can't read your fridge without a connection" state. Dependency is **confirmed** (F4); ⚠ **CB6-blocked** on the exact UX design.
- **Latency-overrun behaviour** — the processing state (S2) times out → re-shoot rather than hanging. (The overrun *metric* is Phase 4; this is the *behaviour*.)

**DEMO STATE:** shoot a near-empty fridge → the most viable real meal it can make (not a shopping
list). Shoot a blurry/dark/non-fridge frame → a "try again" prompt only when truly unreadable.
Force the AI call to fail → retry/re-shoot, **never** a manual ingredient form. Go offline → an
honest connection-required state. (CB4 truly-empty, CB5 correction affordance, CB6 offline UX all
flagged as blocked — see §6.)

**Depends on:** Phase 2 (adds states to the usable loop). Transitively Phases 0–1.

---

## PHASE 4 — MEASUREMENT  *(does it work as a product?)*
**Goal:** instrument the §5 success criteria so the core bets become measurable.

**Delivers (binding items):**
- **Latency tracking** — overrun of the ~10s budget logged as a failure against the §5 criterion.
- **Time-of-day signal** — app-open timestamps, to show clustering around meal times (the "decision moment" bet).
- **Cook-it + deferral signals** — collect/analyse the cook-it and "I cooked it" events (emitted in Phase 2). ⚠ **CB3-blocked** for *order-out deferral* ("self-reported or inferred" is a direction, not a definition).
- **Repeat-capture metric (G6)** — "the tenth photo." ⚠ **CB7-blocked**: client-side install-ID analytics vs. server-side `capture_event` table. *Server-side resolution materialises a data-model delta + possibly the F1 device token* — see §6.

**DEMO STATE:** after a week of real use, pull an event view showing: when the app was opened
(time-of-day), how long each suggestion took (and overruns), how often "Cook this" was tapped and
"I cooked it" confirmed. The **deferral** and **repeat-across-weeks** metrics are stubbed and
labelled blocked pending CB3 and CB7.

**Depends on:** Phases 1–2 (the screens that emit the events). *Independent of Phase 3* — ordered
after it by risk, not dependency.

---

## PHASE 5 — SHIP  *(the "polish/payments" slot, accounted for honestly)*
**Goal:** make v1 deployable and operable; close the infra loop.

**Delivers:**
- **Activate the Jenkins deploy stage** — un-stub the deploy step established in Phase 0 (same item; deploys the proven build to a real environment).
- **Payments — NULL.** ⚠ **CB2**: monetisation is absent from the spec entirely. With no source line, no pricing, paywall, or trial can be written. The canonical terminal "payments" phase is *empty by spec*, not by omission — recorded so it isn't silently dropped.
- **Polish** — S2 progress refinement; the optional auto-capture (J1.3 noted it as "open option, not required"). Both are below-line; included only as light finish.

**DEMO STATE:** a commit ships through the full pipeline (build → test → docker → **deploy**) to a
real environment; the deployed app runs the full loop end-to-end. There is no paywall — because the
spec has none.

**Depends on:** everything (Phases 0–4).

---

## 6. CASCADE-BACK — what blocks full sequencing

The roadmap is **fully ordered, with no forward dependencies, for everything bindable now.** The
items below are *named with their target phase* so a ruling drops into a known slot rather than
reopening the sequence.

- **CB1 — Dietary / allergy filters.** ⚠ *Could jump the MVP line.* If ruled **v1**, an
  allergen-exclusion step inserts into the AI prompt/adapter (**Phase 1**) and possibly a filter
  surface (**Phase 2/3**) — it would *re-weight Phase 1*. Until ruled, **unplaceable**; the roadmap
  is incomplete on this one axis. Safety-adjacent: "decide later" is not a safe default.
- **CB3 — Order-out deferral metric.** Blocks part of **Phase 4**. The rest of Phase 4 ships; this
  signal stays stubbed until operationally defined.
- **CB4 — Empty-fridge resolution.** Blocks the truly-nothing branch of **Phase 3 / J2**. The
  thin-but-makeable case ships; the degenerate case waits.
- **CB5 — Manual correction on misdetection.** Shapes **Phase 3 / J3**. Forbidden → re-shoot-only
  (already built). Permitted → a correction affordance is added in Phase 3.
- **CB6 — Offline state design.** Dependency confirmed (F4); the *state's UX* blocks part of
  **Phase 3**. Detection scaffolds; the exact state waits.
- **CB7 — Repeat-capture metric (client vs. server).** Blocks part of **Phase 4** *and* determines
  whether persistence stays empty. **Server-side** → a `capture_event` `@Entity` + `JpaRepository`
  + Flyway migration appears (a new data-model delta, slotting before/within Phase 4), plus likely
  the F1 anonymous device token. **Client-side** → no backend change.
- **CB8 — Photo retention / privacy.** Ephemeral pass-through (the default) binds to nothing and
  needs no phase. Any retention → an object-store/blob delta materialises (Phase 1 backend +
  infra). Must be *stated* clean, not assumed.
- **CB2 — Monetisation.** Renders the **Phase 5 payments slot null** (above).

**Persistence note:** core-loop persistence is "none" and therefore lands in *no phase* — correctly.
It materialises only via CB7 (server-side) or CB8 (retention).

---

## 7. COVERAGE TABLE — every binding item, exactly one phase

| Binding item (Constrained PRD §1) | Phase |
|---|---|
| Docker | 0 |
| JUnit (harness + per-phase test convention) | 0 |
| Jenkins (build→test→docker→deploy; deploy activates in 5) | 0 |
| Object Adapter (`MealVisionPort` + adapter + SDK) | 1 |
| `MealController` endpoint (multipart) | 1 |
| `MealService` orchestration | 1 |
| Config (`application.yml` + `@ConfigurationProperties`) | 1 |
| J1.3 — capture + upload (+ downscale) | 1 |
| S3 — Meal Result screen | 1 |
| S1 / G4 — camera live on open | 2 |
| S5 — permission primer + OS prompt | 2 |
| S2 — processing screen | 2 |
| S4 — cook / steps + "I cooked it" (screen + event emission) | 2 |
| S6 — re-shoot | 2 |
| §7 / S2-error — exception handler + error-code → S6 | 3 |
| Latency tracking | 4 |
| Time-of-day signal | 4 |
| Cook-it + deferral signals | 4 |
| Core-loop persistence (= none) | none (materialises only via CB7/CB8) |

Every above-the-line binding item appears once. The below-the-line / deferred cluster (multiple
options, AI food images, sharing/social, saved recipes, cupboard photos, grocery recs, meal
planning, manual correction, monetisation, allergy filters) is **deliberately not phased** — it
is post-v1 per the MVP boundary, not orphaned.

---

*Footer: `{{DRIFT_AND_CASCADE_FOOTER}}` came through unfilled again — fifth document running.
Left out rather than invented. Hand me the template and I'll fit this Roadmap to it.*
