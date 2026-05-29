# **Constrained PRD — “Fridge → Meal” (bound to boilerplate)**

***

## 1. BINDING TABLE

Each requirement is forced onto the **React Native + Spring Boot + SQL + AI SDK** stack.

> Legend:  
> **REUSE** = directly maps to an existing convention  
> **EXTEND** = modify an existing pattern  
> **NEW** = new artifact (justified)

***

### Core Loop + User Journey

| Requirement                      | Binding    | Implementation Anchor                                                                   |
| -------------------------------- | ---------- | --------------------------------------------------------------------------------------- |
| Open app → camera live (G4, S1)  | **NEW**    | `frontend/screens/CameraScreen.tsx` — initial route; justified: no camera screen exists |
| Capture fridge photo             | **REUSE**  | React Native camera library pattern (`react-native-camera` / expo-camera)               |
| Upload photo to backend          | **EXTEND** | `POST /api/v1/meal/from-image` in Spring Controller; extends existing REST pattern      |
| ≤10s processing (G3, S2)         | **EXTEND** | Existing async service layer → add timeout constraint + progress response DTO           |
| AI ingredient detection          | **REUSE**  | Existing *AI service SDK (Java Object Adapter)*                                         |
| Meal generation from ingredients | **NEW**    | `MealGenerationService.java`; justification: no existing domain logic                   |
| Return one meal (G2, S3)         | **EXTEND** | JSON response DTO pattern (`MealResponse`)                                              |
| “Cook this” action               | **NEW**    | `POST /api/v1/session/{id}/cook` — requires state tracking                              |
| “I cooked it” confirmation       | **NEW**    | `POST /api/v1/session/{id}/complete` — new interaction signal                           |
| Re-shoot flow (S6)               | **REUSE**  | Navigation stack reset in React Native                                                  |

***

### Backend Structure

| Requirement                 | Binding    | Implementation Anchor                                              |
| --------------------------- | ---------- | ------------------------------------------------------------------ |
| REST API pattern            | **REUSE**  | `@RestController`, `/api/v1/*` convention                          |
| Service layer orchestration | **REUSE**  | `@Service` classes (existing Spring Boot pattern)                  |
| Error handling              | **EXTEND** | Add `VisionProcessingException`, handled via `@ControllerAdvice`   |
| Async processing            | **EXTEND** | Use `@Async` or CompletableFuture; extend existing service pattern |
| Time-bound processing       | **NEW**    | Timeout wrapper (e.g., `ProcessingDeadlineService`)                |

***

### Data + Persistence

| Requirement                      | Binding    | Implementation Anchor       |
| -------------------------------- | ---------- | --------------------------- |
| Session tracking (G6, analytics) | **NEW**    | `meal_session` table        |
| Store detected ingredients       | **NEW**    | `detected_ingredient` table |
| Store generated meal             | **NEW**    | `generated_meal` table      |
| Track “cook” + “completed”       | **EXTEND** | Columns on `meal_session`   |

***

### Analytics / Success Metrics

| Requirement            | Binding                                                  |
| ---------------------- | -------------------------------------------------------- |
| Time-to-result ≤10s    | **EXTEND** logging (`request_start`, `response_time_ms`) |
| Cook rate tracking     | **NEW** persisted field                                  |
| Repeat usage           | **REUSE** session timestamps                             |
| Time-of-day clustering | **REUSE** timestamp column                               |

***

### Non-Goals Enforcement

| Non-goal           | Binding                                              |
| ------------------ | ---------------------------------------------------- |
| No accounts        | **REUSE**: absence of auth module                    |
| No saved recipes   | **REUSE**: no persistence beyond session-linked data |
| No social features | **REUSE**: no endpoints added                        |
| No grocery logic   | **REUSE**: exclude from service layer                |

***

## 2. FRICTION LOG

This is where reality pushes back.

***

### F1 — **Camera-first launch vs React Native app lifecycle**

* **Requirement:** Camera live immediately on open
* **Friction:** RN apps typically boot to a navigation container; camera permission must be resolved first
* **Cheapest honest options:**
  * Change requirement: allow **first-run permission screen before camera** *(already partially accepted)*
  * Accept cost: preload camera + optimistic render
* ✅ Recommended: **Keep requirement, accept permission-first exception**

***

### F2 — **≤10s processing vs synchronous Spring Boot request**

* **Requirement:** hard latency budget
* **Friction:** AI inference + meal generation may exceed HTTP timeout
* **Options:**
  * Change boilerplate: introduce async job queue (RabbitMQ, Kafka)
  * Change requirement: allow delayed response
  * Accept cost: aggressive timeout + partial results
* 🚨 Decision: **FRICTION — needs distributed async (not present)**  
  → cascade risk to architecture

***

### F3 — **“Always return a meal” vs real ingredient constraints**

* **Requirement:** no “no result” state
* **Friction:** algorithm cannot guarantee cookable meal
* **Options:**
  * Change requirement (allow fallback)
  * Accept hallucinated recipes (dangerous)
* 🚨 Requires **spec decision (CB4)**  
  → backend cannot safely implement

***

### F4 — **No manual correction vs unreliable AI detection**

* **Requirement:** no ingredient editing
* **Friction:** AI SDK output may be wrong
* **Options:**
  * Change requirement (add lightweight correction)
  * Accept re-shoot loop frustration
* 🚨 High-risk — product may fail without correction  
  → **CB5 remains unresolved**

***

### F5 — **No auth vs persistence**

* **Requirement:** no accounts
* **Friction:** analytics + repeat tracking need identity
* **Options:**
  * Accept anonymous session IDs (cookie/device ID)
  * Add auth (breaks non-goal)
* ✅ Recommended: **Anonymous device/session ID**

***

### F6 — **Offline behaviour undefined**

* **Requirement:** unstated
* **Friction:** AI SDK likely requires network
* **Options:**
  * Add offline fallback (impossible with current stack)
  * Define hard failure state
* ✅ Recommended: **explicit “requires connection” state (CB6)**

***

### F7 — **AI SDK abstraction mismatch**

* **Requirement:** real-time multimodal processing
* **Friction:** SDK may not support:
  * ingredient extraction + recipe generation in one pass
* **Options:**
  * Chain multiple calls (latency risk)
  * Build custom orchestration layer
* ⚠️ Cost accepted → **EXTEND service layer heavily**

***

## 3. DATA MODEL DELTA

Using typical **Spring Boot + JPA (Hibernate)** conventions.

***

### `meal_session`

```sql
CREATE TABLE meal_session (
  id BIGSERIAL PRIMARY KEY,
  created_at TIMESTAMP NOT NULL,
  image_url TEXT NOT NULL,
  processing_time_ms INT,
  cooked BOOLEAN DEFAULT FALSE,
  completed BOOLEAN DEFAULT FALSE
);
```

***

### `detected_ingredient`

```sql
CREATE TABLE detected_ingredient (
  id BIGSERIAL PRIMARY KEY,
  session_id BIGINT REFERENCES meal_session(id),
  name VARCHAR(255),
  confidence FLOAT
);
```

***

### `generated_meal`

```sql
CREATE TABLE generated_meal (
  id BIGSERIAL PRIMARY KEY,
  session_id BIGINT REFERENCES meal_session(id),
  title VARCHAR(255),
  steps TEXT,
  time_minutes INT
);
```

***

### JPA Entities

```java
@Entity
class MealSession {
  @Id @GeneratedValue Long id;
  Instant createdAt;
  String imageUrl;
  Integer processingTimeMs;
  Boolean cooked;
  Boolean completed;
}
```

***

## 4. CONVENTION NOTES (CRITICAL FOR AGENTS)

These are the rails — do not break them downstream.

***

### Backend (Spring Boot)

* **Controllers**
  * Path: `/api/v1/...`
  * Naming: `XController.java`
  * No business logic inside controllers

* **Services**
  * Naming: `XService.java`
  * Contain orchestration logic
  * AI SDK calls MUST live here

* **DTOs**
  * Suffix: `Request`, `Response`
  * Never expose entities directly

* **Error handling**
  * Centralized via `@ControllerAdvice`
  * Never throw raw exceptions to client

* **Async**
  * Use `CompletableFuture` or `@Async`
  * Do NOT block controller threads

***

### Frontend (React Native)

* **Structure**
  ```
  /screens
  /components
  /services (API calls)
  /navigation
  ```

* **Navigation**
  * Stack-based (Camera → Result → Steps)

* **API calls**
  * Centralized in `/services/api.ts`

* **State**
  * Local state (no Redux unless necessary)

***

### Database

* Snake\_case tables
* Foreign keys always explicit
* No soft deletes unless required

***

### CI/CD (Jenkins + Docker)

* Build pipeline:
  * test → build → dockerize → deploy
* JUnit coverage required for:
  * Service layer
  * Controller layer

***

## DRIFT & CASCADE FOOTER

### Drift Signals Identified

* Async processing requirement exceeds current backend simplicity
* AI reliability conflicts with “no manual correction”
* “Always return meal” conflicts with reality constraints

***

### Cascade-Back Required

* **CB4:** Empty fridge contradiction
* **CB5:** Manual correction allowed?
* **CB6:** Offline behavior definition
* **F2 escalation:** Async architecture decision (queue vs synchronous)

***

## Bottom Line

* \~70% of PRD **binds cleanly** to boilerplate (REST + RN + JPA)
* The **core risk is not UI — it’s AI reliability + latency**
* The biggest architectural pressure point:  
  → **synchronous Spring Boot is misaligned with a hard 10s AI loop**

This is the real constraint:

> Without async orchestration + clarified spec assumptions, the “core loop” is architecturally fragile.
