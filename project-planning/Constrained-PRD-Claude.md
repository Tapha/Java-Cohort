# CONSTRAINED PRD — Fridge → Meal (v1)

**Reality Stack:** greenfield. No pre-existing repo — the chosen stack *is* the constraint.
Bindings are to **framework default conventions**, not to files that already exist.

**Stack:** React Native (client) · Java / Spring Boot (backend) · SQL (database) ·
Docker (hosting) · JUnit (test) · Jenkins (CI/CD) · AI service SDK wrapped as a Java
**Object Adapter** (GoF, composition).

**Bucket meaning in greenfield:**
- **REUSE** — a capability the framework gives you; you configure it, you don't build it.
- **EXTEND** — a framework-provided base you add specific code onto.
- **NEW** — code you write. Most of a greenfield app. *Cheap-NEW* = framework idiom dictates the
  shape (a clear template exists). *Real-NEW* = a genuine decision is owed.

---

## 1. BINDING TABLE

### Frontend — React Native

| PRD ref | Bind | Concretely (by convention) | Note / justification |
|---|---|---|---|
| **S1, G4** — camera live on open | NEW (cheap) + REUSE | `CameraScreen` as the **initial route** of the React Navigation stack; camera surface via `react-native-vision-camera` | RN ships no nav and no camera; both are the de-facto standard libs (REUSE). "Initial route = camera" *is* the whole G4 binding — nothing above it. |
| **J1.3** — capture + upload | NEW (cheap) + REUSE | capture → file URI from vision-camera; POST multipart via a single `axios` client module | downscale before upload — see **F3** |
| **S5** — permission primer + OS prompt | NEW (primer) + REUSE (OS) | primer component → `Camera.requestCameraPermission()`; iOS `NSCameraUsageDescription`; Android camera permission | the OS prompt is platform-provided (REUSE); the primer that precedes it is NEW |
| **S2** — processing | NEW (cheap) | `ProcessingScreen` route during the request; progress indicator, never a freeze | binds to the latency budget — **F2** |
| **S3** — meal result | NEW | `MealResultScreen`: title, used-ingredients, steps, time estimate, "Cook this" CTA | renders the backend `MealResponse` |
| **S4** — cook / steps + "I cooked it" | NEW | `CookScreen` route; "I cooked it" fires a **client analytics event** | confirmation is telemetry, not a DB write (NG3) |
| **S6** — re-shoot | REUSE (nav) | navigate back to `CameraScreen` (the initial route) | re-shoot is "pop to camera" — no new surface mechanics |

### Backend — Java / Spring Boot

| PRD ref | Bind | Concretely (by convention) | Note / justification |
|---|---|---|---|
| **G1, G2** — one photo → one meal | NEW (controller) + REUSE (multipart) | `MealController` `@RestController`, `@PostMapping(consumes = MULTIPART_FORM_DATA)` taking `MultipartFile`, returns `MealResponse` DTO | Spring's `MultipartFile` + `spring.servlet.multipart.*` config is built-in (REUSE) |
| **G2** — orchestration (image → ingredients → meal) | NEW (service) | `MealService` `@Service`; depends on the **AI port**, never the SDK | call-shape (one hop vs two) is the **F2** decision |
| **§7, S2/error** — error → re-shoot, never manual entry | NEW + EXTEND + REUSE | `@ControllerAdvice GlobalExceptionHandler`; domain exception **EXTENDs** `RuntimeException`; returns a structured error the client maps to **S6** | Spring's advice/handler mechanism is REUSE; "error *means* re-shoot" is the §7 binding |
| config (AI key/endpoint, multipart limits) | NEW (class) + REUSE | `application.yml` + `@ConfigurationProperties` class | externalised config is Spring convention; no secrets in code |

### AI integration — the stated Object Adapter

| PRD ref | Bind | Concretely (by convention) | Note / justification |
|---|---|---|---|
| AI service via **Object Adapter** | NEW (port) + NEW (adapter) + REUSE (SDK) | domain interface `MealVisionPort` (e.g. `Meal suggestMeal(byte[] image)`); `<Vendor>MealVisionAdapter implements MealVisionPort` holding the SDK client **as a field** (composition = GoF object adapter); the AI SDK is the wrapped library | honours the explicitly-named pattern. Keeps `MealService` vendor-agnostic and lets JUnit mock the port. The method signature (returns a value vs a stream) is coupled to **F2**. |

### Persistence — SQL

| PRD ref | Bind | Concretely | Note / justification |
|---|---|---|---|
| core-loop persistence | **none** | no `@Entity`, no `JpaRepository`, no migration for the core loop | NG3 + no accounts + saved-recipes deferred ⇒ **the loop is stateless**. The shipped SQL DB is untouched by v1's core path. Delta empty pending CB7/CB8. |

### Infra / Test / CI

| PRD ref | Bind | Concretely (by convention) | Note / justification |
|---|---|---|---|
| **Docker** hosting | REUSE + NEW | multi-stage `Dockerfile` (build with Maven/Gradle → run on JRE); `docker-compose.yml` with app + SQL service | RN app isn't containerised (mobile). SQL container present though the loop doesn't use it — see persistence row. |
| **JUnit** test | REUSE + NEW | `src/test/java`; JUnit 5; `MealService` unit-tested with **Mockito-mocked `MealVisionPort`**; adapter contract/integration-tested; `@SpringBootTest` at controller level | mocking the port is *why the port exists* |
| **Jenkins** CI/CD | NEW + REUSE | `Jenkinsfile` (declarative): build → test (JUnit) → docker build → deploy | standard pipeline |

Every above-the-line PRD requirement binds. Nothing in the table serves a non-goal.

---

## 2. FRICTION LOG

Each entry: the requirement, the friction the stack creates, and the cheapest honest options.
**No option is picked here** — these are rulings owed, not made.

### F1 — Zero-question loop vs an open cost tap
- **Requirement:** G4 / §3 — no login, no question before the answer.
- **Friction:** the core path is a **public, unauthenticated image endpoint sitting in front of a
  metered AI SDK.** Greenfield Spring ships no auth (good — nothing forces a login wall) but also
  no rate-limiting. Anyone who finds the endpoint can run up the AI bill. The §3 no-login rule and
  basic cost protection pull against each other.
- **Cheapest honest options:**
  (a) ship open for v1, add an IP/device **rate-limit filter** (small NEW, e.g. bucket4j) — accept residual abuse risk;
  (b) mint a **silent anonymous device token** on first launch, invisible to the user, gate the endpoint with it — adds one call inside the budget and *seeds* CB7;
  (c) accept the exposure for v1 and revisit.

### F2 — 10-second budget vs a two-hop AI call  *(G3 at risk until decided)*
- **Requirement:** G3 — app-open → first viable suggestion ≤ ~10s.
- **Friction:** the natural design is **two sequential model calls** through one adapter —
  (1) image → ingredient list, (2) ingredients → meal + steps + time — plus multipart upload, plus
  possible cold start (Spring app or AI endpoint), all inside 10s. The §7 KILLS-IF-FALSE assumption
  (one casual photo → usable ingredients) lives entirely inside hop 1.
- **Cheapest honest options:**
  (a) **single combined multimodal call** (image → meal in one shot) — collapses two hops to one, tighter latency, but couples detection and synthesis and gives less control over each;
  (b) keep two hops but **stream / show partial progress** in S2 to protect *perceived* latency;
  (c) treat 10s as aspirational (the spec's own "~").
  The adapter's method signature (return a `Meal` vs return a stream) rides on this choice.

### F3 — Upload weight vs latency
- **Requirement:** G3 + §4 "casual badly-lit photo works."
- **Friction:** a full-res phone photo is multi-MB; uploading over cellular alone can eat the budget
  before any model runs.
- **Cheapest honest option (likely just adopt):** **client-side downscale/compress** before upload
  via vision-camera capture settings. Flagged so it isn't silently skipped — it's load-bearing on G3.

### F4 — Offline: confirmed dead, no longer "likely"
- **Requirement:** CB6 (PRD left this uncertain — "probably needs a connection").
- **Friction:** the AI adapter is a network call, so **offline = no loop, with certainty.** The
  uncertainty in CB6 resolves to *certain*. The offline *state* (what the user sees) is still
  undesigned.
- **Resolution owed:** a defined "can't read your fridge without a connection" state before this binds.

---

## 3. DATA MODEL DELTA

**Empty.** The core loop persists nothing: NG3 (the photo is the inventory, every time), no accounts,
no saved recipes in v1. The shipped SQL database is present but untouched by v1's core path.

The only candidate table is the CB7 repeat-capture event store, which is **not adopted** — it's a
cascade-back, not a delta. *If* CB7 were ruled server-side, the minimal shape would be a single
`capture_event` entity (`@Entity`) — anonymous device id, timestamp, event type — via `JpaRepository`
and a Flyway migration `V1__capture_event.sql`. **Until that ruling, no delta.**

---

## 4. CONVENTION NOTES — what every downstream task must follow

**Package layout (Spring), under `com.<org>.fridgemeal`:**
`controller` · `service` · `dto` · `config` · `exception` · domain port in `domain` (or `port`) ·
adapter in `infrastructure/adapter`. The AI **never** touches a controller or `MealService`
directly — only through `MealVisionPort`. New AI work extends the **adapter**, not the service.

**Boundaries:** DTOs at the edge (`MealResponse`, error DTO); never serialise a domain object
straight out of a controller.

**Errors:** raised as domain exceptions, handled **only** in `@ControllerAdvice`. Controllers do not
try/catch-and-return. Error responses carry a code the client maps — the "re-shoot" signal (§7) is a
code, so S6 is driven by the response, not guessed by the client.

**Tests:** every `MealService` test mocks `MealVisionPort` (Mockito); **no test calls the real SDK**;
`@SpringBootTest` for controller-level checks. The latency budget (G3) gets at least one explicit
timing assertion or it isn't real.

**React Native:** functional components + hooks; React Navigation stack with **`CameraScreen` as the
initial route** — load-bearing for G4, *do not insert a splash or home above it*. Screens own their
navigation; all API calls go through **one** `axios` client module.

**Config:** externalised (`application.yml` + `@ConfigurationProperties`); AI key via env, injected
through Docker / Jenkins — never in source.

**Migrations:** Flyway (`V__*.sql`). v1 adds none, but the convention is set so the first table — if
CB7 lands — follows it rather than being invented ad hoc.

---

## 5. CASCADE-BACK (spec rulings the stack forces or sharpens)

Not decisions this stage may make alone.

- **CB1 (carried) — Dietary / allergy filters.** Still unresolved, still safety-adjacent, still the
  one item that could force a v1 scope change. Stack-neutral — the spec must rule (minimal
  allergen-exclusion step in v1, or stated accepted risk). "Decide later" is not a safe default.
- **CB7 (carried, sharpened) — Repeat-capture metric vs no-identity.** G6 is **above the line** and
  unmeasurable without a persistent identifier, which §6 excludes. Ruling needed: **client-side**
  install-ID analytics (never touches the backend; identity-lite, arguably consistent with
  no-accounts) **vs server-side** anonymous-event table (a NEW table and an identity concept the spec
  didn't authorise). Note F1's device-token option would seed the server-side path.
- **CB8 (carried) — Photo retention / privacy.** Ephemeral pass-through (image → adapter → discarded)
  binds to **nothing**. Any retention = NEW object store / blob column / lifecycle policy. The
  default-clean answer is ephemeral, but it must be *stated* clean, not assumed.
- **CB6 → resolved-to-confirmed.** The dependency is now certain (F4); the offline-state *design* call
  remains open.

---

*Footer: `{{DRIFT_AND_CASCADE_FOOTER}}` came through unfilled again — fourth document running. Left
out rather than invented. Hand me the template and I'll fit this Constrained PRD to it.*
