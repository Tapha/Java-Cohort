# PRODUCT REQUIREMENTS DOCUMENT — **Fridge → Meal (v1)**

***

## 1. GOALS / NON-GOALS

### Goals (sharpened from Founding Spec)

1. **Win the decision moment (≤30 seconds)**  
   The product must convert a hungry, undecided user standing at the fridge into someone who decides to cook instead of ordering out.  
   → Success depends on speed, clarity, and decisiveness.

2. **Deliver one actionable meal immediately from a single photo**  
   The system must take a casual fridge photo and return a *single*, cookable meal with a time estimate — no intermediate steps.

3. **Maintain a sub-10-second “open → answer” loop**  
   From app launch to viable meal suggestion must stay under \~10 seconds.

4. **Always return a viable meal**  
   The system must not produce “no results.” It must construct the best possible meal from whatever is detected.

5. **Drive repeat usage at real meal times**  
   The product should be used at hunger moments (e.g., lunch/dinner windows), demonstrating behavior change vs novelty.

6. **Enable “cook-it” completion signal**  
   Users must be able to confirm they cooked the suggestion, enabling measurement of real-world outcomes.

***

### Non-Goals (strict, v1)

* No browsing or searching recipes
* No multi-option suggestion lists (pending validation — currently committed to single output)
* No meal planning or future scheduling
* No ingredient inventory tracking or persistence
* No pantry/cupboard system (fridge-only interaction)
* No grocery or shopping features
* No social, sharing, or feeds
* No AI-generated food imagery
* No onboarding flows that require setup (e.g., preferences, diets) before first use

***

## 2. USER JOURNEYS

### 2.1 Happy Path — Core Loop (Primary Journey)

**Trigger:** User is hungry, fridge open, considering ordering food

1. User opens app
2. Camera is immediately live
3. User points camera at fridge and taps capture
4. Image is processed automatically (no user input required)
5. System extracts ingredients from image
6. System generates *one* meal suggestion
7. Result screen displays:
   * Meal name
   * Short description
   * Estimated cook time
8. User taps “Cook this”
9. Optional confirmation: “Did you cook this?” after interaction

**Outcome:** User decides to cook instead of ordering out

***

### 2.2 Edge Journey — Low Ingredient / Sparse Fridge

1. User opens app and captures fridge
2. Image yields minimal ingredients
3. System still returns a meal using available ingredients (e.g., basic combination)

**Outcome requirement:** No “failure” state; meal must still be returned

***

### 2.3 Edge Journey — Imperfect Photo (blurry, incomplete)

1. User captures a poor image
2. Detection partially succeeds
3. System generates a best-effort ingredient set
4. System returns a meal suggestion regardless

**Outcome requirement:** No request for retake before delivering value

***

### 2.4 Edge Journey — User Rejects Suggestion

1. User views suggested meal
2. User decides not to cook

**Outcome:** Session ends without “cook-it”  
**Constraint:** No alternative suggestions shown (single-answer commitment)

***

### 2.5 First-Time User Journey

1. User opens app for first time
2. Immediately sees camera (no setup)
3. OS permission prompt for camera appears
4. User accepts → continues into core loop

**Constraint:** No onboarding friction before value is delivered

***

## 3. SCREENS / SURFACES

1. **Camera Screen (Default + First Screen)**  
   Live camera interface for capturing fridge image immediately on app open.

2. **Processing State (Overlay/Transition)**  
   Transitional state indicating image is being processed.

3. **Meal Result Screen**  
   Displays:
   * Single meal suggestion
   * Description
   * Time estimate
   * “Cook this” action

4. **Cook Confirmation Prompt (Lightweight State)**  
   Simple confirmation mechanism after selection (“Did you cook this?”)

5. **Permission Prompt (OS-mediated)**  
   Camera access request

***

## 4. STATES & EDGE CASES

### Core States

* **Idle (Camera Live):** Default entry state
* **Capturing:** Photo taken
* **Processing:** System generating ingredients + meal
* **Result Ready:** Meal displayed
* **Cook Confirmed:** User completes action

***

### Edge Cases

#### 4.1 First Run

* Camera permission required before use
* No pre-permission onboarding

#### 4.2 Loading / Latency

* Must remain under \~10 seconds total
* If delayed, show clear processing indicator

#### 4.3 Detection Failure (Critical)

* Partial detection must still produce a meal
* Never expose failure to user as “no ingredients found”

#### 4.4 Offline

* Photo capture allowed
* Meal generation cannot proceed
* Must clearly communicate inability to produce result

#### 4.5 Permission Denied

* Cannot proceed without camera
* Must block with clear explanation (no alternate flows)

#### 4.6 Poor Image Quality

* System proceeds with best-effort interpretation
* No forced retake before output

#### 4.7 Empty/Minimal Fridge

* Must still output a meal

***

## 5. MVP BOUNDARY

### ✅ Included in v1 (Must Ship)

* Instant camera-on app open
* Single photo capture flow
* Ingredient detection from image
* Single meal generation (no list)
* Meal display with time estimate
* “Cook this” interaction
* Cook confirmation signal
* Sub-10 second loop performance target
* Always-return-a-meal logic
* Basic handling for:
  * first run
  * offline
  * permission denial
  * low-quality images

***

### ❌ Explicitly Deferred (Named)

* Multiple meal suggestions or browsing
* Recipe exploration or database
* Step-by-step cooking instructions
* Pantry/cupboard support
* Ingredient editing or correction
* Dietary filters / allergy settings
* Saved meals or history
* AI-generated meal imagery
* Social features or sharing
* Grocery recommendations or shopping lists
* Inventory tracking over time
* Expiry tracking
* Monetization systems

***

## 6. REQUIREMENT TRACE

| Requirement                             | Founding Spec Source                                                                |
| --------------------------------------- | ----------------------------------------------------------------------------------- |
| Camera opens immediately on app launch  | “Open app → camera is already live” (Core Loop)                                     |
| Single photo triggers entire experience | “photograph the fridge → get one meal” (Core Loop)                                  |
| One meal output (not list)              | “one meal you can cook right now” (Product Truth); “\[INFERRED] ‘One meal for now’” |
| No intermediate questions before result | “If the loop… asks a question before answering, the product is dead”                |
| Time estimate included                  | “get one meal… with a time estimate”                                                |
| Sub-10 second loop                      | “Time from app-open to first viable suggestion stays under \~10 seconds”            |
| Always return a meal                    | “\[STATED] The app always returns something to cook”                                |
| No “no result” state                    | “A ‘no meal found’ result is a failure state”                                       |
| No inventory persistence                | “Not an inventory/pantry manager”                                                   |
| No browsing or recipe database          | “Not a recipe database or browser”                                                  |
| No planning features                    | “Not a meal planner”                                                                |
| No social features                      | “Not a social network”                                                              |
| No shopping/grocery features            | “Not a grocery/shopping app”                                                        |
| No onboarding friction                  | Derived from “zero-friction loop” + decision-moment urgency                         |
| Must work with imperfect images         | “\[INFERRED] casual, badly-lit fridge photo yields… ingredients”                    |
| Must support decision moment usage      | “The app exists to win that thirty-second standoff”                                 |
| Measure “cook-it” completion            | “track the cook-it tap”                                                             |
| Encourage repeat usage                  | “Repeat photographing across weeks”                                                 |
| Fridge-only interaction                 | Open question states ambiguity; MVP constrained by loop clarity                     |
| No AI food images                       | Open question suggests risk; v1 leans toward absence                                |
| No dietary setup                        | Open question highlights conflict with friction                                     |

***

## CASCADE-BACK NOTES (Gaps Identified)

1. **Single vs Multiple Meal Outputs**  
   Currently enforced as *one* in PRD, but explicitly unresolved in spec.

2. **Dietary/Allergy Constraints**  
   Safety concern vs friction tradeoff not resolved.

3. **Fridge vs Pantry Scope**  
   PRD fixes to fridge-only for clarity, but spec leaves open.

4. **User willingness to photograph fridge**  
   Critical assumption not validated — impacts entire product viability.

***

This PRD strictly operationalizes the core loop: **photo → one answer → cook**, preserving speed and decisiveness as the defining product constraints.
