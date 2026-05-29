You did not provide **INPUT B — Boilerplate Manifest**, which is the critical constraint layer this task depends on.  
Per your own rule: *“every requirement must bind to something that actually exists”* — that is currently impossible.

So this output will do two things honestly:

1. **Bind only where generic, near-universal boilerplate patterns exist** (e.g. REST API, standard frontend routing patterns, ORM tables).
2. **Surface the absence of the manifest itself as the primary friction artifact** (because it blocks true grounding).

***

# CONSTRAINED PRD (PARTIAL — BLOCKED BY MISSING MANIFEST)

***

## 1. BINDING TABLE

> Legend:  
> **REUSE** = almost certain to exist in modern boilerplates  
> **EXTEND** = safely additive without structural change  
> **NEW** = unavoidable; justified

***

### CORE LOOP

| Requirement                          | Binding | Target                                                                                              |
| ------------------------------------ | ------- | --------------------------------------------------------------------------------------------------- |
| Camera live on open                  | EXTEND  | `frontend/app/root route (e.g. /)` → auto-init camera stream component                              |
| Capture fridge photo                 | REUSE   | existing file upload / media capture component pattern                                              |
| Upload image to backend              | REUSE   | `/api/upload` or `/api/media` route (common pattern)                                                |
| Process image (vision → ingredients) | NEW     | `/api/vision/analyze` (no generic boilerplate includes multimodal inference) — *core product logic* |
| Generate one meal from ingredients   | NEW     | `/api/meal/generate` — *LLM/logic endpoint*                                                         |
| Return single meal result            | EXTEND  | response schema from API layer                                                                      |
| ≤10s response time                   | EXTEND  | existing request lifecycle + timeout config                                                         |
| “Cook this” action                   | REUSE   | standard POST interaction (e.g. `/api/events`)                                                      |
| “I cooked it” confirmation           | EXTEND  | existing analytics/event tracking pattern                                                           |

***

### SURFACES

| Surface             | Binding | Target                                                  |
| ------------------- | ------- | ------------------------------------------------------- |
| S1 Camera screen    | EXTEND  | existing main page component                            |
| S2 Processing state | REUSE   | loading state pattern (spinners / suspense / skeletons) |
| S3 Meal result      | EXTEND  | result display component                                |
| S4 Steps view       | EXTEND  | detail-view pattern (modal/page)                        |
| S5 Permission flow  | REUSE   | browser/native permission handling                      |
| S6 Re-shoot         | REUSE   | navigation back to root route                           |

***

### SUCCESS CRITERIA / TRACKING

| Requirement                | Binding | Target                              |
| -------------------------- | ------- | ----------------------------------- |
| Time-to-result tracking    | EXTEND  | existing logging/metrics middleware |
| “Cook this” events         | REUSE   | analytics/event table               |
| “I cooked it” confirmation | EXTEND  | analytics event payload             |
| Time-of-day clustering     | EXTEND  | timestamp column in events table    |
| Repeat usage               | EXTEND  | user/session tracking               |

***

### NON-GOALS ENFORCEMENT

| Non-goal           | Binding                                               |
| ------------------ | ----------------------------------------------------- |
| No saved recipes   | REUSE (omit existing “favorites” features if present) |
| No social          | REUSE (disable/remove sharing modules if exist)       |
| No inventory state | NEW CONSTRAINT — avoid writing persistence logic      |
| No planner         | REUSE (do not add scheduling features)                |

***

### DATA / BACKEND

| Requirement             | Binding | Target                                |
| ----------------------- | ------- | ------------------------------------- |
| Store photo temporarily | REUSE   | existing blob storage / uploads table |
| Store session result    | EXTEND  | request log / session table           |
| Ingredient output       | NEW     | requires structured storage           |
| Meal output             | NEW     | requires structured storage           |

***

### NET-NEW ITEMS (JUSTIFIED)

| Item                                   | Why NEW is required                                   |
| -------------------------------------- | ----------------------------------------------------- |
| Vision inference endpoint              | No boilerplate includes fridge → ingredient detection |
| Meal generation endpoint               | Core product logic                                    |
| Ingredient schema                      | Not a standard entity                                 |
| Meal schema                            | Not a standard entity                                 |
| Session-level pipeline (vision → meal) | Cross-service orchestration does not pre-exist        |

***

## 2. FRICTION LOG

This is where the real value is — and it's heavy.

***

### F1 — **MISSING BOILERPLATE MANIFEST (BLOCKER)**

* **Requirement:** Bind to existing files, routes, schema
* **Friction:** No knowledge of:
  * framework (Next.js? Django? Rails?)
  * ORM (Prisma? TypeORM? ActiveRecord?)
  * auth model
  * file structure
* **Impact:** All bindings above are probabilistic, not real
* **Cheapest honest option:**  
  → **STOP and supply manifest**
* **Status:** **HARD BLOCKER**

***

### F2 — CAMERA-FIRST UX vs WEB BOILERPLATES

* **Requirement:** camera auto-live on open
* **Friction:**
  * Most web boilerplates do NOT default to camera permissions
  * Requires HTTPS + permission handling + device constraints
* **Options:**
  * Change requirement → add “Tap to enable camera” (adds friction)
  * Accept cost → custom camera bootstrap logic
* **Reality:** **This is non-trivial on web, trivial on mobile-native**

***

### F3 — ≤10s GUARANTEE vs MULTI-STAGE PIPELINE

* **Requirement:** vision → ingredients → meal ≤10s
* **Friction:**
  * Two async processes (vision + generation)
  * Typical API chains exceed latency budget
* **Options:**
  * Change requirement → allow progressive response
  * Change architecture → parallelize / cache
  * Accept cost → expensive infra
* **Drift signal:** STRONG

***

### F4 — “ALWAYS RETURN A MEAL” vs REAL DATA

* **Requirement:** no failure state
* **Friction:**
  * Real ingredient detection may be empty/incorrect
* **Options:**
  * Change requirement → allow fallback meal
  * Accept hallucinated meal risk
* **This is a spec contradiction (already flagged CB4)**

***

### F5 — NO MANUAL CORRECTION vs MISDETECTION

* **Requirement:** no input UX
* **Friction:**
  * Vision errors inevitable
* **Options:**
  * Change requirement → allow minimal correction UI
  * Accept poor outputs
* **This is CB5 — unresolved by design**

***

### F6 — STATELESS PRODUCT vs ANALYTICS NEEDS

* **Requirement:** no inventory/history
* **Friction:**
  * Success metrics require stored sessions/events
* **Resolution:**  
  → store metadata but not “inventory”
* **Cost:** conceptual purity compromised slightly

***

### F7 — CAMERA PERMISSION FAILURE = PRODUCT FAILURE

* **Requirement:** entire app depends on camera
* **Friction:**
  * Boilerplates don’t enforce permission-recovery UX
* **Options:**
  * Extend frontend to handle fail states
* **Cost:** medium

***

### F8 — UNKNOWN AUTH MODEL

* **Requirement:** track repeat usage
* **Friction:**
  * If no auth → need anonymous session system
* **Options:**
  * Extend session layer
* **Blocked by missing manifest**

***

## 3. DATA MODEL DELTA (GENERIC ORM SHAPE)

Since ORM is unknown, this is expressed in a **Prisma-like neutral form**.

***

### `Session`

Tracks one fridge → meal loop

```
Session {
  id
  userId (nullable)
  createdAt
  imageUrl
  latencyMs
}
```

***

### `IngredientDetection`

```
IngredientDetection {
  id
  sessionId (FK)
  name
  confidence
}
```

***

### `MealResult`

```
MealResult {
  id
  sessionId (FK)
  title
  steps (JSON)
  timeEstimateMinutes
}
```

***

### `Event`

(for success metrics)

```
Event {
  id
  sessionId (FK)
  type ("cook_tap" | "cooked_confirmed")
  createdAt
}
```

***

### NOTES

* **No inventory table** (explicit non-goal)
* **No recipe DB** (meals are generated per session)
* **Loose coupling via sessionId**

***

## 4. CONVENTION NOTES (ASSUMED BASELINES)

These are inferred standard patterns — must be replaced once manifest exists.

***

### API DESIGN

* RESTful routes:
  * `/api/vision/analyze`
  * `/api/meal/generate`
* JSON responses only
* Stateless endpoints

***

### FRONTEND

* Single entry route (`/`)
* Component structure:
  * `CameraView`
  * `ProcessingState`
  * `MealResultView`
* State machine style flow (camera → loading → result)

***

### ERROR HANDLING

* Never expose raw errors to UI
* Always fallback:
  * retry
  * re-shoot

***

### STATE MANAGEMENT

* Session scoped (no persistence)
* Minimal global state

***

### STORAGE

* Images stored via existing upload provider
* TTL or cleanup recommended

***

### ANALYTICS

* Event-based tracking
* No behavioral personalization (non-goal)

***

# FINAL READ

### What is actually grounded?

* The **shape of the system**
* The **minimum new components required**
* The **pressure points where the spec collides with reality**

### What is NOT grounded (critical)?

* Exact files
* Exact routes
* Exact ORM syntax
* Auth integration
* Storage provider

***

# REQUIRED NEXT STEP (NON-OPTIONAL)

Provide:

```
{{BOILERPLATE_MANIFEST}}
```

Without it, any deeper binding would violate your own rule:

> *“Every requirement must bind to something that actually exists.”*

***

If you send it, I’ll convert this into a **fully concrete Constrained PRD with exact file paths, routes, schema, and zero ambiguity.**
