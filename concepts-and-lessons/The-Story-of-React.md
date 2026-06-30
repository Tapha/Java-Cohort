# ⚛️ The Story of React — How UI Became a Function of State

Before React, the web already had its three classic powers:

```text
HTML = structure
CSS = presentation
JavaScript = behaviour
```

HTML gave the page meaning.

CSS gave the page style.

JavaScript gave the page movement.

But as web applications became more complex, a new problem appeared:

```text
How do we keep the screen correct
when the data keeps changing?
```

A page was no longer just a document.

It became a living interface.

Buttons changed values.

Forms updated state.

APIs returned data.

Errors appeared.

Loading screens came and went.

Lists grew.

Users logged in and out.

Screens needed to redraw.

The browser could do all of this.

But the code became hard to manage.

React was created to solve that problem.

```text
React = a way to build user interfaces from components that redraw when state changes.
```

---

# 🧠 1️⃣ The Problem Before React

JavaScript can directly change the page.

Example:

```javascript
document.getElementById("title").textContent = "Meal Ready";
```

This works.

But as applications grow, many parts of the page need to change.

Example:

```text
User clicks upload
        ↓
Button disables
        ↓
Loading spinner appears
        ↓
Image preview remains visible
        ↓
Backend request starts
        ↓
Meal response returns
        ↓
Meal title appears
        ↓
Ingredients appear
        ↓
Steps appear
        ↓
Loading spinner disappears
        ↓
Error message may appear if request fails
```

If you manually update each DOM element, the code becomes fragile.

You must remember:

```text
Which elements exist?
Which values changed?
Which parts need to update?
Which parts should stay the same?
Which old UI should disappear?
Which error should be cleared?
```

This is where traditional DOM manipulation becomes painful.

The app has truth.

The screen must reflect that truth.

But manually keeping them aligned gets harder as the app grows.

---

# 🪞 2️⃣ React’s Big Idea

React’s big idea is simple:

```text
Describe what the UI should look like for a given state.
```

Instead of manually changing the screen step by step, you describe the result.

React handles the redraw.

Core idea:

```text
UI = function of state
```

Meaning:

```text
If this is the current state,
this is what the screen should look like.
```

Example:

```text
state: loading = true

UI:
Show spinner
Disable button
Hide meal response
```

Then:

```text
state: loading = false
state: meal = Tomato Pasta

UI:
Hide spinner
Enable button
Show meal response
```

React is not mainly about buttons.

React is about keeping the UI aligned with changing truth.

---

# 🧱 3️⃣ Components: Breaking UI Into Pieces

React applications are built from components.

A component is a reusable piece of UI.

Example components:

```text
Header
MealCard
UploadButton
ImagePreview
IngredientList
StepList
LoadingSpinner
ErrorMessage
```

A page can be built by combining components.

Example:

```text
MealScreen
 ├─ Header
 ├─ ImagePreview
 ├─ UploadButton
 ├─ LoadingSpinner
 ├─ MealCard
 │   ├─ IngredientList
 │   └─ StepList
 └─ ErrorMessage
```

This is powerful because the UI becomes structured.

Instead of one large page with everything mixed together, each component owns a smaller piece of the interface.

```text
Component = named UI responsibility
```

---

# 🧠 4️⃣ Why Components Matter

Without components, UI code becomes one large block.

Everything knows about everything.

That becomes hard to change.

With components, we can separate concerns.

Example:

```text
MealCard displays a meal.

IngredientList displays ingredients.

UploadButton starts an upload.

ErrorMessage displays failure.
```

Each component has a clearer job.

This makes the UI easier to:

```text
read
test
reuse
change
debug
replace
```

Good components reduce chaos.

Bad components become mini-apps that do too much.

A component should have a clear reason to exist.

---

# 🧾 5️⃣ JSX: HTML-Looking JavaScript

React uses JSX.

JSX looks like HTML, but it is used inside JavaScript or TypeScript.

Example:

```tsx
function MealCard() {
  return (
    <View>
      <Text>Tomato Pasta</Text>
      <Text>20 minutes</Text>
    </View>
  );
}
```

JSX lets us describe UI structure close to the logic that controls it.

In web React, JSX often uses elements like:

```tsx
<div>
  <h1>Tomato Pasta</h1>
</div>
```

In React Native, JSX uses components like:

```tsx
<View>
  <Text>Tomato Pasta</Text>
</View>
```

Different rendering target.

Same React idea.

```text
JSX = UI structure written inside JavaScript/TypeScript
```

---

# 🔁 6️⃣ Rendering

Rendering means:

```text
Turning component descriptions into visible UI.
```

A React component returns what should appear.

Example:

```tsx
function Greeting() {
  return <Text>Hello</Text>;
}
```

React renders it.

If the data changes, React can render again.

That is important.

React components are not one-time drawings.

They can redraw when state or props change.

```text
state/props change
        ↓
component runs again
        ↓
React calculates new UI
        ↓
screen updates
```

This is the React loop.

---

# 📦 7️⃣ Props: Passing Data Into Components

Props are inputs to a component.

Example:

```tsx
type MealCardProps = {
  title: string;
  timeEstimateMinutes: number;
};

function MealCard({ title, timeEstimateMinutes }: MealCardProps) {
  return (
    <View>
      <Text>{title}</Text>
      <Text>{timeEstimateMinutes} minutes</Text>
    </View>
  );
}
```

Usage:

```tsx
<MealCard title="Tomato Pasta" timeEstimateMinutes={20} />
```

Props let parent components pass data to child components.

```text
Props = component inputs
```

They help keep components reusable.

One `MealCard` can display different meals depending on the props it receives.

---

# 🧠 8️⃣ Props Are Read-Only From the Child’s View

A child component should not directly change its props.

It receives them.

It renders from them.

Example:

```text
Parent owns meal data.

MealCard receives meal data.

MealCard displays meal data.
```

If the meal changes, the parent sends new props.

This gives React a clear direction of data flow.

```text
Parent → child
```

This is called one-way data flow.

It makes the app easier to reason about.

---

# 🧠 9️⃣ State: Data a Component Remembers

Props come from outside the component.

State is data the component remembers internally.

Example:

```tsx
const [count, setCount] = useState(0);
```

This means:

```text
count = current value
setCount = function used to change the value
```

Example:

```tsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <View>
      <Text>{count}</Text>
      <Button title="Add" onPress={() => setCount(count + 1)} />
    </View>
  );
}
```

Flow:

```text
button press
        ↓
setCount
        ↓
state changes
        ↓
component renders again
        ↓
screen shows new count
```

This is the heart of React.

```text
Events update state.

State updates UI.
```

---

# 🔄 1️⃣0️⃣ React’s Clean Flow

A clean React flow looks like this:

```text
User action
        ↓
Event handler
        ↓
State update
        ↓
Component re-renders
        ↓
UI changes
```

Example:

```text
Tap upload button
        ↓
handleUpload runs
        ↓
uploadStatus becomes "uploading"
        ↓
screen re-renders
        ↓
loading spinner appears
```

Then:

```text
backend returns meal
        ↓
mealSuggestion state is set
        ↓
uploadStatus becomes "success"
        ↓
screen re-renders
        ↓
meal appears
```

React helps keep the screen synchronized with state.

---

# 📸 1️⃣1️⃣ React in Fridge2Meal

For Fridge2Meal, imagine a screen with this state:

```tsx
type ImageUploadStatus =
  | "idle"
  | "ready"
  | "uploading"
  | "success"
  | "error";

type MealResponse = {
  title: string;
  usedIngredients: string[];
  steps: string[];
  timeEstimateMinutes: number;
};
```

A component may track:

```tsx
const [selectedImageUri, setSelectedImageUri] = useState<string | null>(null);
const [uploadStatus, setUploadStatus] = useState<ImageUploadStatus>("idle");
const [mealSuggestion, setMealSuggestion] = useState<MealResponse | null>(null);
const [errorMessage, setErrorMessage] = useState<string | null>(null);
```

This state tells the screen what is true.

Then the UI can render from it:

```text
if no image → show capture prompt
if image selected → show preview
if uploading → show spinner
if success → show meal suggestion
if error → show error message
```

The UI becomes a reflection of state.

---

# 🧠 1️⃣2️⃣ Conditional Rendering

Conditional rendering means:

```text
Show different UI depending on state.
```

Example:

```tsx
{uploadStatus === "uploading" && <LoadingSpinner />}

{uploadStatus === "error" && (
  <ErrorMessage message={errorMessage} />
)}

{uploadStatus === "success" && mealSuggestion && (
  <MealCard meal={mealSuggestion} />
)}
```

This is powerful.

The screen is no longer manually controlled.

It is derived from state.

```text
State decides what appears.
```

---

# ⚡ 1️⃣3️⃣ Events

Events are things that happen.

Examples:

```text
button press
text input change
form submit
screen load
image selected
API response returned
error thrown
```

In React, events usually trigger functions.

Example:

```tsx
<Button title="Upload" onPress={handleUpload} />
```

`handleUpload` might:

```text
set uploadStatus to uploading
create FormData
send axios request
receive response
set mealSuggestion
set uploadStatus to success
handle error if request fails
```

Events are the causes of state changes.

```text
Event → state update → UI redraw
```

---

# 🌐 1️⃣4️⃣ React and APIs

Modern React apps often talk to backends.

Example:

```tsx
async function handleUpload() {
  setUploadStatus("uploading");

  try {
    const formData = new FormData();

    formData.append("image", {
      uri: selectedImageUri,
      name: "fridge.jpg",
      type: "image/jpeg"
    } as any);

    const response = await axios.post<MealResponse>(
      "http://localhost:8080/api/meals/from-image",
      formData
    );

    setMealSuggestion(response.data);
    setUploadStatus("success");
  } catch (error) {
    setErrorMessage("Could not generate meal suggestion");
    setUploadStatus("error");
  }
}
```

This shows React handling a process over time:

```text
start upload
        ↓
loading
        ↓
success/error
        ↓
UI redraw
```

React does not remove the complexity.

It gives the complexity a clearer shape.

---

# ⏳ 1️⃣5️⃣ useEffect: Responding to Lifecycle and Changes

`useEffect` lets a component run logic after rendering or when certain values change.

Example:

```tsx
useEffect(() => {
  console.log("Meal suggestion changed");
}, [mealSuggestion]);
```

This means:

```text
When mealSuggestion changes,
run this effect.
```

Common uses:

```text
fetch data when screen loads
listen for changes
sync with external systems
clean up subscriptions
```

But `useEffect` should not be used for everything.

If something can happen directly in an event handler, keep it there.

Example:

```text
Button press should usually call handleUpload directly.
```

Use effects when logic needs to respond to render/change lifecycle.

---

# 🧩 1️⃣6️⃣ Hooks

React hooks are functions that let components use React features.

Common hooks:

```text
useState = remember local state
useEffect = run side effects
useMemo = memoize calculated values
useCallback = memoize functions
useRef = hold a value without causing re-render
```

For beginners, the first two matter most:

```text
useState
useEffect
```

Start there.

Do not rush into every hook.

The goal is to understand the React loop first.

```text
state changes
        ↓
render happens
```

---

# 🧠 1️⃣7️⃣ React Is Declarative

Declarative means:

```text
Describe the desired result.
```

Imperative means:

```text
Give step-by-step instructions for changing things.
```

Imperative DOM style:

```javascript
const title = document.getElementById("title");
title.textContent = "Tomato Pasta";
title.style.color = "green";
```

Declarative React style:

```tsx
<MealTitle title="Tomato Pasta" />
```

Or:

```tsx
{mealSuggestion && <MealCard meal={mealSuggestion} />}
```

React asks:

```text
Given the current state, what should the UI be?
```

That is declarative UI.

---

# 🧱 1️⃣8️⃣ React Is a Library, Not the Whole App

React helps build user interfaces.

It does not automatically solve everything.

React does not fully decide:

```text
routing
global state management
server-state caching
forms
API architecture
styling system
authentication
testing strategy
deployment
backend design
```

Other tools often help with those.

React’s core responsibility is UI.

```text
React renders interface from state and props.
```

Do not expect React to be the whole system.

React is one layer in the application.

---

# 🕸️ 1️⃣9️⃣ The Component Tree Problem

React UI is arranged as a tree.

Example:

```text
App
 ├─ Header
 ├─ HomeScreen
 └─ MealScreen
     ├─ ImagePicker
     ├─ UploadButton
     └─ MealCard
```

But the app’s data relationships may be more connected than the tree.

Example:

```text
User ↔ Meal Suggestions ↔ Saved Meals ↔ Preferences ↔ API
```

This is why React eventually needs state management.

Passing props is enough for small flows.

As the graph grows, state placement becomes harder.

That is why the next concept after React is state management.

React gives you components.

State management governs truth across those components.

---

# 🔁 2️⃣0️⃣ Re-rendering

A re-render happens when React runs a component again to calculate updated UI.

This can happen when:

```text
state changes
props change
parent component re-renders
```

Re-rendering is normal.

It is not automatically bad.

But unnecessary re-renders can become a performance issue in larger apps.

For beginners, the main point is:

```text
React redraws from state.

Do not manually redraw the screen yourself.
```

---

# 🧪 2️⃣1️⃣ React and Testing

React components can be tested.

Test questions:

```text
Does the component display the meal title?

Does the upload button call the right handler?

Does loading state show the spinner?

Does error state show an error message?

Does success state show meal details?
```

React testing is about proving UI behaviour.

Backend testing proves backend behaviour.

Full-loop testing proves the system works together.

```text
React test = UI behaviour proof
JUnit test = backend behaviour proof
Manual/API test = loop proof
Jenkins = repeated proof
```

---

# 📌 2️⃣2️⃣ Common Beginner Confusions

## React is not HTML

React uses JSX, which looks like HTML, but it is JavaScript/TypeScript syntax.

## React is not the browser

React describes UI.

The browser or React Native runtime displays it.

## Props and state are different

Props come from outside.

State is remembered inside.

## State changes should use setter functions

Do not mutate state directly.

Use:

```tsx
setCount(count + 1);
```

Not:

```tsx
count = count + 1;
```

## React is not magic

React redraws from state.

If the state is wrong, the UI will reflect wrong truth.

---

# 🧭 2️⃣3️⃣ What Students Should Be Ready to Understand Next

After this doc, learners should be ready to understand:

```text
components
JSX
props
state
events
conditional rendering
rendering from state
useState
basic useEffect
one-way data flow
why state management becomes important
```

Most importantly:

```text
React is not mainly about making screens.

React is about keeping screens aligned with changing state.
```

---

# 🚀 Final Compression

```text
React = UI library
Component = named piece of UI
JSX = UI structure inside JavaScript/TypeScript
Props = inputs passed into components
State = values a component remembers
Event = something that triggers logic
Render = React calculating UI
Re-render = React calculating UI again after change
Conditional rendering = UI depends on state
Hook = function that gives components React capabilities
useState = local state
useEffect = respond to lifecycle/change
```

---

# 🌌 Ultimate Compression

```text
HTML gave pages structure.

CSS gave pages style.

JavaScript gave pages behaviour.

React gave changing interfaces a repeatable shape.
```

React’s core idea is:

```text
When state changes, the UI should redraw from that state.
```

That is why React matters.
