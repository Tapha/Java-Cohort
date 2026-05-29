Below are **Agent Task Packets** derived mechanically from the roadmap + constrained PRD. Each packet is single-responsibility, tightly scoped, independently verifiable, and ordered by dependency.

***

# PHASE 0 — SPINE

### Packet 0.1

**TITLE** — Create Spring Boot app with `/health` endpoint  
**RESPONSIBILITY** — Establish a bootable backend skeleton  
**SCOPE** — `src/main/java/**`, `build.gradle` or `pom.xml`  
**ACCEPTANCE CRITERIA** — App starts locally; `GET /health` returns 200 OK  
**VALIDATION STEPS** —

* `./mvnw spring-boot:run` (or Gradle equivalent)
* `curl http://localhost:8080/health` → `200 OK`  
  **OUT OF SCOPE** — Controllers beyond `/health`, AI logic, DB  
  **RESET CONDITION** — App fails to start or `/health` not reachable

***

### Packet 0.2

**TITLE** — Add React Native blank app shell  
**RESPONSIBILITY** — Create installable RN app that boots  
**SCOPE** — RN project root (`App.tsx`, config files)  
**ACCEPTANCE CRITERIA** — App installs and shows blank screen  
**VALIDATION STEPS** —

* `npx react-native run-ios|android`
* Confirm blank app launches  
  **OUT OF SCOPE** — Navigation, camera, API calls  
  **RESET CONDITION** — App fails to run on device/emulator

***

### Packet 0.3

**TITLE** — Add JUnit test harness with one passing test  
**RESPONSIBILITY** — Establish test convention baseline  
**SCOPE** — `src/test/java/**`  
**ACCEPTANCE CRITERIA** — One test passes in CI runner  
**VALIDATION STEPS** —

* `./mvnw test` → all green  
  **OUT OF SCOPE** — Business logic tests  
  **RESET CONDITION** — Tests fail or not detected

***

### Packet 0.4

**TITLE** — Add multi-stage Dockerfile  
**RESPONSIBILITY** — Containerise backend build + runtime  
**SCOPE** — `Dockerfile`  
**ACCEPTANCE CRITERIA** — Builds and runs container  
**VALIDATION STEPS** —

* `docker build -t app .`
* `docker run -p 8080:8080 app`  
  **OUT OF SCOPE** — Compose, DB wiring  
  **RESET CONDITION** — Image fails to build or run

***

### Packet 0.5

**TITLE** — Add docker-compose with app + SQL service  
**RESPONSIBILITY** — Define local multi-service runtime  
**SCOPE** — `docker-compose.yml`  
**ACCEPTANCE CRITERIA** — Both services start successfully  
**VALIDATION STEPS** —

* `docker compose up`
* `/health` reachable  
  **OUT OF SCOPE** — DB usage  
  **RESET CONDITION** — Any service fails to start

***

### Packet 0.6

**TITLE** — Add Jenkinsfile for build→test→docker  
**RESPONSIBILITY** — CI pipeline definition  
**SCOPE** — `Jenkinsfile`  
**ACCEPTANCE CRITERIA** — Pipeline runs build + tests + docker build  
**VALIDATION STEPS** —

* Run Jenkins job
* All stages green  
  **OUT OF SCOPE** — Deploy stage  
  **RESET CONDITION** — Pipeline red in any stage

***

# PHASE 1 — THE LOOP RUNS ONCE

### Packet 1.1

**TITLE** — Define `MealVisionPort` interface  
**RESPONSIBILITY** — Create domain abstraction for AI  
**SCOPE** — `domain/MealVisionPort.java`  
**ACCEPTANCE CRITERIA** — Interface compiles, method defined  
**VALIDATION STEPS** — `./mvnw compile`  
**OUT OF SCOPE** — Implementation  
**RESET CONDITION** — Compilation failure

***

### Packet 1.2

**TITLE** — Implement `<Vendor>MealVisionAdapter`  
**RESPONSIBILITY** — Wrap SDK with object adapter  
**SCOPE** — `infrastructure/adapter/*MealVisionAdapter.java`  
**ACCEPTANCE CRITERIA** — Adapter implements port and returns `Meal`  
**VALIDATION STEPS** — Unit compile + mock call works  
**OUT OF SCOPE** — Controller/service usage  
**RESET CONDITION** — Adapter requires changes outside scope

***

### Packet 1.3

**TITLE** — Implement `MealService` orchestration  
**RESPONSIBILITY** — Call port and return domain result  
**SCOPE** — `service/MealService.java`  
**ACCEPTANCE CRITERIA** — Service calls port and returns data  
**VALIDATION STEPS** — Unit test with Mockito passes  
**OUT OF SCOPE** — HTTP layer  
**RESET CONDITION** — Requires SDK direct usage

***

### Packet 1.4

**TITLE** — Add `MealController` multipart endpoint  
**RESPONSIBILITY** — Expose POST endpoint for image  
**SCOPE** — `controller/MealController.java`, DTOs  
**ACCEPTANCE CRITERIA** — Accepts file and returns JSON response  
**VALIDATION STEPS** —

* `curl -F "file=@test.jpg" localhost:8080/meal`  
  **OUT OF SCOPE** — AI correctness  
  **RESET CONDITION** — Endpoint not reachable or invalid response

***

### Packet 1.5

**TITLE** — Add config for AI + multipart  
**RESPONSIBILITY** — Externalise configuration  
**SCOPE** — `application.yml`, `config/*`  
**ACCEPTANCE CRITERIA** — Values load via `@ConfigurationProperties`  
**VALIDATION STEPS** — App starts with env vars  
**OUT OF SCOPE** — Business logic  
**RESET CONDITION** — Config not injectable

***

### Packet 1.6

**TITLE** — Add RN capture + upload flow  
**RESPONSIBILITY** — Capture image and POST to backend  
**SCOPE** — `CameraScreen`, `api/axiosClient`  
**ACCEPTANCE CRITERIA** — Image upload succeeds  
**VALIDATION STEPS** —

* Capture photo
* Observe successful POST  
  **OUT OF SCOPE** — Navigation, UI polish  
  **RESET CONDITION** — Upload fails or crashes

***

### Packet 1.7

**TITLE** — Implement `MealResultScreen`  
**RESPONSIBILITY** — Render meal response  
**SCOPE** — `MealResultScreen.tsx`  
**ACCEPTANCE CRITERIA** — Displays title, ingredients, steps, time  
**VALIDATION STEPS** — Manual run with sample data  
**OUT OF SCOPE** — Navigation flow  
**RESET CONDITION** — Requires backend changes

***

### Packet 1.8

**TITLE** — Add backend tests (service + controller + latency)  
**RESPONSIBILITY** — Ensure correctness and timing  
**SCOPE** — `src/test/java/**`  
**ACCEPTANCE CRITERIA** — Tests pass; latency assertion exists  
**VALIDATION STEPS** — `./mvnw test`  
**OUT OF SCOPE** — RN tests  
**RESET CONDITION** — Flaky or failing tests

***

# PHASE 2 — THE LOOP AS A PRODUCT

### Packet 2.1

**TITLE** — Set `CameraScreen` as initial route  
**RESPONSIBILITY** — Enforce camera-on-open  
**SCOPE** — Navigation config  
**ACCEPTANCE CRITERIA** — App opens directly to camera  
**VALIDATION STEPS** — Launch app  
**OUT OF SCOPE** — Capture logic  
**RESET CONDITION** — Any intermediate screen appears

***

### Packet 2.2

**TITLE** — Add permission primer + OS prompt  
**RESPONSIBILITY** — Request camera permission flow  
**SCOPE** — `PermissionScreen`, camera permission calls  
**ACCEPTANCE CRITERIA** — Primer shows; OS prompt triggered  
**VALIDATION STEPS** — Fresh install run  
**OUT OF SCOPE** — Error handling  
**RESET CONDITION** — Permission not granted or stuck

***

### Packet 2.3

**TITLE** — Implement `ProcessingScreen`  
**RESPONSIBILITY** — Show waiting state  
**SCOPE** — `ProcessingScreen.tsx`  
**ACCEPTANCE CRITERIA** — Visible during API call  
**VALIDATION STEPS** — Capture → see loading  
**OUT OF SCOPE** — Backend timing  
**RESET CONDITION** — Freeze or blank screen

***

### Packet 2.4

**TITLE** — Add `CookScreen` + cook confirmation  
**RESPONSIBILITY** — Display steps and emit event  
**SCOPE** — `CookScreen.tsx`  
**ACCEPTANCE CRITERIA** — Button emits event  
**VALIDATION STEPS** — Tap “I cooked it” → log event  
**OUT OF SCOPE** — Analytics storage  
**RESET CONDITION** — Event not triggered

***

### Packet 2.5

**TITLE** — Implement re-shoot navigation  
**RESPONSIBILITY** — Return to camera  
**SCOPE** — Navigation stack  
**ACCEPTANCE CRITERIA** — Re-shoot returns to camera  
**VALIDATION STEPS** — Tap re-shoot → camera opens  
**OUT OF SCOPE** — Capture logic  
**RESET CONDITION** — Navigation breaks

***

# PHASE 3 — HONEST UNDER STRESS

### Packet 3.1

**TITLE** — Add GlobalExceptionHandler with error codes  
**RESPONSIBILITY** — Map exceptions to structured responses  
**SCOPE** — `exception/*`, controller advice  
**ACCEPTANCE CRITERIA** — Errors return code + message  
**VALIDATION STEPS** — Force exception → inspect response  
**OUT OF SCOPE** — UI mapping  
**RESET CONDITION** — Unhandled exceptions leak

***

### Packet 3.2

**TITLE** — Map error codes to re-shoot flow in client  
**RESPONSIBILITY** — Trigger UI fallback  
**SCOPE** — RN API handler  
**ACCEPTANCE CRITERIA** — Errors navigate to re-shoot  
**VALIDATION STEPS** — Simulate error  
**OUT OF SCOPE** — Backend changes  
**RESET CONDITION** — User stuck on error

***

### Packet 3.3

**TITLE** — Implement degenerate fridge response policy  
**RESPONSIBILITY** — Ensure always-something output  
**SCOPE** — `MealService`  
**ACCEPTANCE CRITERIA** — Returns minimal viable meal  
**VALIDATION STEPS** — Use sparse image input  
**OUT OF SCOPE** — Empty-fridge CB4  
**RESET CONDITION** — Returns null/no result

***

### Packet 3.4

**TITLE** — Add bad-photo detection threshold  
**RESPONSIBILITY** — Reject unreadable images  
**SCOPE** — adapter/service  
**ACCEPTANCE CRITERIA** — Only extreme cases prompt retry  
**VALIDATION STEPS** — Test blurry/photo edge cases  
**OUT OF SCOPE** — UI  
**RESET CONDITION** — Over-triggering retries

***

### Packet 3.5

**TITLE** — Add offline detection state  
**RESPONSIBILITY** — Detect no connectivity  
**SCOPE** — RN network layer  
**ACCEPTANCE CRITERIA** — Offline shows defined state  
**VALIDATION STEPS** — Disable network  
**OUT OF SCOPE** — UX design CB6  
**RESET CONDITION** — Silent failure

***

### Packet 3.6

**TITLE** — Add latency timeout behavior  
**RESPONSIBILITY** — Abort long requests  
**SCOPE** — RN API client  
**ACCEPTANCE CRITERIA** — Timeout triggers re-shoot  
**VALIDATION STEPS** — Simulate slow response  
**OUT OF SCOPE** — Metrics  
**RESET CONDITION** — Hanging request

***

# PHASE 4 — MEASUREMENT

### Packet 4.1

**TITLE** — Add latency tracking instrumentation  
**RESPONSIBILITY** — Record request duration  
**SCOPE** — backend logging or client timer  
**ACCEPTANCE CRITERIA** — Latency logged per request  
**VALIDATION STEPS** — Inspect logs  
**OUT OF SCOPE** — dashboards  
**RESET CONDITION** — Missing data

***

### Packet 4.2

**TITLE** — Track app-open timestamps  
**RESPONSIBILITY** — Capture usage time-of-day  
**SCOPE** — RN analytics module  
**ACCEPTANCE CRITERIA** — Event recorded on open  
**VALIDATION STEPS** — Launch app → event logged  
**OUT OF SCOPE** — backend storage  
**RESET CONDITION** — No event fires

***

### Packet 4.3

**TITLE** — Record cook-it confirmation events  
**RESPONSIBILITY** — Capture completion signal  
**SCOPE** — RN analytics module  
**ACCEPTANCE CRITERIA** — Event fires on tap  
**VALIDATION STEPS** — Tap → log visible  
**OUT OF SCOPE** — deferral metric CB3  
**RESET CONDITION** — Event missing

***

# PHASE 5 — SHIP

### Packet 5.1

**TITLE** — Activate Jenkins deploy stage  
**RESPONSIBILITY** — Enable deployment step  
**SCOPE** — `Jenkinsfile`  
**ACCEPTANCE CRITERIA** — Successful deploy to environment  
**VALIDATION STEPS** — Run pipeline end-to-end  
**OUT OF SCOPE** — infra provisioning  
**RESET CONDITION** — Deploy fails

***

### Packet 5.2

**TITLE** — Refine processing screen progress  
**RESPONSIBILITY** — Improve perceived latency UX  
**SCOPE** — `ProcessingScreen.tsx`  
**ACCEPTANCE CRITERIA** — Visible progress feedback  
**VALIDATION STEPS** — Manual interaction  
**OUT OF SCOPE** — backend timing  
**RESET CONDITION** — No UX improvement

***

### Packet 5.3

**TITLE** — Add optional auto-capture experiment  
**RESPONSIBILITY** — Implement auto-trigger capture  
**SCOPE** — `CameraScreen.tsx`  
**ACCEPTANCE CRITERIA** — Auto-capture works under condition  
**VALIDATION STEPS** — Manual test  
**OUT OF SCOPE** — core capture flow  
**RESET CONDITION** — Breaks manual capture

***

✅ All packets are single-purpose, bounded, and independently executable with clear reset signals.
