# 🧠 Exercise: From Data to State to Action in React and Redux

## Central Teaching Line

```text
State is the present tense of data.
Information is what that present state means for action.
```

This exercise helps you connect five ideas:

```text
Data → State → Information → Decision → Action
```

You will first practise the chain conceptually.

Then you will apply it to React.

Then you will apply it to Redux.

The goal is to understand why state management matters, and why Redux exists.

Redux is not just “global state.”

Redux exists to make state changes more:

```text
sequential
predictable
visible
rule-based
debuggable
```

than scattered component updates.

---

# 1️⃣ Short Explanation

## Data

Data is a raw value.

Example:

```text
cartCount = 3
```

By itself, this is just a value.

It does not yet say where the value lives, what it means, or what should happen next.

---

## State

State is present-tense data inside a system.

Example:

```text
state.cartCount = 3
```

This means:

```text
Right now, the system believes the cart contains 3 items.
```

State is data held inside a running system.

It is not just a value.

It is the system’s current truth.

---

## Information

Information is produced when present state is interpreted against context, uncertainty, purpose, or possible action.

Example:

```text
state.cartCount > 0
```

This means:

```text
The user has something to buy.
```

The raw value was:

```text
3
```

The state was:

```text
cartCount is currently 3
```

The information is:

```text
the cart is not empty
```

Information is what state means.

---

## Decision

A decision follows from information.

Example:

```text
Enable checkout.
```

Because the cart is not empty, the system can decide that checkout should be available.

---

## Action

An action is what happens in the UI or system.

Example:

```text
Show the checkout button.
```

The user sees the consequence.

---

# 2️⃣ The Chain

Use this chain throughout the exercise:

```text
Data → State → Information → Decision → Action
```

Shopping cart example:

```text
Data:
cartCount = 3

State:
state.cartCount = 3

Information:
cartCount > 0 means the user has something to buy

Decision:
Enable checkout

Action:
Show checkout button
```

---

# 3️⃣ How This Connects to React

In React:

```text
useState stores present-tense component state.
Events call setter functions.
When state changes, React re-renders.
The UI is a visual consequence of current state.
```

React flow:

```text
User clicks button
        ↓
event handler runs
        ↓
setter updates state
        ↓
React re-renders
        ↓
UI changes
```

Simple version:

```text
Event → State update → UI redraw
```

But inside that flow, the deeper chain is:

```text
Data → State → Information → Decision → Action
```

---

# 4️⃣ How This Connects to Redux

In Redux:

```text
Redux stores application-level present state.
Actions describe what happened.
Reducers apply sequential rules to produce the next state.
Selectors read state and turn it into useful information for the UI.
Components dispatch actions and render based on selected information.
```

Redux flow:

```text
Component dispatches action
        ↓
Reducer receives current state and action
        ↓
Reducer returns next state
        ↓
Store saves next state
        ↓
Selector reads state
        ↓
Component renders UI
```

Simple version:

```text
Action → Reducer → Store → Selector → UI
```

Redux makes state changes more sequential than scattered component updates.

---

# 5️⃣ Warm-Up: Classify the Examples

For each item, write one of:

```text
Data
State
Information
Decision
Action
```

| # | Example | Your Answer |
|---|---|---|
| 1 | `cartCount = 3` | |
| 2 | `state.cartCount = 3` | |
| 3 | `cartCount > 0` means the cart is not empty | |
| 4 | Enable checkout | |
| 5 | Show checkout button | |
| 6 | `isLoggedIn = false` | |
| 7 | `state.isLoggedIn = false` | |
| 8 | User is not allowed to view dashboard | |
| 9 | Redirect to login page | |
| 10 | Hide dashboard link | |
| 11 | `stockLevel = 0` | |
| 12 | `state.stockLevel = 0` | |
| 13 | Product is unavailable | |
| 14 | Disable add-to-cart | |
| 15 | Show “Out of stock” message | |

---

# 6️⃣ React `useState` Example: Shopping Cart

## Goal

Build the chain using React local state.

```text
Data → State → Information → Decision → Action
```

## React Code

```tsx
import { useState } from "react";
import { Button, Text, View } from "react-native";

export default function CartExample() {
  const [cartCount, setCartCount] = useState(0);

  const hasItems = cartCount > 0;
  const checkoutMessage = hasItems
    ? "You can checkout"
    : "Your cart is empty";

  function handleAddItem() {
    setCartCount(cartCount + 1);
  }

  function handleRemoveItem() {
    if (cartCount > 0) {
      setCartCount(cartCount - 1);
    }
  }

  return (
    <View>
      <Text>Cart count: {cartCount}</Text>

      <Text>{checkoutMessage}</Text>

      <Button title="Add item" onPress={handleAddItem} />

      <Button title="Remove item" onPress={handleRemoveItem} />

      {hasItems && (
        <Button title="Checkout" onPress={() => console.log("Checkout")} />
      )}
    </View>
  );
}
```

## Chain Breakdown

| Chain Step | React Example |
|---|---|
| Data | `0`, `1`, `2`, `3` |
| State | `cartCount` stored by `useState` |
| Information | `hasItems = cartCount > 0` |
| Decision | If `hasItems`, checkout should be available |
| Action | Render checkout button |

## Key Point

```text
cartCount is state.
hasItems is information derived from state.
The checkout button is a UI action/consequence.
```

---

# 7️⃣ React Tasks

## Task A

Current code:

```tsx
const [cartCount, setCartCount] = useState(3);

const hasItems = cartCount > 0;
```

Complete the chain:

```text
Data:
...

State:
...

Information:
...

Decision:
...

Action:
...
```

---

## Task B

Current code:

```tsx
const [cartCount, setCartCount] = useState(0);

const hasItems = cartCount > 0;
```

Complete the chain:

```text
Data:
...

State:
...

Information:
...

Decision:
...

Action:
...
```

---

## Task C

Add a new derived value:

```tsx
const cartLabel = cartCount === 1 ? "1 item" : `${cartCount} items`;
```

Answer:

```text
Is cartLabel data, state, information, decision, or action?

Explain why:
...
```

---

# 8️⃣ Redux Version of the Same Example

Now we move the shopping cart state into Redux.

This is useful if many parts of the app need the cart count:

```text
Header
Cart screen
Checkout screen
Product screen
Navigation badge
```

Instead of scattered state updates, Redux gives us:

```text
central state
named actions
reducers
selectors
component rendering logic
```

---

## Initial State

```typescript
type CartState = {
  cartCount: number;
};

const initialState: CartState = {
  cartCount: 0
};
```

This means:

```text
Right now, the application believes the cart contains 0 items.
```

---

## Action Objects

Actions describe what happened.

```typescript
const addItemAction = {
  type: "cart/itemAdded"
};

const removeItemAction = {
  type: "cart/itemRemoved"
};
```

These are not state changes by themselves.

They are named events.

```text
itemAdded happened
itemRemoved happened
```

---

## Reducer

The reducer applies the rules.

```typescript
function cartReducer(
  state = initialState,
  action: { type: string }
): CartState {
  switch (action.type) {
    case "cart/itemAdded":
      return {
        ...state,
        cartCount: state.cartCount + 1
      };

    case "cart/itemRemoved":
      return {
        ...state,
        cartCount: Math.max(0, state.cartCount - 1)
      };

    default:
      return state;
  }
}
```

The reducer says:

```text
Given the current state and the action,
calculate the next state.
```

This is sequential state discipline.

---

## Selector

A selector reads state and can turn it into useful information.

```typescript
type RootState = {
  cart: CartState;
};

function selectCartCount(state: RootState) {
  return state.cart.cartCount;
}

function selectHasItems(state: RootState) {
  return state.cart.cartCount > 0;
}

function selectCheckoutMessage(state: RootState) {
  return state.cart.cartCount > 0
    ? "You can checkout"
    : "Your cart is empty";
}
```

Selectors help produce information from state.

```text
cartCount = state
hasItems = information
checkoutMessage = information for UI
```

---

## Component Rendering Logic

```tsx
import { Button, Text, View } from "react-native";
import { useDispatch, useSelector } from "react-redux";

export default function CartExampleRedux() {
  const dispatch = useDispatch();

  const cartCount = useSelector(selectCartCount);
  const hasItems = useSelector(selectHasItems);
  const checkoutMessage = useSelector(selectCheckoutMessage);

  return (
    <View>
      <Text>Cart count: {cartCount}</Text>

      <Text>{checkoutMessage}</Text>

      <Button
        title="Add item"
        onPress={() => dispatch({ type: "cart/itemAdded" })}
      />

      <Button
        title="Remove item"
        onPress={() => dispatch({ type: "cart/itemRemoved" })}
      />

      {hasItems && (
        <Button title="Checkout" onPress={() => console.log("Checkout")} />
      )}
    </View>
  );
}
```

## Redux Chain Breakdown

| Chain Step | Redux Example |
|---|---|
| Data | raw values like `0`, `1`, `2`, `3` |
| State | `state.cart.cartCount` |
| Information | `selectHasItems(state)` |
| Decision | If `hasItems`, checkout should be available |
| Action | Component renders checkout button |

---

# 9️⃣ Redux Tasks

## Task D: Fill in the Chain

Given:

```typescript
const state = {
  cart: {
    cartCount: 2
  }
};

const hasItems = selectHasItems(state);
```

Complete:

```text
Data:
...

State:
...

Information:
...

Decision:
...

Action:
...
```

---

## Task E: Identify the Redux Piece

Classify each item as:

```text
Initial state
Action
Reducer
Selector
Component rendering logic
```

| # | Code / Description | Your Answer |
|---|---|---|
| 1 | `{ type: "cart/itemAdded" }` | |
| 2 | `cartCount: 0` | |
| 3 | `state.cart.cartCount > 0` | |
| 4 | `case "cart/itemAdded": return ...` | |
| 5 | `{hasItems && <Button title="Checkout" />}` | |
| 6 | `dispatch({ type: "cart/itemRemoved" })` | |
| 7 | `selectCheckoutMessage(state)` | |

---

## Task F: Why Redux?

Answer in 3-5 sentences:

```text
Why might Redux be useful if Header, CartScreen, ProductScreen,
and CheckoutScreen all need cart information?
```

Use these words:

```text
shared state
actions
reducer
selector
predictable
```

---

# 🔟 Fill in the Missing Chain

Complete each row.

| Scenario | Data | State | Information | Decision | Action |
|---|---|---|---|---|---|
| Cart has 3 items | `cartCount = 3` | `state.cartCount = 3` | | | |
| User is logged out | `isLoggedIn = false` | | | Redirect to login | |
| Form email is empty | | `state.email = ""` | Email is missing | | Show validation error |
| Stock is 0 | `stockLevel = 0` | | Product unavailable | | |
| Player health is 20 | `health = 20` | `state.health = 20` | Health is low | Use warning state | |

---

# 1️⃣1️⃣ Debugging Section: Incorrect State Updates

## Bug A: Direct Mutation in React

Bad code:

```tsx
const [cartCount, setCartCount] = useState(0);

function handleAddItem() {
  cartCount = cartCount + 1;
}
```

Questions:

```text
1. What is wrong with this code?
2. Why will React not reliably re-render?
3. What should the code do instead?
```

Correct shape:

```tsx
setCartCount(cartCount + 1);
```

---

## Bug B: Derived Information Stored Incorrectly

Bad code:

```tsx
const [cartCount, setCartCount] = useState(0);
const [hasItems, setHasItems] = useState(false);

function handleAddItem() {
  setCartCount(cartCount + 1);
}
```

Problem:

```text
hasItems may not update when cartCount changes.
```

Questions:

```text
1. Why is hasItems not good source state?
2. What should hasItems be derived from?
3. Rewrite hasItems correctly.
```

Correct shape:

```tsx
const hasItems = cartCount > 0;
```

---

## Bug C: Redux Reducer Mutates State Incorrectly

Bad classic Redux reducer:

```typescript
function cartReducer(state = initialState, action) {
  switch (action.type) {
    case "cart/itemAdded":
      state.cartCount = state.cartCount + 1;
      return state;

    default:
      return state;
  }
}
```

Questions:

```text
1. What is wrong with this reducer in classic Redux?
2. Why should the reducer return a new state object?
3. Rewrite it correctly.
```

Correct shape:

```typescript
return {
  ...state,
  cartCount: state.cartCount + 1
};
```

Note:

```text
Redux Toolkit allows simpler-looking updates inside createSlice,
but classic Redux reducers should return new state objects.
```

---

## Bug D: Selector Does Too Little

Weak selector:

```typescript
function selectCartCount(state) {
  return state.cart.cartCount;
}
```

Component logic:

```tsx
const cartCount = useSelector(selectCartCount);

const hasItems = cartCount > 0;
const checkoutMessage = hasItems ? "You can checkout" : "Your cart is empty";
```

This is not always wrong.

But if many components repeat the same logic, it becomes duplication.

Questions:

```text
1. What useful information could a selector provide?
2. Why might selectHasItems be better?
3. Why might selectCheckoutMessage be useful?
```

Better selectors:

```typescript
function selectHasItems(state) {
  return state.cart.cartCount > 0;
}

function selectCheckoutMessage(state) {
  return state.cart.cartCount > 0
    ? "You can checkout"
    : "Your cart is empty";
}
```

---

# 1️⃣2️⃣ Final Challenge

Create your own example using one of these scenarios:

```text
login status
form validation
stock level
game health
```

You must include:

```text
1. Data
2. State
3. Information
4. Decision
5. Action
6. React useState version
7. Redux version with:
   - initial state
   - action object
   - reducer
   - selector
   - component rendering logic
```

## Template

```text
Scenario:
...

Data:
...

State:
...

Information:
...

Decision:
...

Action:
...
```

## React Version

```tsx
// write your useState example here
```

## Redux Version

```typescript
// write your initial state, action, reducer, and selector here
```

## Component Rendering Logic

```tsx
// show how the component uses selected information
```

---

# ✅ Answer Key

## Warm-Up Answers

| # | Example | Answer |
|---|---|---|
| 1 | `cartCount = 3` | Data |
| 2 | `state.cartCount = 3` | State |
| 3 | `cartCount > 0` means cart is not empty | Information |
| 4 | Enable checkout | Decision |
| 5 | Show checkout button | Action |
| 6 | `isLoggedIn = false` | Data |
| 7 | `state.isLoggedIn = false` | State |
| 8 | User is not allowed to view dashboard | Information |
| 9 | Redirect to login page | Decision |
| 10 | Hide dashboard link | Action |
| 11 | `stockLevel = 0` | Data |
| 12 | `state.stockLevel = 0` | State |
| 13 | Product is unavailable | Information |
| 14 | Disable add-to-cart | Decision |
| 15 | Show “Out of stock” message | Action |

---

## Task A Answer

```text
Data:
cartCount = 3

State:
React currently stores cartCount as 3

Information:
cartCount > 0 means the cart is not empty

Decision:
Checkout should be enabled

Action:
Show the checkout button
```

---

## Task B Answer

```text
Data:
cartCount = 0

State:
React currently stores cartCount as 0

Information:
cartCount > 0 is false, so the cart is empty

Decision:
Checkout should be disabled or unavailable

Action:
Hide the checkout button or show “Your cart is empty”
```

---

## Task C Answer

```text
cartLabel is information.

It is derived from cartCount.

It should usually not be stored separately because it can be calculated from current state.
```

---

## Task D Answer

```text
Data:
cartCount = 2

State:
state.cart.cartCount = 2

Information:
selectHasItems(state) returns true, so the cart is not empty

Decision:
Checkout should be available

Action:
Render the checkout button
```

---

## Task E Answers

| # | Code / Description | Answer |
|---|---|---|
| 1 | `{ type: "cart/itemAdded" }` | Action |
| 2 | `cartCount: 0` | Initial state |
| 3 | `state.cart.cartCount > 0` | Selector / information logic |
| 4 | `case "cart/itemAdded": return ...` | Reducer |
| 5 | `{hasItems && <Button title="Checkout" />}` | Component rendering logic |
| 6 | `dispatch({ type: "cart/itemRemoved" })` | Component dispatching an action |
| 7 | `selectCheckoutMessage(state)` | Selector |

---

## Task F Sample Answer

```text
Redux is useful because the cart is shared state needed by multiple parts of the app.
Instead of each component updating cart information separately, components dispatch actions.
The reducer applies predictable rules to create the next state.
Selectors let components read useful information from the store.
This makes the cart flow easier to debug and less scattered.
```

---

## Missing Chain Table Answers

| Scenario | Data | State | Information | Decision | Action |
|---|---|---|---|---|---|
| Cart has 3 items | `cartCount = 3` | `state.cartCount = 3` | Cart is not empty | Enable checkout | Show checkout button |
| User is logged out | `isLoggedIn = false` | `state.isLoggedIn = false` | User cannot access protected page | Redirect to login | Navigate to login screen |
| Form email is empty | `email = ""` | `state.email = ""` | Email is missing | Block form submission | Show validation error |
| Stock is 0 | `stockLevel = 0` | `state.stockLevel = 0` | Product unavailable | Disable purchase | Show out-of-stock message |
| Player health is 20 | `health = 20` | `state.health = 20` | Health is low | Use warning state | Show red health warning |

---

## Debugging Answers

### Bug A

```text
The code tries to reassign cartCount directly.
React state must be changed through the setter function.
React will not reliably know state changed if the setter is not called.
Use setCartCount(cartCount + 1).
```

### Bug B

```text
hasItems is derived from cartCount.
If it is stored separately, it can become stale.
It should be calculated as const hasItems = cartCount > 0.
```

### Bug C

```text
The reducer mutates the existing state object.
Classic Redux expects reducers to return a new state object.
The correct version returns { ...state, cartCount: state.cartCount + 1 }.
```

### Bug D

```text
If many components need hasItems or checkoutMessage, repeating the logic creates duplication.
A selector can turn state into useful information once.
selectHasItems and selectCheckoutMessage make the UI simpler and more consistent.
```

---

# 🚀 Final Compression

```text
Data = raw value
State = present-tense data inside the system
Information = what state means for action
Decision = what the system should do
Action = what happens in the UI
React = component state and re-rendering from current state
Redux = application state with named actions, reducers, and selectors
Selector = turns state into useful information for UI
Reducer = turns current state + action into next state
```

---

# 🌌 Ultimate Compression

```text
State is the present tense of data.

Information is what that present state means for action.
```

React shows the consequence of current state.

Redux makes the changes to shared state sequential, predictable, and rule-based.
