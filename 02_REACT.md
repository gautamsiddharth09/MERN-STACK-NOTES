# ==========================================================
#                  ⚛️ REACT — PART 3
# ==========================================================

# 1. What are the two main use cases of `useRef`? Why doesn't changing a ref trigger a re-render?

useRef is mainly used for accessing DOM elements and storing mutable values across renders. Updating `ref.current` doesn't trigger a re-render because React doesn't track ref mutations; it only re-renders when state, props, or context values change.

## 1. Accessing DOM Elements Directly

It allows us to get a reference to a DOM element and perform operations such as:

- Focusing an input
- Scrolling to an element
- Playing/pausing a video
- Measuring element dimensions

```jsx
const inputRef = useRef();

const focusInput = () => {
  inputRef.current.focus();
};

<input ref={inputRef} />;
```

## 2. Storing Mutable Values That Persist Across Renders

useRef can store values that need to survive re-renders without causing additional renders.

Examples:

- Previous state value
- Timer IDs
- API call status flags
- Render count

```jsx
const renderCount = useRef(0);

useEffect(() => {
  renderCount.current += 1;
});
```

## Why Doesn't Changing a Ref Trigger a Re-render?

React only re-renders when:

- State changes (`useState`)
- Props change
- Context updates

A ref is just a mutable JavaScript object:

```jsx
const ref = useRef(0);

// React creates

{
  current: 0;
}
```

When we do:

```jsx
ref.current = 10;
```

we are simply mutating the object's property. The object reference itself remains the same.

React does not track changes to `ref.current`, so it has no reason to re-render the component.

## 3.Working with Uncontrolled Components

useRef is useful for uncontrolled components because it lets us access the current value of an input without storing it in React state.

```jsx
function LoginForm() {
  const inputRef = useRef();

  const handleSubmit = () => {
    console.log(inputRef.current.value);
  };

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleSubmit}>Submit</button>
    </>
  );
}
```

---

# 2. Difference between `useEffect` and `useLayoutEffect`. When do you actually need `useLayoutEffect`?

useEffect runs asynchronously after the browser has painted the UI, making it suitable for most side effects such as API calls and subscriptions. useLayoutEffect runs synchronously after DOM mutations but before paint, allowing us to measure or modify layout without visible flicker. Because it blocks painting, I use useLayoutEffect only when layout calculations or DOM measurements are required before the user sees the screen.

Both useEffect and useLayoutEffect are used for side effects, but the key difference is when they run in the rendering process.

## useEffect

- Runs after the browser paints the screen
- Non-blocking
- Preferred for most side effects

```jsx
useEffect(() => {
  document.title = "Dashboard";
}, []);
```

## useLayoutEffect

- Runs before the browser paints the screen
- Blocks painting until it finishes
- Use only when you need to read or modify layout before the user sees it

```jsx
const divRef = useRef();

useLayoutEffect(() => {
  console.log(divRef.current.offsetWidth);
}, []);
```

```text
Render Component
      ↓
DOM Updated
      ↓
useLayoutEffect
      ↓
Browser Paint
      ↓
useEffect
```

---

# 3.What is `useId` and what problem does it solve in SSR apps?

useId is a React Hook that generates unique, stable IDs that work correctly across both the server and the client.

It was introduced to solve hydration mismatch issues in Server-Side Rendered (SSR) applications.

## Problem Without useId

Suppose we generate IDs like this:

```jsx
const id = Math.random();
```

or

```jsx
const id = Date.now();
```

During SSR:

- Server Render → id = 123
- Client Render → id = 456

The generated IDs don't match, causing a hydration mismatch warning.

## Solution with useId

```jsx
import { useId } from "react";

function LoginForm() {
  const id = useId();

  return (
    <>
      <label htmlFor={id}>Email</label>
      <input id={id} />
    </>
  );
}
```

---

# 4. Quick overview of React 18's `useTransition` and `useDeferredValue`.

Both useTransition and useDeferredValue were introduced in React 18 to improve UI responsiveness by prioritizing urgent updates over non-urgent ones.

## 1. useTransition

useTransition lets us mark certain state updates as non-urgent.

### Example

Imagine a search page:

```jsx
const [input, setInput] = useState("");
const [results, setResults] = useState([]);
const [isPending, startTransition] = useTransition();

const handleChange = (e) => {
  setInput(e.target.value); // urgent

  startTransition(() => {
    setResults(filterData(e.target.value)); // non-urgent
  });
};
```

### Real Use Cases

- Search results
- Large tables
- Data filtering
- Complex list rendering
- Expensive UI updates

## 2. useDeferredValue

useDeferredValue lets a value "lag behind" the latest value.

### Example

```jsx
const [query, setQuery] = useState("");
const deferredQuery = useDeferredValue(query);

const filteredItems = useMemo(() => {
  return filterItems(deferredQuery);
}, [deferredQuery]);
```

Here:

- query updates immediately
- deferredQuery updates later when React has time

This keeps typing smooth even if filtering is expensive.

## Difference

### useTransition

You decide which update is low priority.

```jsx
startTransition(() => {
  setResults(data);
});
```

### useDeferredValue

React creates a low-priority copy of a value.

```jsx
const deferredValue = useDeferredValue(value);
```

useTransition controls React's rendering priority.

```jsx
startTransition(() => {
  setResults(bigList);
});
```

It says:

- Run it now,
- but if React is busy,
- give priority to user typing first.

No fixed delay.

### Real Example

Suppose you already have 50,000 products in memory.

```jsx
const filteredProducts = products.filter(...);
```

Every keystroke filters 50,000 items.

There is no API call, so debounce may not even be needed.

The problem is:

```text
Typing
↓
Huge list re-renders
↓
Input becomes laggy
```

Use:

```jsx
startTransition(() => {
  setFilteredProducts(...);
});
```

Now:

- Typing stays smooth
- Results update slightly later

---

# 5. What are the rules of hooks, and why must hooks be called at the top level of a component (not inside conditions/loops)?

## Rules of Hooks:

- Hooks must be called at the top level of a component or custom hook.
- Hooks can only be called inside React components or custom hooks.

## Why not inside conditions or loops ?

React tracks hooks by their call order. If a hook is called conditionally or inside a loop, the order can change between renders, causing React to associate the wrong state or effect with a hook and leading to errors.

---

# 1.What is the Virtual DOM and how is it different from the real DOM?

Virtual DOM is a lightweight copy of the real DOM. React uses it to detect changes and update only the affected parts of the real DOM, making updates faster and more efficient.

## 🧠 How it works (simple explanation)

When the state of a component changes:

- React creates a new Virtual DOM
- It compares it with the previous Virtual DOM (this process is called diffing)
- It identifies only the changed parts
- Then it updates only those parts in the real DOM (this is called reconciliation)

This avoids updating the whole page.

---

# 2.Explain the reconciliation algorithm — how does React decide what to update?

Reconciliation is the process React uses to compare the previous Virtual DOM with the new Virtual DOM and determine the minimal set of changes needed to update the real DOM efficiently.

In simple terms:

👉 React figures out what changed and updates only that part in the UI.

## How React decides what to update (step-by-step)

1. 🧾 New Virtual DOM is created
2. ⚖️ Diffing process (comparison)

React compares:

- Old Virtual DOM
- New Virtual DOM

3. 🔍 Key rules React uses in reconciliation

If the type changes:

👉 React destroys old DOM and creates a new one

---

# 3. Why is the `key` prop important in lists? What goes wrong if you use the array index as the key when items are reordered or deleted?

The key prop in React is a unique identifier used to help React identify which items in a list have changed, been added, or removed. It helps React efficiently update the UI during re-rendering.

## 🧠 Why key is important

When React renders a list, it uses key to:

- Track each element uniquely
- Decide what changed between renders
- Reuse existing DOM elements instead of recreating them
- Optimize reconciliation (diffing process)

Without proper keys, React may re-render unnecessarily or incorrectly map components.

---

# 4.What is React Fiber, and why was it introduced?

React Fiber is the re-implementation of React’s reconciliation algorithm introduced in React 16. It is a new architecture that breaks rendering work into small units (fibers) so React can pause, prioritize, and resume rendering work.

Fiber makes React rendering incremental, interruptible, and more controllable.

## 🧠 Why React Fiber was introduced

Before Fiber (old React stack reconciler):

### ❌ Problems:

#### 1. Blocking rendering

- Entire UI update happened in one go
- Could freeze the browser for large updates

#### 2. No prioritization

All updates treated equally (even small input updates vs big page render)

#### 3. Poor user experience

Slow UI, jank, unresponsive apps during heavy rendering

## 🚀 What Fiber changed

Fiber introduced a new way of rendering:

### 1. Work is split into units

Instead of rendering whole tree at once:

React breaks work into small chunks called "fibers"

### 2. ⏸️ Interruptible rendering

- Pause rendering work
- Work on more important updates
- Resume later

### 3. 🎯 Prioritization (Lane system)

React assigns priority levels:

- High priority → typing, clicks
- Low priority → background updates

## ⚙️ How Fiber works internally (simple flow)

- State update happens
- React creates a Fiber tree
- Work is scheduled
- React processes fibers in chunks
- Can pause/resume based on priority
- Finally commits changes to real DOM

---

# 5.What causes a component to re-render? Name all triggers.

A React component re-renders when React needs to re-execute the component function to reflect updated state, props, or context in the UI.

Re-render ≠ DOM update (important point).

React may re-render without changing the real DOM (due to diffing).

## 🔥 Re-render Triggers in React

- State change (`setState`, `useState`)
- Props change
- Context value change (`useContext`)
- Parent component re-render
- Key change (in lists / components)
- Force update (`forceUpdate` in class components / hacks in function components)
- External store update (Redux, Zustand, MobX, etc.)
- Hook-triggered state update (inside `useEffect`, event handlers, callbacks)
- Route change (in React Router, when component remounts/re-renders due to navigation)

---

# 6. What does `<React.StrictMode>` do, and why does it sometimes call your component twice in dev?

`<React.StrictMode>` is a development-only wrapper in React that helps detect potential problems in an application by intentionally adding extra checks and warnings for unsafe or legacy patterns.

👉 It does NOT affect production build.

## 🧠 What it actually does

StrictMode enables additional checks for:

- Unsafe lifecycle methods
- Side effects in rendering
- Deprecated APIs
- Unexpected mutations
- Improper use of hooks

## ⚠️ Why components run twice in dev (important point)

In React 18+, StrictMode intentionally:

👉 What actually happens:

🔁 Double-invokes certain functions in development

In StrictMode (development only) React:

- runs extra checks
- re-runs the render again
- to detect side effects / unsafe code

---

# 7.Difference between controlled and uncontrolled components — give an example of each. ?

## 👉 Controlled Component

A controlled component is a form element whose value is controlled by React state. React is the single source of truth.

## 👉 Uncontrolled Component

An uncontrolled component stores its own state in the DOM itself, and we access the value using a ref instead of React state.

### ⚛️ Uncontrolled Component Example

```jsx
import { useRef } from "react";

function UncontrolledInput() {
  const inputRef = useRef();

  const handleSubmit = () => {
    alert(inputRef.current.value);
  };

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleSubmit}>Submit</button>
    </>
  );
}
```

---

# 8.What is `React.memo` and when does it actually prevent re-renders (and when doesn't it)?

React.memo is a higher-order component (HOC) used to prevent unnecessary re-renders of functional components by memoizing the rendered output.

👉 It does a shallow comparison of props and reuses the previous render result if props haven’t changed.

## 🧠 How it works (simple)

When a parent re-renders:

- React normally re-renders child components
- But with React.memo:
  - 👉 React checks props
  - 👉 If props are same → skip re-render
  - 👉 If props changed → re-render

```jsx
const Child = React.memo(function Child({ name }) {
  console.log("Child rendered");
  return <div>{name}</div>;
});

function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>Click</button>
      <Child name="Rahul" />
    </>
  );
}
```

👉 Result:

- Clicking button → Parent re-renders
- Child does NOT re-render (because props didn’t change)

## ❌ When React.memo does NOT prevent re-render

### 1. Props change

```jsx
<Child name={count} />
```

### 2. New object / array reference (VERY IMPORTANT)

```jsx
<Child user={{ name: "Rahul" }} />
```

👉 Even if values are same, this is a new object → re-render happens

### 3. Functions passed as props (without useCallback)

```jsx
<Child onClick={() => console.log("hi")} />
```

👉 New function every render → re-render

### 4. Context updates

React.memo does NOT block re-render caused by useContext

### 5. State changes inside the component

If child has its own state → it re-renders anyway

---

# 9.How does React handle events? What are synthetic events and event delegation in React?

## How React handles events

React does NOT attach events to every element.

It uses one global system (event delegation).

When event happens → React finds the target component and runs handler.

## 1. What is a Synthetic Event? (simple meaning)

A Synthetic Event is React’s cross-browser wrapper around native events.

👉 In normal JavaScript, browser events are different in different browsers.

Example:

Chrome event behavior ≠ Firefox event behavior

So React creates its own wrapper event:

👉 SyntheticEvent = React’s “standard event object”

- ✔ Same behavior in all browsers
- ✔ Improves performance
- ✔ Same properties (`target`, `preventDefault`)
- ✔ Easier for React to manage

```jsx
function App() {
  const handleClick = (e) => {
    console.log(e); // SyntheticEvent
    console.log(e.nativeEvent); // actual DOM event
  };

  return <button onClick={handleClick}>Click</button>;
}
```

👉 Here `e` is NOT raw browser event.

👉 It is SyntheticEvent (React version of event).

# React Event Flow

```text
User Clicks Button
        │
        ▼
Browser Creates Native Event
(Browser Event)
        │
        ▼
React Receives the Native Event
        │
        ▼
React Creates a SyntheticEvent
(React Event Wrapper)
        │
        ▼
React Calls Your `onClick` Function
```

## Explanation

### 1. User Clicks Button
The user clicks a button on the webpage.

### 2. Browser Creates Native Event
The browser creates a **Native Event** (such as `MouseEvent`).

- Created by the browser
- Different browsers may implement events slightly differently

### 3. React Receives the Event
React listens for browser events using **event delegation** and receives the native event.

### 4. React Creates a SyntheticEvent
React wraps the native event inside a **SyntheticEvent**.

A **SyntheticEvent** is:
- A React event object
- A wrapper around the browser's native event
- Provides the same API across all browsers
- Makes event handling consistent

### 5. React Calls Your `onClick` Function
Finally, React passes the `SyntheticEvent` to your event handler.

Example:

```jsx
function App() {
  const handleClick = (event) => {
    console.log(event); // SyntheticEvent
  };

  return <button onClick={handleClick}>Click Me</button>;
}
```

## One-Line Memory Trick

> **Browser creates the event → React wraps it → Your event handler receives the wrapper.**

## 🧠 What is Event Delegation in React?

Event delegation means:

👉 Instead of attaching event listeners to each element, React attaches one listener at the root.

Then it uses event bubbling to:

- Detect which element triggered the event
- Bubble up it child to parent
- Route it to correct handler

---

# 10.Explain React 18's automatic batching, concurrent rendering, and transitions in plain English.

## ⚛️ 1. Automatic Batching (React 18)

### ✅ Meaning

React groups multiple state updates together and re-renders only once instead of re-rendering for each update.

```jsx
setCount((c) => c + 1);
setName("Rahul");
setFlag(true);
```

👉 “React waits a little, combines updates, then renders once.”

Before React 18:

👉 Might re-render 3 times

React 18:

👉 Only 1 re-render

## ⚛️ 2. Concurrent Rendering

React can start rendering UI, pause it, and resume later depending on priority.

Concurrent rendering allows React to pause, prioritize, and resume rendering work so the UI stays responsive.

## ⚛️ 3. Transitions (`startTransition`)

Transitions let you mark non-urgent updates so React can delay them.

```jsx
startTransition(() => {
  setSearchResults(heavyList);
});
```

👉 “This update is not urgent, do it later.”

### ⚡ Real-world use case

Urgent:

- Typing in search box

Non-urgent:

- Filtering big list results

👉 React prioritizes typing first

👉 List updates happen later

---

# 11.Why must hooks always run in the same order? What does React internally use to track them?

React Hooks must be called in the same order on every render because React relies on the call order to track Hook state internally, not on Hook names or variables.

If the order changes between renders, React will assign the wrong state to the wrong Hook, leading to bugs or crashes.

## ⚙️ How React tracks Hooks internally

React stores Hooks inside something called a Fiber node as a linked list.

React uses:

### 1. Fiber Node

Each component has a Fiber object that stores its Hook data.

### 2. Linked List of Hooks

Inside the Fiber, Hooks are stored as a linked list:

```text
Hook1 → Hook2 → Hook3 → null
```

Each Hook node contains:

- state (`memoizedState`)
- pointer to next Hook (`next`)

## 🔁 How rendering works

During render, React:

- Starts from the first Hook
- Moves sequentially through the linked list
- Matches Hooks based on position (order)

So:

- 1st Hook call → first stored Hook
- 2nd Hook call → second stored Hook
- 3rd Hook call → third stored Hook



# ==========================================================
#                  ⚛️ REDUX PART
# ==========================================================


## 1. What is Redux and what problem does it solve?

Redux is a predictable state management library commonly used with React to manage and centralize application state.

### What problem does Redux solve?

In a React app, components manage their own state. As the app grows, this leads to:--

- Prop drilling: passing data through many layers of components unnecessarily
- Unpredictable state updates: multiple components updating shared state in different ways
- Hard debugging: it becomes difficult to track where and why state changed
- Tight coupling between components

Redux solves this by introducing a single source of truth.

### How Redux works (core idea)

Redux stores all shared state in a single object called the store.

Data flow is strict and predictable:

- Action → describes what happened
- Reducer → decides how state changes
- Store → holds the updated state
- UI subscribes → React components read state from store

👉 Data flow is one-way (unidirectional)

---

## 2. Explain the three principles of Redux (single source of truth, state is read-only, changes via pure reducers).

### 1. Single Source of Truth

Redux stores the entire application state in one object called the store.

**What it means:**

- There is only one central state tree
- Any component that needs data reads it from this store
- No duplicated or scattered state across components

### 2. State is Read-Only

You cannot directly modify the state object in Redux.

**What it means:**

- You never do: `store.state = something`
- State can only be changed by dispatching actions

### 3. Changes are Made via Pure Reducers

A reducer is a pure function that takes current state + action and returns a new state.

---

## 3. Define: store, action, reducer, dispatch, selector.

In Redux, the store holds the entire application state. Actions describe what happened, reducers define how the state changes, dispatch is used to send actions to the store, and selectors are functions used to read specific data from the store. Together, they create a predictable and structured state management flow.

### 1. Store

The store is the central place that holds the entire application state.

### 2. Action

An action is a plain JavaScript object that describes what happened.

**Structure:**

```js
{
  type: "INCREMENT",
  payload: 1
}
```

**What it does:**

- It is the only way to send data to the store
- It tells Redux what change is needed
- Must have a type property

### 3. Reducer

A reducer is a pure function that decides how the state changes based on an action.

**Structure:**

```js
(state, action) => newState
```

```js
function counterReducer(state = 0, action) {
  switch (action.type) {
    case "INCREMENT":
      return state + 1;
    default:
      return state;
  }
}
```

### 4. Dispatch

Dispatch is the method used to send an action to the store.

**What it does:**

- Triggers state update process
- Sends action to reducer

**Example:**

```js
dispatch({ type: "INCREMENT" });
```

### 5. Selector

A selector is a function used to extract specific data from the Redux store.

**What it does:**

- Reads state from store
- Returns only required slice of data
- Helps avoid unnecessary re-renders

---

## 4. What is middleware in Redux? What does `redux-thunk` do?

Middleware in Redux is a function that intercepts actions between dispatch and reducer to handle side effects like logging or async operations. Redux Thunk is a middleware that allows action creators to return functions instead of objects, enabling asynchronous logic like API calls before dispatching the final action.

```text
dispatch → reducer → store update
dispatch → middleware → reducer → store update
```

### What does redux-thunk do?

redux-thunk is a middleware that allows you to write asynchronous logic inside Redux actions.

Normally Redux only accepts plain objects as actions:

```js
dispatch({ type: "INCREMENT" });
```

But with redux-thunk, you can dispatch a function instead of an object.

```js
export const fetchUsers = () => {
  return async (dispatch) => {
    dispatch({ type: "FETCH_USERS_LOADING" });

    const res = await fetch("/api/users");
    const data = await res.json();

    dispatch({
      type: "FETCH_USERS_SUCCESS",
      payload: data,
    });
  };
};
```

---

## 5. Why must reducers be pure functions?

Reducers must be pure functions because Redux depends on predictable and consistent state updates. Pure reducers ensure the same input always produces the same output without side effects, which enables features like time-travel debugging, reliable testing, and easier debugging while maintaining immutable state updates.

### What is a pure function (in this context)?

A function is pure if:

- Same input → always same output
- No side effects
- Does not modify external stat

**Input**

- old state = 2
- action = INCREMENT

output--always same , it will not afect

Reducer returns:

```text
3
```

Always same result.

### ✅ Why Redux needs pure reducers

Because Redux wants:

- Same action → same result
- Easy debugging
- Easy testing
- Predictable app behavior

---

## 6. What is Redux Toolkit and which problems of plain Redux does it solve?

Redux Toolkit is the official way to write Redux. It solves problems of plain Redux like excessive boilerplate, complex store setup, and manual immutable updates. It provides utilities like createSlice, configureStore, and createAsyncThunk which make Redux code shorter, easier to write, and less error-prone while following best practices by default.

### What is Redux Toolkit (RTK)?

Redux Toolkit is the official, recommended way to write Redux logic.

It is basically a simplified and modern version of Redux that reduces boilerplate and makes Redux easier to use.

### ❌ Problems with plain Redux

Before Redux Toolkit, Redux had a few pain points:

1. Too much boilerplate code
2. Complex store setup
3. Hard to manage immutable updates
4. No built-in best practices

### ✅ How Redux Toolkit solves these problems

Redux Toolkit simplifies everything:

#### 1. configureStore() (easy store setup)

```js
const store = configureStore({
  reducer: counterReducer,
});
```

#### 2. createSlice() (removes boilerplate)

This replaces:

- actions
- action creators
- reducer switch cases

#### 3. Easier immutable updates (Immer built-in)

You can write:

```js
state.value += 1;
```

#### 4. Built-in async handling (createAsyncThunk)

Instead of writing thunk manually:

```js
createAsyncThunk("users/fetch", async () => {
  const res = await fetch("/api/users");
  return res.json();
});
```

✔ handles loading / success / error automatically

---

## 7. What is `createSlice`? How does it leverage Immer to allow 'mutating' code?

createSlice is a Redux Toolkit function that simplifies Redux logic by combining reducers, actions, and initial state in one place. It uses Immer internally, which allows developers to write mutable-looking code like state.value += 1, but safely converts it into immutable updates behind the scenes, ensuring state integrity while improving readability and reducing boilerplate.

### What is createSlice?

createSlice is a function from Redux Toolkit that lets you define:

- initial state
- reducers (logic)
- actions automatically

all in one place.

👉 It removes the need to write separate action types, action creators, and switch-case reducers.

### What is Immer?

Immer is a library used inside Redux Toolkit that:

lets you write “mutating logic” but produces a new immutable state behind the scenes

### How it works internally

When you write:

```js
state.value += 1;
```

Immer:

- Creates a draft copy of state
- Tracks all changes you make
- Produces a new immutable state object
- Keeps original state unchanged

---

## 8. What is `createAsyncThunk` and how does it model `pending` / `fulfilled` / `rejected` states?

createAsyncThunk is a Redux Toolkit utility used to handle asynchronous operations like API calls. It automatically generates three lifecycle actions: pending when the request starts, fulfilled when it succeeds, and rejected when it fails. These states help manage loading, success, and error handling in Redux in a clean and standardized way without writing manual action types and reducers.

### What is createAsyncThunk?

createAsyncThunk is a helper function from Redux Toolkit used to handle asynchronous logic (like API calls) in Redux.

👉 It automatically generates Redux actions for async operations.

### Why do we need it?

In Redux, async operations (like fetching data) are not allowed inside reducers.

So earlier we had to manually write:

- loading state
- success action
- error action
- multiple action types

createAsyncThunk removes this boilerplate.

```js
import { createAsyncThunk } from "@reduxjs/toolkit";

export const fetchUsers = createAsyncThunk(
  "users/fetchUsers",
  async () => {
    const res = await fetch("https://api.example.com/users");
    return res.json();
  }
);
```

### 🔥 What it automatically creates

For every async thunk, Redux Toolkit automatically generates 3 action types:

#### 1. pending (request started)

Dispatched when API call starts

**Use case:**

- show loading spinner
- set loading = true

```js
builder.addCase(fetchUsers.pending, (state) => {
  state.loading = true;
});
```

#### 2. fulfilled (success)

Dispatched when API call succeeds

**Use case:**

- store data
- stop loading

```js
builder.addCase(fetchUsers.fulfilled, (state, action) => {
  state.loading = false;
  state.users = action.payload;
});
```

#### 3. rejected (error)

Dispatched when API call fails

**Use case:**

- store error
- stop loading

```js
builder.addCase(fetchUsers.rejected, (state, action) => {
  state.loading = false;
  state.error = action.error.message;
});
```

---

## 9. What is RTK Query and when would you use it over `createAsyncThunk`?

RTK Query is a powerful data-fetching and caching tool in Redux Toolkit that automatically manages API calls, loading states, caching, and refetching. It reduces boilerplate significantly compared to createAsyncThunk. We prefer RTK Query for standard API interactions, while createAsyncThunk is used when we need more control over complex async logic or custom state handling.

RTK Query is a data-fetching and caching solution built into Redux Toolkit. It’s designed to handle API calls in a much more automated and optimized way compared to manually writing thunks and reducers.

### What is RTK Query?

RTK Query is a tool that lets you:

- Fetch data from APIs
- Cache responses automatically
- Track loading, success, and error states
- Refetch data when needed
- Avoid writing reducers and boilerplate code

👉 In simple terms:

RTK Query is Redux Toolkit’s built-in solution for managing server state (API data) efficiently.

---

## 10. When should you choose Redux Toolkit over Context API?

I would use Context API for simple and lightweight global state like theme, language, or authentication flags where updates are infrequent. However, for complex applications with large-scale state, frequent updates, or async data handling like API calls, I would choose Redux Toolkit because it provides better structure, scalability, middleware support, and powerful tools like RTK Query and DevTools.

