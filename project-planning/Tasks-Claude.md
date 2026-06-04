# AGENT TASK PACKETS — Fridge → Meal (v1)

**Sources:** the Roadmap (phase structure) + the Constrained PRD (binding details, file
names, conventions). Each packet maps to a binding-table item; ordering within a phase is by
dependency (stated per phase). Each packet has **exactly** seven fields. Validation commands
assume Maven (`./mvnw …`); Gradle equivalents apply.

## Emittable vs. blocked — the honest map

| Phase | State |
|---|---|
| 0 — Spine | **Fully packetized** (5) |
| 1 — Loop runs once | **Fully packetized** (7) — shape encodes the F2 synchronous default |
| 2 — Loop as a product | **Fully packetized** (5) |
| 3 — Honest under stress | **Partial** (4 emitted) — CB4, CB5, CB6-refinement blocked, not emitted |
| 4 — Measurement | **Partial** (4 emitted) — CB3, CB7 blocked; CB7 server-side spawns an unwritable persistence sub-phase |
| 5 — Ship | **Mostly blocked** — deploy target unspecified (gap); payments null (CB2); only below-line polish is emittable (optional) |

Conventions every packet obeys (Constrained PRD §4): Spring packages
`controller · service · dto · config · exception · domain · infrastructure/adapter`; AI reaches
the app **only** through `MealVisionPort`; errors flow **only** through `@ControllerAdvice`; RN is
functional components + one `axios` client module + React Navigation with `CameraScreen` as the
initial route; no secrets in source.

---

## PHASE 0 — SPINE
**Order:** P0.1 → P0.2 (parallel) ; then P0.3, P0.4 (after P0.1) ; then P0.5 (after P0.3 + P0.4).

### P0.1 — Initialize the Spring Boot backend with a liveness endpoint
- **TITLE:** Initialize Spring Boot backend with Actuator health.
- **RESPONSIBILITY:** a backend that boots and proves liveness.
- **SCOPE:** `pom.xml` (Spring Boot + Actuator), `…/Application.java`, `application.yml` (port, app name only).
- **ACCEPTANCE CRITERIA:** `./mvnw spring-boot:run` boots clean; `GET /actuator/health` → `200 {"status":"UP"}`.
- **VALIDATION STEPS:** `./mvnw clean package`; `./mvnw spring-boot:run`; `curl -s localhost:8080/actuator/health`.
- **OUT OF SCOPE:** any controller/service/adapter; AI code; entities/migrations; Docker; CI.
- **RESET CONDITION:** won't compile/boot from a clean checkout, or domain code was added to make health work — delete, re-init from Spring Initializr defaults.

### P0.2 — Initialize the React Native app shell
- **TITLE:** Initialize React Native app shell.
- **RESPONSIBILITY:** an installable RN app that opens to a blank screen.
- **SCOPE:** RN project root (`package.json`), `App.tsx` (blank root).
- **ACCEPTANCE CRITERIA:** builds and installs on device/simulator; opens to a blank screen; no crash.
- **VALIDATION STEPS:** install deps; start Metro; build to device/sim; observe blank screen.
- **OUT OF SCOPE:** navigation, camera, any screen S1–S6, axios, any API call.
- **RESET CONDITION:** won't install clean, or screens/nav/camera were added — delete, re-init from the RN template.

### P0.3 — Add Docker packaging (backend image + compose with SQL)
- **TITLE:** Add Dockerfile + docker-compose (app + SQL).
- **RESPONSIBILITY:** a reproducibly containerized backend with the compose topology.
- **SCOPE:** `Dockerfile` (multi-stage build→JRE), `docker-compose.yml` (app + SQL service), `.dockerignore`.
- **ACCEPTANCE CRITERIA:** `docker compose up` boots the backend; health returns 200 from the container; the SQL container runs (unused is fine).
- **VALIDATION STEPS:** `docker build .`; `docker compose up -d`; `curl` health on the mapped port; `docker compose ps` shows app + db.
- **OUT OF SCOPE:** any schema/migration/entity (no Flyway, no tables — that is CB7/CB8); CI; app code.
- **RESET CONDITION:** image won't build or container fails health, *and* the fix would mean editing app code — discard Dockerfile/compose, restart; packaging adapts to the app.

### P0.4 — Add the JUnit test harness with a placeholder test
- **TITLE:** Add JUnit 5 harness + smoke test.
- **RESPONSIBILITY:** a working test runner proving the test path is wired.
- **SCOPE:** test deps in `pom.xml` (JUnit 5, Mockito), `src/test/java/…/SmokeTest.java` (one trivial assertion).
- **ACCEPTANCE CRITERIA:** `./mvnw test` runs; the placeholder test passes; a report is produced.
- **VALIDATION STEPS:** `./mvnw test`; observe 1 test, green.
- **OUT OF SCOPE:** tests for MealService/adapter/controller (those ship in Phase 1); any production code.
- **RESET CONDITION:** runner won't execute, or tests were written against code that doesn't exist yet — delete, restart with one assertion.

### P0.5 — Add the Jenkins pipeline (build → test → image; deploy stubbed)
- **TITLE:** Add Jenkinsfile (build, test, image; deploy stub).
- **RESPONSIBILITY:** CI that builds, tests, and builds the image on every commit.
- **SCOPE:** `Jenkinsfile` only.
- **ACCEPTANCE CRITERIA:** stages run build → test (P0.4 green) → docker build (P0.3); a `deploy` stage exists as a no-op stub; pipeline green on a clean commit.
- **VALIDATION STEPS:** trigger the pipeline; observe each stage green; confirm `deploy` present and stubbed.
- **OUT OF SCOPE:** a real deploy target (gap — see Phase 5); app/test/Docker changes.
- **RESET CONDITION:** pipeline can't go green without changing app code — discard the Jenkinsfile, restart; CI adapts to the build.

---

## PHASE 1 — THE LOOP RUNS ONCE
**Order:** P1.1 → P1.2 → (P1.3, P1.4) → P1.5 → P1.6 → P1.7.
**F2:** all packets below encode the *synchronous* port shape (F2 option a/c). The three packets
that touch the wire (P1.1, P1.2, P1.5, P1.6) name streaming in OUT OF SCOPE; an F2=streaming
ruling re-shapes them.

### P1.1 — Define the MealVisionPort and Meal domain types
- **TITLE:** Define `MealVisionPort` + `Meal` domain types.
- **RESPONSIBILITY:** the vendor-agnostic domain contract for "image → meal."
- **SCOPE:** `domain/MealVisionPort.java`, `domain/Meal.java` (title, used-ingredients, steps, time estimate).
- **ACCEPTANCE CRITERIA:** `Meal suggestMeal(byte[] image)` compiles; `Meal` carries the four fields; `domain` imports neither the SDK nor `org.springframework`.
- **VALIDATION STEPS:** `./mvnw compile`; import-scan / ArchUnit check that `domain` has no SDK or Spring import.
- **OUT OF SCOPE:** the adapter, the SDK, the controller, JSON/DTO; **a streaming return type** (F2 b) — not built here.
- **RESET CONDITION:** `domain` ends up importing the SDK or Spring — delete the package, restart; vendor-isolation is the point.

### P1.2 — Implement the AI adapter behind the port
- **TITLE:** Implement `<Vendor>MealVisionAdapter` via composition.
- **RESPONSIBILITY:** adapt the AI SDK to `MealVisionPort` (object adapter, SDK held as a field).
- **SCOPE:** `infrastructure/adapter/<Vendor>MealVisionAdapter.java`, SDK dependency in `pom.xml`.
- **ACCEPTANCE CRITERIA:** implements the port; holds the SDK client as a field (not inheritance); an integration test against the real SDK returns a populated `Meal` from a sample fridge image.
- **VALIDATION STEPS:** `./mvnw compile`; run the adapter integration test (the *only* test allowed to hit the real SDK); assert non-empty `Meal`.
- **OUT OF SCOPE:** `MealService`; the controller; the RN client; the port's signature (P1.1 owns it); **streaming** (F2 b). The one-vs-two-hop call choice lives *inside* this file and is invisible to callers.
- **RESET CONDITION:** you change the port to fit the SDK, or extend the SDK class instead of holding it — discard, restart; composition is the contract.

### P1.3 — Add the MealService orchestration
- **TITLE:** Add `MealService` orchestration.
- **RESPONSIBILITY:** orchestrate image bytes → `Meal` through the port.
- **SCOPE:** `service/MealService.java` (`@Service`, depends on `MealVisionPort`).
- **ACCEPTANCE CRITERIA:** calls the port, returns the `Meal`; a unit test with a **Mockito-mocked** `MealVisionPort` verifies orchestration without the SDK.
- **VALIDATION STEPS:** `./mvnw test -Dtest=MealServiceTest`; green.
- **OUT OF SCOPE:** the adapter/SDK (mocked here); the controller; HTTP/multipart; DTOs.
- **RESET CONDITION:** the test imports the SDK or the concrete adapter — discard; the service depends only on the port.

### P1.4 — Add config for AI credentials and multipart limits
- **TITLE:** Add `AiProperties` + multipart config.
- **RESPONSIBILITY:** externalised configuration for the AI client and upload limits.
- **SCOPE:** `config/AiProperties.java` (`@ConfigurationProperties`), `application.yml` (AI key/endpoint via env placeholders, `spring.servlet.multipart.*`).
- **ACCEPTANCE CRITERIA:** AI key/endpoint resolve from env (no literal secret in source); multipart max sizes set; the adapter consumes the config.
- **VALIDATION STEPS:** boot-check that properties bind; `grep -ri "api[_-]key" src/` returns no literal key.
- **OUT OF SCOPE:** the controller, the RN client, business logic.
- **RESET CONDITION:** a secret lands in source, or config types leak into `domain` — discard, restart.

### P1.5 — Add the MealController multipart endpoint (happy path)
- **TITLE:** Add `MealController` multipart endpoint.
- **RESPONSIBILITY:** accept a fridge image over HTTP and return a meal.
- **SCOPE:** `controller/MealController.java` (`@PostMapping(consumes=MULTIPART_FORM_DATA)`, `MultipartFile`), `dto/MealResponse.java` (edge DTO — never serialise `Meal` directly).
- **ACCEPTANCE CRITERIA:** POSTing a multipart image returns a `MealResponse` JSON (four fields); a `@SpringBootTest` (mocked service) verifies the happy path; **a latency timing assertion exists** (G3).
- **VALIDATION STEPS:** `./mvnw test -Dtest=MealControllerTest`; **curl checkpoint** — `curl -F image=@fridge.jpg localhost:8080/…/meal` returns a populated meal JSON within budget.
- **OUT OF SCOPE:** the RN client; **auth/rate-limiting** (F1, unruled); the **error contract** (Phase 3 — happy path only); persistence; **streaming** (F2 b).
- **RESET CONDITION:** error-advice, auth, or rate-limiting appears here — discard that; this is the happy-path endpoint only.

### P1.6 — Add RN capture + multipart upload
- **TITLE:** Add RN capture + upload to the endpoint.
- **RESPONSIBILITY:** capture a fridge photo and POST it, holding the response.
- **SCOPE:** RN `src/api/client.js` (the single axios module), a capture call via `react-native-vision-camera` with **client-side downscale** (F3), behind a throwaway trigger.
- **ACCEPTANCE CRITERIA:** tapping the trigger captures a downscaled image, POSTs it, and the `MealResponse` lands in state/log.
- **VALIDATION STEPS:** run on device; tap trigger; observe the request fires and a meal payload is received (console is fine — render is P1.7).
- **OUT OF SCOPE:** navigation/initial-route (Phase 2 / S1-G4); the result UI (P1.7); permissions (Phase 2); processing screen (Phase 2); **streaming consumption** (F2 b).
- **RESET CONDITION:** navigation, permissions, or result UI get wired here — discard those; capture→upload→payload only.

### P1.7 — Add the S3 Meal Result screen
- **TITLE:** Add `MealResultScreen` (render one meal).
- **RESPONSIBILITY:** render one received meal.
- **SCOPE:** RN `screens/MealResultScreen.jsx` (title, used ingredients, steps, time estimate).
- **ACCEPTANCE CRITERIA:** given a `MealResponse`, displays all four content elements (S3).
- **VALIDATION STEPS:** run on device; complete a capture (P1.6) and **see the meal render — the Phase 1 end-to-end demo**.
- **OUT OF SCOPE:** the "Cook this" CTA + S4 (Phase 2); re-shoot (Phase 2); error/empty states (Phase 3).
- **RESET CONDITION:** cook/steps or error states appear here — discard; result render only.

---

## PHASE 2 — THE LOOP AS A PRODUCT
**Order:** P2.1 → then P2.2, P2.3, P2.4, P2.5 (P2.4/P2.5 need P1.7; otherwise parallel after P2.1).

### P2.1 — Add the navigation stack with Camera as the initial route
- **TITLE:** Add React Navigation stack, Camera as initial route (S1/G4).
- **RESPONSIBILITY:** the nav stack whose initial route is the live camera.
- **SCOPE:** RN `navigation/AppNavigator.jsx` (`initialRouteName` = Camera), `screens/CameraScreen.jsx` (wraps P1.6's capture as a screen).
- **ACCEPTANCE CRITERIA:** cold-launch opens directly to a live camera, zero intervening screens; capture still fires the existing upload.
- **VALIDATION STEPS:** cold-launch; observe camera live on first frame, no splash/home; capture → upload path fires.
- **OUT OF SCOPE:** permission primer (P2.2); processing (P2.3); cook/re-shoot wiring.
- **RESET CONDITION:** a splash/home/menu appears above the camera, or capture is rebuilt rather than reused — discard, restart from the initial-route placement.

### P2.2 — Add camera permission primer, OS prompt, and denied recovery
- **TITLE:** Add permission primer + denied re-grant path (S5).
- **RESPONSIBILITY:** the camera-access lifecycle, including recovery from denial.
- **SCOPE:** RN `screens/PermissionPrimerScreen.jsx`, `Camera.requestCameraPermission()`, iOS `Info.plist` (`NSCameraUsageDescription`), Android manifest camera permission, a denied-state re-grant affordance.
- **ACCEPTANCE CRITERIA:** first run → primer → OS prompt → camera; if denied, an unmistakable re-grant path (settings deep-link / re-prompt), never a dead end.
- **VALIDATION STEPS:** fresh install → primer → grant → camera; reset permission, deny → observe recovery path, not a frozen screen.
- **OUT OF SCOPE:** processing/result/cook screens; the nav structure (P2.1 owns initial route).
- **RESET CONDITION:** denial dead-ends, or the iOS usage string is missing (the app will crash) — discard, restart; recovery is the point.

### P2.3 — Add the Processing screen during the request
- **TITLE:** Add `ProcessingScreen` (in-flight waiting state, S2).
- **RESPONSIBILITY:** an honest waiting state while the meal request is in flight.
- **SCOPE:** RN `screens/ProcessingScreen.jsx` + a state/nav change in the capture→result flow to show it during the request.
- **ACCEPTANCE CRITERIA:** after capture, a processing state shows with progress (never a freeze); on response it advances to the result.
- **VALIDATION STEPS:** run on device; capture; observe processing → result; confirm no freeze.
- **OUT OF SCOPE:** timeout/overrun behaviour (Phase 3 / P3.3); result content (P1.7); the latency metric (Phase 4).
- **RESET CONDITION:** timeout or error handling appears here — discard; in-flight waiting state only.

### P2.4 — Add the Cook/Steps screen, "Cook this" nav, and "I cooked it" event
- **TITLE:** Add `CookScreen` + Cook-this nav + I-cooked-it event (S4).
- **RESPONSIBILITY:** the commit-and-confirm step of the loop.
- **SCOPE:** RN `screens/CookScreen.jsx`, a "Cook this" CTA on `MealResultScreen.jsx` (nav S3→S4), an "I cooked it" button that **emits** a client analytics event.
- **ACCEPTANCE CRITERIA:** "Cook this" → steps view; "I cooked it" fires the event (emission only — collection is Phase 4).
- **VALIDATION STEPS:** run on device; from a result, tap "Cook this" → steps; tap "I cooked it" → confirm the event fires (log/inspector).
- **OUT OF SCOPE:** analytics collection/dashboard (Phase 4 / P4.4); the deferral metric (CB3); backend persistence of the event (NG3); re-shoot (P2.5).
- **RESET CONDITION:** analytics collection or backend writes for the event get built here — discard; emission only.

### P2.5 — Add re-shoot from a result
- **TITLE:** Add re-shoot to camera (S6).
- **RESPONSIBILITY:** return to the live camera from a result.
- **SCOPE:** RN — a re-shoot control on `MealResultScreen.jsx` (and/or `CookScreen.jsx`) navigating back to `CameraScreen`.
- **ACCEPTANCE CRITERIA:** from a result, re-shoot returns to the live camera, ready to capture again.
- **VALIDATION STEPS:** run on device; from a result tap re-shoot → camera live → capture again.
- **OUT OF SCOPE:** error-triggered re-shoot (Phase 3 maps error→S6); any new surface.
- **RESET CONDITION:** a new screen is created instead of navigating to the existing camera — discard.

---

## PHASE 3 — HONEST UNDER STRESS  *(partial)*
**Order (emitted):** P3.1 → P3.2 ; P3.3 (after P2.3+P2.5) ; P3.4 (after P1.6).
**Blocked, not emitted** — see the consolidated cascade-back: CB4 (truly-empty fridge), CB5
(correction affordance), CB6 (offline-state *design refinement* — the floor ships in P3.4).

### P3.1 — Add the backend error contract
- **TITLE:** Add `GlobalExceptionHandler` + domain exception + `ErrorResponse`.
- **RESPONSIBILITY:** turn backend failures into structured error responses with stable codes.
- **SCOPE:** `exception/GlobalExceptionHandler.java` (`@ControllerAdvice`), `exception/MealVisionException.java` (EXTENDs `RuntimeException`), `dto/ErrorResponse.java` (carries a code).
- **ACCEPTANCE CRITERIA:** vision failures and unreadable-image cases surface as `ErrorResponse` with stable codes (`VISION_FAILED`, `UNREADABLE_IMAGE`); an advice test asserts the codes; the response **never** contains a manual-entry affordance.
- **VALIDATION STEPS:** `./mvnw test -Dtest=GlobalExceptionHandlerTest`; simulate a thrown domain exception → assert JSON shape + code.
- **OUT OF SCOPE:** the client mapping (P3.2); retries; auth; the happy path (P1.5).
- **RESET CONDITION:** controllers start try/catch-and-returning instead of advice — discard; errors flow through `@ControllerAdvice` only.

### P3.2 — Map error responses to re-shoot on the client
- **TITLE:** Map backend error codes → re-shoot / try-again.
- **RESPONSIBILITY:** translate a backend error code into the recovery path.
- **SCOPE:** RN `src/api/client.js` (error parsing) + an error branch in the capture→result flow routing codes to S6 (and `UNREADABLE_IMAGE` → a "try again" prompt).
- **ACCEPTANCE CRITERIA:** a backend error → user sees retry/re-shoot, **never** a manual ingredient form; `UNREADABLE_IMAGE` → "try again"; other codes → re-shoot.
- **VALIDATION STEPS:** mock the endpoint to return each code; observe the correct recovery; grep the RN tree to confirm no ingredient-edit UI exists.
- **OUT OF SCOPE:** the backend codes (P3.1); offline (P3.4); timeout (P3.3).
- **RESET CONDITION:** any manual-entry/edit affordance appears — discard immediately; this violates the §7 hardest line.

### P3.3 — Add request timeout → re-shoot for latency overrun
- **TITLE:** Add request timeout → recovery.
- **RESPONSIBILITY:** bound the in-flight wait and recover when the budget is blown.
- **SCOPE:** RN `src/api/client.js` (request timeout) + the processing flow (P2.3) advancing to retry/re-shoot on timeout.
- **ACCEPTANCE CRITERIA:** a request exceeding the budget resolves to retry/re-shoot, not an infinite spinner.
- **VALIDATION STEPS:** mock a slow/no response; observe timeout → recovery within a bounded wait.
- **OUT OF SCOPE:** the latency *metric/logging* (Phase 4 / P4.3); backend changes.
- **RESET CONDITION:** the spinner can still hang indefinitely — discard, restart the timeout wiring.

### P3.4 — Add offline detection + connection-required floor state
- **TITLE:** Add offline short-circuit + floor state.
- **RESPONSIBILITY:** when offline, don't attempt the upload; inform; offer retry.
- **SCOPE:** RN — a connectivity check before upload in the capture flow + a minimal `OfflineState` (message + retry).
- **ACCEPTANCE CRITERIA:** offline → no upload attempt → a clear connection-required message + a retry that proceeds once back online.
- **VALIDATION STEPS:** airplane mode on; capture; observe no network attempt + the message; restore; retry succeeds.
- **OUT OF SCOPE:** the state's **design refinement** (copy, visual, richer behaviour) is **CB6-blocked — floor only**; backend untouched.
- **RESET CONDITION:** it attempts the upload offline, or shows a generic *error* instead of an *informational* state — discard; offline is not an error.

---

## PHASE 4 — MEASUREMENT  *(partial)*
**Order (emitted):** P4.1 → then P4.2, P4.3, P4.4 (P4.4 needs P2.4's emission points).
**Blocked, not emitted:** CB3 (order-out deferral — undefined); CB7 (repeat-capture metric —
client-vs-server unruled). CB7 server-side additionally spawns a persistence sub-phase
(`capture_event` entity + `JpaRepository` + Flyway migration + the F1 device token) that cannot
be written until the ruling — see cascade-back.

### P4.1 — Add the analytics client
- **TITLE:** Add the client analytics sink.
- **RESPONSIBILITY:** a working analytics pipe with one verifiable event.
- **SCOPE:** RN `src/analytics/` (analytics SDK config or a thin event-POST client) + one test event.
- **ACCEPTANCE CRITERIA:** a single emitted event reaches the sink and is observable.
- **VALIDATION STEPS:** run on device; fire the test event; confirm it lands (dashboard/inspector).
- **OUT OF SCOPE:** the specific §5 signals (P4.2–P4.4); any backend/persistence (that is CB7 territory).
- **RESET CONDITION:** the pipe can't deliver one event without backend changes — discard, restart; client-side sink only.

### P4.2 — Emit the app-open time-of-day signal
- **TITLE:** Emit app-open timestamp.
- **RESPONSIBILITY:** record when the app is opened (the "decision moment" bet).
- **SCOPE:** RN — an app-open event emitted through `src/analytics/`.
- **ACCEPTANCE CRITERIA:** each cold-open emits a timestamped event; time-of-day clustering is derivable.
- **VALIDATION STEPS:** cold-open the app a few times; confirm timestamped events in the sink.
- **OUT OF SCOPE:** latency (P4.3); cook signals (P4.4); deferral (CB3).
- **RESET CONDITION:** it writes to the backend or fabricates identity — discard; client-side, identity-free.

### P4.3 — Emit the suggestion-latency signal
- **TITLE:** Emit open→suggestion latency + overrun flag.
- **RESPONSIBILITY:** record time from capture to first suggestion, flagging budget overruns.
- **SCOPE:** RN — timing around the capture→result flow, emitted through `src/analytics/`.
- **ACCEPTANCE CRITERIA:** each suggestion emits a duration; durations over the ~10s budget carry an overrun flag (§5).
- **VALIDATION STEPS:** complete several captures (fast + a forced-slow one); confirm durations + an overrun flag on the slow one.
- **OUT OF SCOPE:** timeout *behaviour* (P3.3); app-open (P4.2); cook signals (P4.4).
- **RESET CONDITION:** the metric duplicates or contradicts the P3.3 timeout logic — discard, restart; measurement, not control.

### P4.4 — Route the cook-it / I-cooked-it confirmation to the sink
- **TITLE:** Collect the cook-confirmation signals.
- **RESPONSIBILITY:** deliver P2.4's cook-it / I-cooked-it events to analytics.
- **SCOPE:** RN — wire the events emitted in P2.4 through `src/analytics/`.
- **ACCEPTANCE CRITERIA:** "Cook this" and "I cooked it" produce distinct, countable events in the sink.
- **VALIDATION STEPS:** run the cook path; confirm both events land and are distinguishable.
- **OUT OF SCOPE:** **order-out deferral** (CB3 — undefined); the emission UI (P2.4 owns it); any backend store.
- **RESET CONDITION:** you try to infer deferral here (CB3) or persist server-side (CB7) — discard; collect the two defined signals only.

---

## PHASE 5 — SHIP  *(mostly blocked)*

**Core deliverable blocked — CASCADE-BACK.** "Deploy to a real environment" cannot be
packetized: the deploy *mechanism* is Docker (settled), but no **deploy target** —
registry, host, or orchestrator — is named in the Founding Spec, PRD, Constrained PRD, or
Roadmap. A packet whose acceptance criteria would read "image pushed to *<unnamed>* and running
on *<unnamed>*" is not writable. The Jenkins `deploy` stage stays stubbed (P0.5) until a target is
declared; then it is a single bounded packet ("push to `<registry>`; run compose on `<host>`").

**Payments — NULL (CB2).** Monetisation is absent from the spec; no pricing/paywall/trial packet
can exist. Recorded so it is not silently dropped.

**Below-line polish — optional, emittable.** Neither is v1-required; both are bounded and offered
so the board honestly reflects what *can* be picked up.

### P5.1 *(optional)* — Add auto-capture as a capture option
- **TITLE:** Add optional auto-capture on the camera.
- **RESPONSIBILITY:** offer hands-free capture when the frame is steady (J1.3 "open option").
- **SCOPE:** RN `screens/CameraScreen.jsx` — an auto-capture toggle/heuristic feeding the existing P1.6 capture path.
- **ACCEPTANCE CRITERIA:** with auto-capture on, a steady fridge frame triggers capture without a tap; manual shutter still works.
- **VALIDATION STEPS:** run on device; enable auto-capture; hold steady → capture fires; disable → manual still works.
- **OUT OF SCOPE:** the upload path (P1.6 owns it); nav/permissions; anything backend.
- **RESET CONDITION:** auto-capture fires erratically or breaks the manual path — discard; manual capture is the floor and must remain intact.

### P5.2 *(optional)* — Refine the processing progress display
- **TITLE:** Replace the indeterminate spinner with staged progress.
- **RESPONSIBILITY:** make the S2 wait feel honest (perceived-latency polish).
- **SCOPE:** RN `screens/ProcessingScreen.jsx` only.
- **ACCEPTANCE CRITERIA:** the processing state shows staged/progressive feedback rather than a bare spinner; still resolves to result or P3.3 recovery.
- **VALIDATION STEPS:** run on device; capture; observe staged progress; confirm timeout recovery (P3.3) still fires.
- **OUT OF SCOPE:** the timeout logic (P3.3); the request itself; backend.
- **RESET CONDITION:** the refinement swallows the timeout/recovery path — discard; polish must not regress P3.3.

---

## CONSOLIDATED CASCADE-BACK — rulings/gaps gating the board

| Ref | What it blocks | What lands when ruled |
|---|---|---|
| **F1** — auth / rate-limiting | *unpacketized* (P1.5 ships open) | (a) a rate-limit filter packet; (b) a device-token packet *(also seeds CB7)*; (c) nothing |
| **F2** — sync vs streaming | *shapes* P1.1, P1.2, P1.5, P1.6 | sync default already encoded; streaming → re-shape those four (port→stream type, controller→SSE, RN→stream consume) |
| **CB1** — allergy filters | *re-weights Phase 1* if v1 | exclusion step in the adapter/prompt (P1.2) + possibly a filter surface (Phase 2) — **unplaceable until ruled** |
| **CB3** — deferral metric | part of Phase 4 (P4.4 scope) | a deferral-inference packet, once operationally defined |
| **CB4** — truly-empty fridge | the degenerate branch of J2 | a "nothing-makeable" state packet (closest-real vs. admit-nothing) |
| **CB5** — correction affordance | a Phase 3 packet | *forbidden* → none (re-shoot-only, already built P2.5); *permitted* → a tap-to-remove packet |
| **CB6** — offline design | the refinement above P3.4's floor | a state-design packet (copy/visual/behaviour) |
| **CB7** — repeat-capture metric | part of Phase 4 + a persistence sub-phase | *client* → one analytics packet; *server* → `capture_event` entity + repo + `V1__capture_event.sql` migration + device token (a 3–4 packet sub-phase) |
| **CB8** — photo retention | nothing (ephemeral default builds nothing) | *retention* → object-store/blob packets (backend + infra) |
| **CB2** — monetisation | Phase 5 payments | null until a source line exists |
| **Deploy target** *(new)* | Phase 5 deploy | one bounded packet once a registry/host/orchestrator is named |

**Coverage:** every binding-table item that is *bindable now* maps to exactly one emitted packet
(P0.1–P5.2 above); every item that is *not* yet bindable is named to its ruling in the table above,
not hidden behind a vague card. Core-loop persistence correctly produces **no packet** (it does not
exist) unless CB7-server or CB8 materialises it.

---

*Footer: `{{DRIFT_AND_CASCADE_FOOTER}}`
