# 🟣 The Story of Redux — How React Apps Learned Central Memory With Rules

React gave frontend development a clearer shape.

It taught us:

```text
Build UI from components.
Pass data through props.
Store changing values in state.
When state changes, the UI redraws.
```

That works well when the state is small.

A dropdown open or closed.

A counter value.

A selected tab.

A form input.

But as apps grow, a new problem appears:

```text
What happens when many components need the same truth?
```

A logged-in user is needed by the header, profile page, settings page, and API layer.

A cart is needed by product pages, the header badge, checkout, discounts, and payment.

A theme is needed everywhere.

A current lesson may be needed by navigation, progress tracking, content display, and assessment.

A meal suggestion may need to move between camera screen, result screen, save screen, and history screen.

This is where local state starts to struggle.

Redux was created to solve that kind of problem.

```text
Redux = central state management with strict rules for changing shared truth.
```

---

# 🧠 1️⃣ The Problem Redux Solves

In React, state often starts inside one component.

Example:

```text
MealScreen owns mealSuggestion.
```

That is fine if only `MealScreen` needs it.

But then more components need the same truth:

```text
MealCard needs mealSuggestion.
SaveMealButton needs mealSuggestion.
Header wants to show latest meal.
MealHistory wants to store it later.
Preferences may affect the suggestion.
Backend sync may need the same data.
```

Now the question becomes:

```text
Where should this shared truth live?
```

If the answer is:

```text
Copy it everywhere.
```

the app becomes fragile.

If the answer is:

```text
Pass it down through every component.
```

you may get prop drilling.

If the answer is:

```text
Let any component change it however it wants.
```

you get chaos.

Redux gives a stricter answer:

```text
Put important shared state in one central store.
Change it only through controlled actions.
Let the UI read from the store.
```

---

# 🧾 2️⃣ The Core Redux Idea

Redux has one main flow:

```text
UI dispatches action
        ↓
Reducer receives action
        ↓
Reducer creates new state
        ↓
Store saves new state
        ↓
UI re-renders from state
```

Simple compression:

```text
Action → Reducer → Store → UI
```

Or more fully:

```text
Event → Action → Reducer → State update → UI redraw
```

Redux is state management with a strong rule:

```text
State should not change randomly.
State should change through named events.
```

---

# 🏛️ 3️⃣ The Store

The store is the central place where shared application state lives.

Think of it as:

```text
The app’s central memory.
```

Example store shape:

```typescript
type RootState = {
  user: UserState;
  meal: MealState;
  theme: ThemeState;
};
```

For Fridge2Meal:

```typescript
type MealState = {
  selectedImageUri: string | null;
  uploadStatus: "idle" | "ready" | "uploading" | "success" | "error";
  mealSuggestion: MealResponse | null;
  errorMessage: string | null;
};
```

This says:

```text
The app has a central place to remember meal-related truth.
```

The store is not for every tiny piece of state.

It is for state that is shared, important, or needs controlled coordination.

---

# ⚡ 4️⃣ Actions

An action is a named event that says something happened.

Example:

```typescript
{
  type: "meal/uploadStarted"
}
```

Another example:

```typescript
{
  type: "meal/uploadSucceeded",
  payload: {
    title: "Tomato Pasta",
    usedIngredients: ["tomato", "pasta", "cheese"],
    steps: ["Boil pasta", "Cook tomatoes", "Mix together"],
    timeEstimateMinutes: 20
  }
}
```

Actions do not directly change state.

They describe what happened.

```text
Action = named fact/event
```

Examples:

```text
imageSelected
uploadStarted
uploadSucceeded
uploadFailed
mealCleared
userLoggedIn
userLoggedOut
themeChanged
```

Good actions are clear.

Bad actions are vague.

Good:

```text
meal/uploadSucceeded
```

Weak:

```text
updateStuff
```

---

# 🧠 5️⃣ Reducers

A reducer decides how state changes when an action happens.

It receives:

```text
previous state
action
```

and returns:

```text
next state
```

Simple map:

```text
(previous state, action) → next state
```

Example:

```typescript
function mealReducer(state, action) {
  if (action.type === "meal/uploadStarted") {
    return {
      ...state,
      uploadStatus: "uploading",
      errorMessage: null
    };
  }

  return state;
}
```

The reducer is the rule layer.

It answers:

```text
Given what was true before,
and given what just happened,
what should be true now?
```

That is why Redux is controlled memory.

---

# 🔒 6️⃣ Redux Rule: Do Not Mutate State Directly

Redux state should not be changed randomly.

Bad:

```typescript
state.uploadStatus = "success";
```

Classic Redux expects you to return a new state object:

```typescript
return {
  ...state,
  uploadStatus: "success",
  mealSuggestion: action.payload
};
```

Why?

Because Redux needs to know:

```text
State changed.
UI should update.
```

Changing state directly can hide changes.

Modern Redux Toolkit makes this easier because it lets you write code that looks like mutation, but safely handles immutability behind the scenes.

Example with Redux Toolkit:

```typescript
state.uploadStatus = "success";
state.mealSuggestion = action.payload;
```

This looks like mutation.

But Redux Toolkit uses Immer to produce safe immutable updates.

Beginner rule:

```text
With classic Redux, return new state.
With Redux Toolkit, write simple updates inside reducers.
```

---

# 🧰 7️⃣ Redux Toolkit

Modern Redux is usually written with Redux Toolkit.

Redux Toolkit reduces boilerplate.

It gives you:

```text
configureStore
createSlice
createAsyncThunk
```

The most important beginner concept is:

```text
slice
```

A slice groups together:

```text
a piece of state
the reducers that change it
the actions connected to those reducers
```

Example slices:

```text
userSlice
mealSlice
cartSlice
themeSlice
```

For Fridge2Meal:

```text
mealSlice
```

would manage the meal upload and suggestion state.

---

# 🧩 8️⃣ Slice

A slice is a named section of Redux state plus its update rules.

Example:

```typescript
import { createSlice, PayloadAction } from "@reduxjs/toolkit";

type MealResponse = {
  title: string;
  usedIngredients: string[];
  steps: string[];
  timeEstimateMinutes: number;
};

type MealState = {
  selectedImageUri: string | null;
  uploadStatus: "idle" | "ready" | "uploading" | "success" | "error";
  mealSuggestion: MealResponse | null;
  errorMessage: string | null;
};

const initialState: MealState = {
  selectedImageUri: null,
  uploadStatus: "idle",
  mealSuggestion: null,
  errorMessage: null
};

const mealSlice = createSlice({
  name: "meal",
  initialState,
  reducers: {
    imageSelected(state, action: PayloadAction<string>) {
      state.selectedImageUri = action.payload;
      state.uploadStatus = "ready";
      state.errorMessage = null;
    },

    uploadStarted(state) {
      state.uploadStatus = "uploading";
      state.errorMessage = null;
    },

    uploadSucceeded(state, action: PayloadAction<MealResponse>) {
      state.uploadStatus = "success";
      state.mealSuggestion = action.payload;
      state.errorMessage = null;
    },

    uploadFailed(state, action: PayloadAction<string>) {
      state.uploadStatus = "error";
      state.errorMessage = action.payload;
    },

    mealCleared(state) {
      state.selectedImageUri = null;
      state.uploadStatus = "idle";
      state.mealSuggestion = null;
      state.errorMessage = null;
    }
  }
});

export const {
  imageSelected,
  uploadStarted,
  uploadSucceeded,
  uploadFailed,
  mealCleared
} = mealSlice.actions;

export default mealSlice.reducer;
```

This gives the feature a controlled truth map.

---

# 🔄 9️⃣ Dispatch

Dispatch means:

```text
Send an action to the store.
```

Example:

```typescript
dispatch(uploadStarted());
```

This says:

```text
The upload has started.
```

Then Redux sends that action to the reducer.

The reducer updates the state.

Then React components using that state can re-render.

Flow:

```text
button pressed
        ↓
dispatch(uploadStarted())
        ↓
meal reducer updates uploadStatus
        ↓
store saves new state
        ↓
UI shows loading spinner
```

Dispatch is how the UI announces that something happened.

---

# 👀 1️⃣0️⃣ Selector

A selector reads a specific piece of state from the store.

Example:

```typescript
const uploadStatus = useSelector(
  (state: RootState) => state.meal.uploadStatus
);
```

This means:

```text
This component cares about uploadStatus.
```

Another example:

```typescript
const mealSuggestion = useSelector(
  (state: RootState) => state.meal.mealSuggestion
);
```

Selector rule:

```text
Components should read only the state they need.
```

Good selectors reduce unnecessary coupling.

---

# 🧠 1️⃣1️⃣ Redux Flow in Fridge2Meal

Imagine the user selects a fridge image.

```text
User selects image
        ↓
dispatch(imageSelected(imageUri))
        ↓
Redux stores selectedImageUri
        ↓
uploadStatus becomes ready
        ↓
UI shows image preview and upload button
```

Then the user uploads:

```text
User taps upload
        ↓
dispatch(uploadStarted())
        ↓
UI shows loading
        ↓
axios multipart POST sends image
        ↓
backend returns MealResponse
        ↓
dispatch(uploadSucceeded(response.data))
        ↓
Redux stores mealSuggestion
        ↓
UI shows meal card
```

If backend fails:

```text
axios request fails
        ↓
dispatch(uploadFailed("Could not generate meal suggestion"))
        ↓
Redux stores errorMessage
        ↓
UI shows error
```

This is controlled state flow.

---

# 🧠 1️⃣2️⃣ Redux and Time

Real app state happens over time.

Example:

```text
idle
        ↓
imageSelected
ready
        ↓
uploadStarted
uploading
        ↓
uploadSucceeded
success
```

Or:

```text
idle
        ↓
imageSelected
ready
        ↓
uploadStarted
uploading
        ↓
uploadFailed
error
```

Redux gives these transitions names.

Instead of hidden random changes, we get clear events:

```text
imageSelected
uploadStarted
uploadSucceeded
uploadFailed
```

This makes debugging easier because you can ask:

```text
What action happened?
What was the state before?
What did the reducer change?
What is the state now?
```

Redux makes time inspectable.

---

# 🧪 1️⃣3️⃣ Redux and Debugging

Redux is useful because state changes are explicit.

If the UI is wrong, you can trace the flow:

```text
Was the correct action dispatched?
Did the reducer handle it?
Did the state update correctly?
Did the component select the right state?
Did the component render the correct UI?
```

This is much cleaner than:

```text
Something changed somewhere.
```

Redux makes state changes visible as a chain.

```text
Event → Action → Reducer → Store → UI
```

That chain is the debugging map.

---

# 🧱 1️⃣4️⃣ Redux vs Local State

Redux should not replace all local state.

Use local state for:

```text
dropdown open/closed
temporary input value
hover state
small component-only UI details
```

Use Redux for:

```text
shared state
important app-wide truth
state needed by many screens/components
state that benefits from central rules
state that needs clear debugging
```

Bad use of Redux:

```text
Every single small UI value goes into Redux.
```

Better:

```text
Local state stays local.
Shared truth goes central.
```

Redux is powerful.

But unnecessary centralization creates noise.

---

# 🌐 1️⃣5️⃣ Redux and Server State

Redux can store server data.

But server state has special concerns:

```text
fetching
loading
caching
refetching
stale data
errors
synchronization
```

For server-heavy apps, tools like RTK Query, React Query, or SWR can help manage server state.

But for learning Redux, the key idea is:

```text
Redux can hold the frontend’s current copy of server-related truth.
```

For Fridge2Meal:

```text
mealSuggestion returned from backend
```

can live in Redux if multiple screens need it.

But remember:

```text
The backend is still the durable source of truth
when data is saved.
```

---

# 📦 1️⃣6️⃣ Store Setup

A Redux store is configured once.

Example:

```typescript
import { configureStore } from "@reduxjs/toolkit";
import mealReducer from "./mealSlice";

export const store = configureStore({
  reducer: {
    meal: mealReducer
  }
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

This creates the central store.

Then the app is wrapped with a `Provider`.

Example:

```tsx
import { Provider } from "react-redux";
import { store } from "./store";

export default function App() {
  return (
    <Provider store={store}>
      <RootNavigator />
    </Provider>
  );
}
```

The `Provider` makes the Redux store available to React components.

---

# ⚛️ 1️⃣7️⃣ React Components With Redux

A component can read from Redux using `useSelector`.

It can send actions using `useDispatch`.

Example:

```tsx
import { useDispatch, useSelector } from "react-redux";
import type { RootState } from "../store";
import { uploadStarted, uploadSucceeded, uploadFailed } from "./mealSlice";

function MealUploadScreen() {
  const dispatch = useDispatch();

  const uploadStatus = useSelector(
    (state: RootState) => state.meal.uploadStatus
  );

  const mealSuggestion = useSelector(
    (state: RootState) => state.meal.mealSuggestion
  );

  async function handleUpload() {
    dispatch(uploadStarted());

    try {
      const response = await uploadImage();
      dispatch(uploadSucceeded(response.data));
    } catch (error) {
      dispatch(uploadFailed("Could not generate meal suggestion"));
    }
  }

  return null;
}
```

This component does not own the shared meal state locally.

It reads from the central store.

It dispatches named changes.

---

# 🧠 1️⃣8️⃣ Redux and TypeScript

TypeScript makes Redux safer.

It helps define:

```text
state shape
action payload shape
selector return types
dispatch type
API response shape
```

Example:

```typescript
type MealState = {
  selectedImageUri: string | null;
  uploadStatus: "idle" | "ready" | "uploading" | "success" | "error";
  mealSuggestion: MealResponse | null;
  errorMessage: string | null;
};
```

This protects the store from unclear truth.

If a reducer tries:

```typescript
state.uploadStatus = "maybe_done";
```

TypeScript complains.

Because allowed values are only:

```text
idle
ready
uploading
success
error
```

Types make the state rules clearer.

---

# 🔁 1️⃣9️⃣ Async Logic

Uploading an image is asynchronous.

That means:

```text
request starts now
response arrives later
```

There are two common learning approaches.

## Simple approach

Do the async work in the component:

```text
dispatch(uploadStarted)
await axios request
dispatch(uploadSucceeded or uploadFailed)
```

This is easier for beginners.

## More structured approach

Use async thunks or RTK Query.

Example concept:

```text
Thunk = async action logic that can dispatch multiple actions over time.
```

For now, simple component-level async is acceptable for learning.

The important idea is:

```text
Async work should still produce clear state transitions.
```

---

# 🧨 2️⃣0️⃣ Common Redux Mistakes

## Putting everything in Redux

Not all state should be global.

Keep local state local.

## Vague action names

Bad:

```text
setData
updateThing
changeState
```

Better:

```text
uploadStarted
uploadSucceeded
uploadFailed
imageSelected
mealCleared
```

## Duplicating state

If a value can be derived, avoid storing it separately.

## Making reducers too clever

Reducers should update state.

They should not become messy business logic monsters.

## Forgetting the source of truth

Redux may store frontend truth.

But server data still belongs to the backend when persisted.

## Dispatching random changes everywhere

Redux should make state flow clearer, not noisier.

---

# 🧭 2️⃣1️⃣ When Should You Reach for Redux?

Redux may be useful when:

```text
Many components need the same state.
State changes are hard to trace.
The same truth is duplicated in several places.
Prop drilling becomes painful.
The app has complex flows over time.
Debugging state changes is difficult.
You need strict, named state transitions.
```

Redux may be unnecessary when:

```text
Only one component needs the state.
The state is temporary UI detail.
Passing props is simple enough.
The app is small.
The extra structure creates more complexity than it removes.
```

Redux is not the default answer to every state problem.

Redux is useful when shared truth needs stronger governance.

---

# 📸 2️⃣2️⃣ Fridge2Meal Redux Readiness Questions

Before using Redux for the Image to Meal flow, ask:

```text
Does more than one screen need the meal suggestion?
Does the upload status need to be shown outside the upload screen?
Does the selected image need to survive navigation?
Do multiple components need to know the current meal?
Is prop drilling starting to appear?
Would named actions make debugging easier?
Is this state local, shared, server, or derived?
```

If most answers point to shared truth, Redux may help.

If the state belongs only to one screen, local state may be enough.

---

# 🧪 2️⃣3️⃣ Testing Redux Logic

Reducers are testable because they are predictable.

A reducer receives:

```text
previous state
action
```

and returns:

```text
next state
```

Example test idea:

```text
Given uploadStatus is idle
When uploadStarted action is handled
Then uploadStatus should become uploading
```

Example:

```typescript
test("uploadStarted sets uploadStatus to uploading", () => {
  const previousState = {
    selectedImageUri: null,
    uploadStatus: "idle",
    mealSuggestion: null,
    errorMessage: null
  };

  const nextState = mealReducer(previousState, uploadStarted());

  expect(nextState.uploadStatus).toBe("uploading");
  expect(nextState.errorMessage).toBeNull();
});
```

Redux is testable because the rules are explicit.

---

# 🧠 2️⃣4️⃣ How Redux Connects to Everything Else

Redux sits between React and broader state management.

React says:

```text
Render UI from state.
```

State management asks:

```text
Where does truth live?
Who can change it?
Who needs to know?
```

Redux answers:

```text
Put important shared truth in a store.
Change it with actions.
Use reducers to control transitions.
Let UI read from state.
```

Connection map:

```text
React = UI redraws from state
Redux = central shared state with named transitions
TypeScript = shape safety for state/actions
Axios/API = source of server responses
Backend DTO = contract for returned data
Testing = proof that transitions behave correctly
Jenkins = repeated proof on every change
```

---

# 🚀 Final Compression

```text
Redux = central state management with rules
Store = central memory
Action = named event
Payload = data carried by an action
Reducer = rule that turns old state + action into new state
Slice = state section plus reducers/actions
Dispatch = send an action
Selector = read state
Provider = gives React access to the store
Redux Toolkit = modern simpler Redux
```

---

# 🌌 Ultimate Compression

```text
React redraws the UI from state.

Redux gives shared state a central memory and controlled change path.
```

When local truth is no longer enough, Redux gives the app a stricter way to remember, change, and share what is true.
