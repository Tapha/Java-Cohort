# FOUNDING SPEC — "Fridge → Meal" (working title)

## 1. PRODUCT TRUTH
A camera-first app that turns a photo of your fridge into one meal you can cook right now — built for the person standing at the open door deciding whether to order out again.

That sentence holds. The notes decided this even where they didn't notice they had.

## 2. THE USER
Not "busy 25-year-old who doomscrolls." The *moment*: it's 1pm or 8pm, they're hungry, the fridge is open, the delivery app is two thumb-taps away, and the only thing stopping the £18 order is that nobody has told them the food already in front of them is enough. They are not a planner. They will not log groceries. They decide at the point of hunger, every time, fresh. The app exists to win that thirty-second standoff against Deliveroo.

## 3. THE CORE LOOP
**Open app → camera is already live → photograph the fridge → get one meal they can make now, with a time estimate.** That's the whole organism. Take the photo, get the answer, cook. Everything else — saved recipes, sharing, generated images, cupboard shots, expiry dates — is scaffolding hung off this loop. If the loop is slow or asks a question before answering, the product is dead and they've ordered out.

## 4. WHY NOW
Multimodal vision crossed the line where a single casual fridge photo reliably yields a usable ingredient list — cheaply, in seconds, without manual entry. Two years ago this loop required typing in what you own, which no hungry person does. That's the unlock. Secondary tailwind: food discovery has already migrated to the feed (Pinterest/Instagram/TikTok cooking), so reaching for a phone to *decide* what to eat is now native behaviour, not a new habit to teach.

## 5. SUCCESS CRITERIA
- Users open the app at the **decision moment** (hunger + fridge), not idly — measurable by time-of-day clustering around meals.
- A meaningful share of sessions end in "I cooked this," not just browsing — track the cook-it tap, ideally a confirmation.
- **Order-out deferral**: self-reported or inferred — they came in leaning toward delivery and didn't.
- Repeat photographing across weeks. One photo is a novelty; the tenth is a product.
- Time from app-open to first viable suggestion stays under ~10 seconds.

## 6. NON-GOALS (v1)
- **Not a recipe database or browser.** It answers one question; it is not a place to go looking through recipes.
- **Not a meal planner.** No weekly plans, no "what to eat Tuesday." It only knows *now*.
- **Not an inventory/pantry manager.** No tracking what you own over time, no expiry dates, no stock levels. The photo is the inventory, every time, from scratch.
- **Not a social network.** Sharing and AI-generated "look at my food" images are the most seductive distraction in these notes and the fastest way to build the wrong product. Cut entirely from v1.
- **Not a grocery/shopping app.** "Ingredient recommendations to improve the meal" stays out until the core loop is proven.

## 7. LOAD-BEARING ASSUMPTIONS
- **[INFERRED] — KILLS THE PRODUCT IF FALSE:** A single, casual, badly-lit fridge photo yields enough correctly-identified ingredients to propose a *real, cookable* meal. If detection is unreliable or forces manual correction, the loop collapses back into data entry and there is no product.
- **[STATED]** The user genuinely wants to eat in but defaults to ordering out — the app's job is interception, not inspiration. ("Deliberates whether to order out or eat in.")
- **[STATED]** The app always returns *something* to cook, however limited the ingredients. A "no meal found" result is a failure state, not an honest answer.
- **[INFERRED]** People will physically point a camera at their fridge at the moment of hunger. (Notes assume this; it is unproven behaviour and the second-riskiest belief here.)
- **[INFERRED]** "One meal for now," not a list of options, is the right output. The notes say "suggests food options" (plural); committing to *one* decisive answer is my call and worth testing.

## 8. OPEN QUESTIONS
- One suggestion or several? Notes say "options"; the loop argues for one. Unresolved.
- Do AI-generated food images build appetite/trust or quietly erode it (you cook it, it looks nothing like the render)? Real-photo-only may be the more honest v1.
- Are dietary/allergy filters v1 or later? They're safety-adjacent, which argues for early — but they add a setup step that fights the zero-friction loop.
- Fridge only, or cupboards/storage too? Notes want both; the photo loop gets murkier the more places you have to shoot.
- Monetisation — entirely absent from the notes.
- The whole social/sharing/Pinterest-integration cluster: parked as non-goal here, but the notes clearly *want* it. Is it a later layer or a different product?

---

*Note: the `{{DRIFT_AND_CASCADE_FOOTER}}` placeholder came through unfilled, so I left it out rather than invent one. If you want the cascade-back / drift-detection footer appended, hand me the template and I'll fit the spec to it.*
