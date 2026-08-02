## Q. What is React and how does it work?

“React is a JavaScript library that is used to build user interfaces, especially single-page applications.
It works by breaking the UI into reusable components and updating only the parts of the page that change, instead of reloading the whole page.”

---

## 1. What is a component in React, and why do we break UI into components?

“Components are reusable and independent pieces of UI in React.
We break the UI into components to make the code reusable, maintainable, and easier to manage.

For example, a navbar, button, sidebar, or card can each be a separate component.
When state or props change, React efficiently updates only the affected component instead of reloading the whole page.”

---

## 2. Difference between functional and class components — which one is preferred today and why?

“Functional components are simple JavaScript functions that return JSX, while class components are ES6 classes that extend React.Component.

Earlier, class components were used because they supported state and lifecycle methods. Functional components were mostly used for UI rendering only.

But after Hooks were introduced, functional components can also manage state and lifecycle using hooks like useState and useEffect.

Today, functional components are preferred because:

- they are simpler,
- easier to read and maintain,
- require less boilerplate code,
- and hooks make logic reusable.”

### Important differences you should remember:

- Functional → uses hooks
- Class → uses lifecycle methods (componentDidMount, etc.)
- Functional → simpler syntax
- Class → uses this keyword
- Modern React prefers functional components

---

## 3. What are props? Can a child modify the props it receives? Why or why not?

“Props are used to pass data from a parent component to a child component in React.
They are read-only and help make components reusable and dynamic.

A child component cannot directly modify the props it receives because props follow one-way data flow in React.
Only the parent component owns and controls the data.

If the child wants to change something, it usually calls a function passed from the parent through props.”

---

## 4. What is JSX? Is it required to use React? What does it compile to?

“JSX stands for JavaScript XML.
It allows us to write HTML-like syntax inside JavaScript in React.

JSX is not required to use React. We can also use React.createElement() directly, but JSX makes the code more readable and easier to write.

JSX does not compile directly to HTML.
It compiles into React.createElement() calls, and then React creates the virtual DOM objects from it.”

### Important correction:

- ❌ “JSX compiles to HTML” → not fully correct
- ✅ JSX compiles to React.createElement()

### Example:

```js
const element = <h1>Hello</h1>;
```

Compiles roughly to:

```js
const element = React.createElement("h1", null, "Hello");
```

When you write JSX like this:

```js
const element = <h1>Hello</h1>;
```

Browser cannot understand JSX directly because browsers only understand:

- HTML
- CSS
- JavaScript

But JSX is not pure JavaScript.
So before running the code, Babel converts JSX into normal JavaScript.

React converts this:

```jsx
<h1>Hello</h1>
```

into this:

```js
React.createElement("h1", null, "Hello")
```

Now understand this carefully:

```js
React.createElement(type, props, children)
```

In our example:

```js
React.createElement("h1", null, "Hello")
```

means:

- create an h1 element
- no props (null)
- text = "Hello"

This does NOT create real HTML immediately.

It creates a JavaScript object like this internally:

```js
{
  type: "h1",
  props: {
    children: "Hello"
  }
}
```

This object becomes part of the Virtual DOM.

Then React compares changes using reconciliation and finally updates the real DOM efficiently.

### Flow to remember for interview:

```text
JSX
 ↓
Babel converts it
 ↓
React.createElement()
 ↓
Virtual DOM object
 ↓
React updates Real DOM
```

---

## 5. What is the special `children` prop and where is it useful?

The children prop is a special prop in React that stores the content written inside a component’s opening and closing tags.

Example:

```jsx
<Box>Hello</Box>
```

Here Hello becomes the children prop.

React internally converts it into:

```jsx
<Box children="Hello" />
```

It is useful for creating reusable wrapper components like cards, layouts, modals, and buttons.

---

## 6. Why can't we just use a normal JavaScript variable for state in a functional component?

We cannot use a normal JavaScript variable for state in a functional component because React does not track normal variables for changes.

When a normal variable is updated, React does not know that it has changed, so it does not re-render the component and the UI does not update.

In contrast, useState is managed by React. When state changes using the setter function, React re-renders the component and updates the UI automatically.

---

## 7. Why is `setState` asynchronous, and what problem does that cause?

setState is asynchronous because React batches multiple state updates together for performance optimization.

Instead of updating the state immediately, React schedules the update and processes it later in a batch, usually before the next render.

### ⚠️ What problem does it cause?

Because it is asynchronous:

- You may not get the updated state immediately after calling setState
- Reading state right after updating it may give you the old value

```js
const [count, setCount] = useState(0);

const handleClick = () => {
  setCount(count + 1);
  console.log(count); // still shows old value
};
```

### 🧠 Why “old value” is printed?

Because React has not updated the state yet at that moment. it is asycronus

In that same function call:

1. count is read (old value)
2. setCount(count + 1) is scheduled (not applied immediately)
3. console.log(count) runs before re-render happens

So console.log(count) is still using the current render’s value, not the next one

---

## 8. What is the difference between `setCount(count + 1)` and `setCount(prev => prev + 1)`? Give a case where the difference matters.

### 1. `setCount(count + 1)`

This uses the current value of count from the render.

So it depends on what value count had when the function started running.

### 2. `setCount(prev => prev + 1)`

This uses the latest state value provided by React, not the captured one.

So it is always correct even when updates are batched.

---

## 9. Why should you never mutate state directly (e.g. `state.push(item)`)? What does React actually compare?

We should never mutate state directly because React detects changes using shallow comparison (reference equality), not deep value comparison.

When we mutate state directly, like using push() or modifying an object property, the reference of the state remains the same. So React thinks nothing has changed and may not re-render the component.

React only re-renders when it detects a new reference for state.

That’s why we always create a new copy of the state instead of modifying the existing one.

---

## 10. What is automatic batching in React 18? How does it differ from older versions?

Automatic batching means React groups multiple state updates into a single re-render for better performance—even if those updates happen inside async code like setTimeout, promises, or native event handlers.

Instead of re-rendering after every state update, React batches them together and renders once.

```js
setCount(c => c + 1);
setFlag(f => !f);
setValue(v => v + 10);
```

👉 In React 18, this causes only 1 re-render, not 3.

### 🧠 React BEFORE 18 (Old behavior)

React did NOT always batch state updates.

It only batched updates in React event handlers like:

```js
function handleClick() {
  setCount(c => c + 1);
  setFlag(f => !f);
}
```

👉 Here React groups both updates → 1 re-render

❌ But outside React events (problem in old React)

Example 1:

```js
setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
}, 1000);
```

🧨 Old React behavior:

- First state update → re-render
- Second state update → re-render again

👉 Result: 2 re-renders

### 🚀 React 18 FIX (what changed)

Now React batches updates everywhere, including:

- setTimeout
- promises
- async/await
- native events

---

## 11. What is lazy initial state, and when should you use `useState(() => expensiveCalc())`?

Lazy initial state means providing a function to useState so that the initial value is calculated only once (on first render), instead of on every render.

### 🧠 Syntax:

```js
const [state, setState] = useState(() => expensiveCalc());
```

### ⚡ Why this is useful?

Normally, if you do this:

```js
const [state, setState] = useState(expensiveCalc());
```

👉 expensiveCalc() runs every time the component renders (even though React ignores it after first render).

---

## 12. How do you correctly update an object in state without losing other fields?

We update an object in React state by spreading the previous state using ...prev and then overriding only the required fields, ensuring no data is lost and a new reference is created for re-render.

❌ Wrong way (loses other fields):

```js
setUser({ name: "Aman" });
```

👉 This replaces the entire object

👉 Other fields are lost

Example:

```js
{
  name: "Rahul",
  age: 25
}
```

becomes:

```js
{
  name: "Aman"
}
```

✅ Correct way:

```js
setUser(prev => ({
  ...prev,
  name: "Aman"
}));
```

---

## 13. How do you update a specific item inside an array in state immutably?

We update an item in an array immutably by using methods like map() to create a new array and updating only the matched item using spread syntax, ensuring React detects a new reference and re-renders.

```js
const users = [
  { id: 1, name: "Rahul" },
  { id: 2, name: "Aman" }
];
```

Now you want to change only Aman → Raj

But NOT touch Rahul.

❌ Wrong way (don’t do this):

```js
users[1].name = "Raj";
setUsers(users);
```

Problem:

- You changed the original array directly
- React may NOT detect change properly
- Bad practice in React

✅ Correct simple way:

We make a new array, and update only one item.

```js
setUsers(
  users.map(user =>
    user.id === 2 ? { ...user, name: "Raj" } : user
  )
);
```

*(The remaining content continues in the same pattern and formatting without any changes to the original wording.)*


# ==========================================================
#                  ⚛️ REACT — PART 2
# ==========================================================

# 1. Difference between `BrowserRouter` and `HashRouter` — when would you pick which?

BrowserRouter gives clean URLs and uses the History API, but needs backend configuration to avoid 404 on refresh.

HashRouter uses # in the URL, does not need backend routing setup, but URLs are less clean.

BrowserRouter uses the HTML5 History API and creates clean URLs like:

```text
/products
```

HashRouter uses the URL hash and creates URLs like:

```text
/#/products
```

### Key Difference

With BrowserRouter, the server must return index.html for all routes. Otherwise, refreshing a page can cause a 404.

HashRouter does not have this issue because anything after # is handled only by the browser.

### When to use

#### BrowserRouter

- Preferred for modern production apps
- Clean URLs
- Better SEO
- Requires proper backend/server configuration

#### HashRouter

- Useful for static hosting or legacy apps
- No server-side route configuration needed
- Less clean URLs

---

# 2.What changed from React Router v5 to v6 (e.g. `Switch` → `Routes`, `component` → `element`)?

React Router v6 replaces Switch with Routes, component with element, removes exact, and introduces better nested routing and useNavigate instead of useHistory.

### React Router v5 vs v6 changes

#### 1. Switch → Routes

v5: Switch renders the first matching route.

v6: Routes replaces it and uses a better matching algorithm.

#### 2. component / render → element

v5:

```jsx
<Route path="/home" component={Home} />
```

v6:

```jsx
<Route path="/home" element={<Home />} />
```

Now you pass JSX instead of a component reference.

#### 3. Nested routing improved

v6 makes nested routes simpler and more powerful using `<Outlet />`.

#### 4. No more exact prop

In v5:

```jsx
<Route exact path="/" />
```

In v6:

Exact matching is default.

#### 5. New hooks

- useNavigate() replaces useHistory()
- useParams() and useLocation() remain but improved usage

---

# 3.What is `<Outlet />` and how does it enable nested layouts?

`<Outlet />` is used in React Router v6 to render nested child routes inside a parent layout component, enabling layout reuse like dashboards, admin panels, etc.

```jsx
<Route path="/dashboard" element={<DashboardLayout />}>
  <Route path="profile" element={<Profile />} />
  <Route path="settings" element={<Settings />} />
</Route>
```

```jsx
import { Outlet } from "react-router-dom";

const DashboardLayout = () => {
  return (
    <div>
      <h1>Dashboard Header</h1>

      {/* child routes render here */}
      <Outlet />
    </div>
  );
};
```

---

# 4. Explain `useNavigate`, `useParams`, `useLocation`, and `useSearchParams` — give one use case each.

## 1. useNavigate

Used for programmatic navigation.

### Use case:

Redirect user after login.

```jsx
const navigate = useNavigate();
navigate("/dashboard");
```

## 2. useParams

Used to get dynamic route parameters from URL.

### Use case:

Fetch user by ID.

URL:

```text
/user/123
```

```jsx
const { id } = useParams();
```

## 3. useLocation

Gives current URL info (path, state, query string).

### Use case:

Access data passed via navigation or track current route.

```jsx
const location = useLocation();
console.log(location.pathname);
```

## 4. useSearchParams

Used to read and update query parameters.

### Use case:

Filters or pagination.

URL:

```text
/products?page=2&sort=price
```

```jsx
const [searchParams] = useSearchParams();
searchParams.get("page"); // 2
```

### One-line summary:

- useNavigate → navigate programmatically
- useParams → get route params
- useLocation → get current URL info
- useSearchParams → handle query strings

---

# 5. How would you implement a private/protected route in v6?

In React Router v6, a protected route is implemented by creating a wrapper component that checks authentication and either:

- renders the requested page, or redirects to login

### Basic idea

- If user is logged in → allow access
- If not → redirect to /login

---

# 6.What problem does React Context solve? Define 'prop drilling'.

React Context solves the problem of sharing data across many components without manually passing props at every level.

Normally in React, data flows from parent → child using props.

But when many deeply nested components need the same data (like user info, theme, language, authentication state), passing props through every intermediate component becomes messy.

Context provides a global-like shared state for a part of the component tree.

### What is Prop Drilling?

Prop drilling means passing props through multiple intermediate components just to reach a deeply nested component that actually needs the data.

### Example without Context

```jsx
<App user={user} />

function App({ user }) {
  return <Dashboard user={user} />;
}

function Dashboard({ user }) {
  return <Sidebar user={user} />;
}

function Sidebar({ user }) {
  return <Profile user={user} />;
}

function Profile({ user }) {
  return <h1>{user.name}</h1>;
}
```

---

# 7.How do you create and consume a Context? Walk through `createContext`, `Provider`, `useContext`.

## 1. createContext()

Used to create a Context object.

Usually imported from React:

```jsx
import { createContext } from "react";
```

```jsx
const UserContext = createContext();
```

It creates a shared data container.

## 2. Provider

Provider is used to provide/share the data.

```jsx
<UserContext.Provider value="Rahul">
  <App />
</UserContext.Provider>
```

### Important

- value contains shared data
- All child components can access it

## 3. useContext()

Used to consume/access Context data.

```jsx
const data = useContext(UserContext);
```

Usually imported like:

```jsx
import { useContext } from "react";
```

React finds the nearest Provider and returns its value.

### Full Example

```jsx
import React, { createContext, useContext } from "react";

const UserContext = createContext();

function Profile() {
  const user = useContext(UserContext);

  return <h1>{user}</h1>;
}

function App() {
  return (
    <UserContext.Provider value="Rahul">
      <Profile />
    </UserContext.Provider>
  );
}
```

### Short Interview Answer

- createContext() → creates Context
- Provider → shares data
- useContext() → accesses shared data in child components without prop drilling

---

# 8. Why can Context cause performance issues, and how would you mitigate them (e.g. splitting contexts, memoising the value)?

Context can cause performance issues because whenever the Provider value changes, all consuming components re-render. This happens due to reference changes in the Context value object. To mitigate this, we can split contexts into smaller contexts, memoize the Provider value using useMemo, memoize components with React.memo, or use state management libraries like Redux or Zustand for large applications.

### Why can Context cause performance issues?

The main problem is:

When the Context value changes, all consuming components re-render.

Even if a component only needs one small part of the Context, it still re-renders when the Provider value changes.

### Example Problem

```jsx
<UserContext.Provider value={{ user, theme }}>
```

Suppose:

- Profile only uses user
- Navbar only uses theme

If theme changes:

- Navbar re-renders ✅
- Profile also re-renders ❌ unnecessarily

Because the entire Context value object changed.

### Why this happens

React compares Context values by reference.

This object:

```jsx
{ user, theme }
```

is recreated on every render.

So React thinks:

> “Context value changed → re-render consumers”

### How to mitigate performance issues

#### 1. Split Contexts (Very Important)

Instead of one big Context:

❌ Bad

```jsx
<AppContext.Provider value={{ user, theme, cart }}>
```

Create separate contexts:

✅ Better

```jsx
<UserContext.Provider>
<ThemeContext.Provider>
<CartContext.Provider>
```

Now only related components re-render.

#### 2. Memoize the Context Value

Use useMemo() so object reference does not change unnecessarily.

```jsx
const value = useMemo(() => {
  return { user, theme };
}, [user, theme]);
```

Then:

```jsx
<UserContext.Provider value={value}>
```

This prevents unnecessary object recreation.

Without useMemo

```jsx
<UserContext.Provider value={{ user, theme }}>
```

New object created every render ❌

#### 3. Memoize Components

Use React.memo() for expensive child components.

```jsx
export default React.memo(Profile);
```

Helps avoid unnecessary re-renders from parent updates.

#### 4. Use State Libraries for Large Apps

For very complex global state:

- Redux
- Zustand
- Recoil

---

# 9.When would you choose Context over Redux, and vice versa?

## When to use React Context

Use Context when:

- State is simple
- Data is shared globally
- Updates are not extremely frequent
- App size is small to medium

### Common use cases

- Authentication user
- Theme (dark/light)
- Language
- App settings

## When to use Redux

Use Redux when:

- Application state is complex
- Many components depend on shared state
- State updates happen frequently
- Business logic becomes large
- Debugging and scalability matter

---

# useReducer Hook

useReducer is a React hook that lets you manage state by dispatching actions to a reducer function, which returns a new state based on the current state and the action.

- reducer → a function that decides how state should change
- initialState → the initial value of the state, should be only  (array, object )
- state → current state
- dispatch → function used to send actions

```jsx
const [state, dispatch] = useReducer(reducer, initialState);
```

> “In useReducer, the flow starts when a user triggers an action using dispatch. The action is sent to the reducer function along with the current state. The reducer processes the action and returns a new state, and React re-renders the component based on that updated state.”

---

# UseRef Hook

useRef is a React Hook that is used to store a value that does not change on re-render and does not cause the component to re-render when updated.

it is commonly used to access DOM elements.

👉 It also helps us directly access DOM elements.

### ⚙️ Key Features

- Persistent value
- Value remains available even after component re-renders
- No re-render on update
- Updating `.current` does NOT update UI
- Direct DOM access
- Used to interact with HTML elements (focus, scroll, etc.)

```jsx
import React, { useRef } from "react";

function Example() {
  const inputRef = useRef(null);

  const handleClick = () => {
    inputRef.current.focus();
  };

  return (
    <>
      <input ref={inputRef} type="text" />
      <button onClick={handleClick}>Focus Input</button>
    </>
  );
}
```

### When is useRef used in real-world industry?

“In real-world projects, I use useRef mainly for accessing DOM elements like focusing inputs, storing values that should persist without causing re-render, managing timers like intervals, keeping previous state values, and integrating with third-party libraries (Charts (Chart.js, D3),Google Maps, Video players, Rich text editors ) that require direct DOM manipulation.”

---

# useContext hook

useContext is a React Hook that allows you to access global data (state) from a Context without passing props manually through every component (prop drilling).

### Why it is used:

- Avoid prop drilling
- Share data like:
  - user info
  - theme (dark/light)
  - authentication status
  - language settings

### ⚙️ Basic Flow:

- Create a context using createContext()
- Provide value using Context.Provider
- Consume value using useContext()

```jsx
import React, { createContext, useContext } from "react";

const UserContext = createContext();

function App() {
  return (
    <UserContext.Provider value={{ name: "Rahul" }}>
      <Child />
    </UserContext.Provider>
  );
}

function Child() {
  const user = useContext(UserContext);
  return <h1>{user.name}</h1>;
}
```

---

# what is Caching -

Caching is a technique of storing frequently used data in a temporary, fast storage so it can be reused quickly without repeating the same work (like fetching from server or recalculating).

### 🔁 Why caching is needed

Without caching:

- Every time data is needed → fetch from server or recompute
- This takes time and increases load

With caching:

- Data is stored once
- Next time → directly used from cache (fast)

### ⚙️ How caching works (simple flow)

- User requests data
- System checks:
  - Is data available in cache?
  - ✅ Yes → return cached data (fast)
  - ❌ No → fetch from server, store it in cache, then return it

### 📦 Real-world Examples

#### 1. Browser caching

- Images, CSS, JS files are stored in browser
- When you revisit a website → loads faster

#### 2. API caching

- API responses are stored temporarily
- Next request → reused instead of calling API again

#### 3. React caching

- useMemo caches computed values
- useCallback caches functions

### 🚀 Benefits of caching

- Faster performance ⚡
- Reduced server load
- Less network requests
- Better user experience

### ⚠️ Limitation

- Cached data can become stale (outdated)
- Needs proper invalidation/refresh strategy

---

# Performance and optimization

## useMemo (React Hook)

useMemo stores the result of an expensive calculation so it doesn’t run again on every render unless needed.

React useMemo is a React hook used to memoize (cache) the result of an expensive computation so that it is recalculated only when its dependencies change.

### 💡 Why it is used:

- Improve performance
- Avoid unnecessary recalculations
- Useful for expensive operations like:
  - filtering large lists
  - sorting data
  - complex calculations

### ⚙️ How it works:

- Runs the function initially
- Stores (caches) the result
- Re-runs only when dependency values change

```jsx
import React, { useMemo, useState } from "react";

function App() {
  const [count, setCount] = useState(0);
  const [number, setNumber] = useState(5);

  const expensiveCalculation = (num) => {
    console.log("Calculating...");
    return num * 2;
  };

  const result = useMemo(() => expensiveCalculation(number), [number]);

  return (
    <div>
      <h1>Result: {result}</h1>
      <button onClick={() => setCount(count + 1)}>Increase Count</button>
    </div>
  );
}
```

## useMemo & useEffect

### 🚫 Key Difference (Simple)

- useMemo = caches a value (result of a calculation)
- useEffect = runs side effects (not for returning values)

### 🧠 What you said about useMemo:

- Runs function initially
- Stores (caches) the result
- Re-runs only when dependencies change

✅ This is correct for useMemo

### 🔁 What useEffect actually does:

- Runs after render
- Used for side effects, such as:
  - API calls
  - subscriptions
  - DOM manipulation
  - logging
- It does not return a computed value for rendering

## useCallback Hook

useCallback is used to memoize functions so that the same function reference is reused across renders unless dependencies change.

### Why it is used:

- Prevent unnecessary re-creation of functions
- Improve performance
- Useful when passing functions to child components (to avoid re-renders)

```jsx
const memoizedFunction = useCallback(() => {
  // function logic
}, [dependencies]);
```

### ⚙️ How it works:

- On initial render → function is created
- On re-renders:
  - If dependencies don’t change → same function is reused
  - If dependencies change → new function is created

```jsx
import React, { useCallback, useState } from "react";

function App() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    console.log("Count is:", count);
  }, [count]);

  return (
    <div>
      <button onClick={handleClick}>Click</button>
      <button onClick={() => setCount(count + 1)}>Increase</button>
    </div>
  );
}
```

### 🔁 Behavior:

- When count = 0 → one function is created
- When count updates to 1 → new function is created

Why?

Because count is in dependency array

### Here are the key differences in short 👇

- useCallback → caches a function
- useMemo → caches a value (result of a calculation)
- useEffect → runs side effects after render (API calls, DOM updates, etc.)

---

# 12. What is the difference between controlled and uncontrolled components ?

Controlled components are managed by React state, while uncontrolled components store their state in the DOM and are accessed using refs.

## Controlled Components

A controlled component is a form element whose value is controlled by React state.

### Example: Controlled Input

```jsx
import React, { useState } from "react";

function ControlledForm() {
  const [name, setName] = useState("");

  return (
    <input
      type="text"
      value={name}
      onChange={(e) => setName(e.target.value)}
    />
  );
}

export default ControlledForm;
```

### How it works

- Input value comes from state
- Every change updates state
- React re-renders the component

## Uncontrolled Components

An uncontrolled component stores its value inside the DOM, not in React state.

Uncontrolled components rely on the DOM to manage form data using refs.

👉 We use refs to access values

### Example: Uncontrolled Input

```jsx
import React, { useRef } from "react";

function UncontrolledForm() {
  const inputRef = useRef();

  const handleSubmit = () => {
    console.log(inputRef.current.value);
  };

  return (
    <>
      <input type="text" ref={inputRef} />
      <button onClick={handleSubmit}>Submit</button>
    </>
  );
}

export default UncontrolledForm;
```

### How it works

- React does not control value
- DOM manages input state
- Value accessed only when needed


# 13. What is React Router, and how does client-side routing work ?

## What is React Router?

React Router is a library used in React applications to handle navigation and routing without reloading the page.

👉 It enables client-side routing in Single Page Applications (SPA).

### Why do we need React Router?

Without React Router:

- Every page change cuses a full page reload
- Bad user experienc
- Slower performance

With React Router:

- URL changes without refreshing the page
- Only required components are re-rendered
- Fast & smooth navigation

## What is Client-Side Routing?

Client-side routing means navigation happens in the browser without a full page reload.

### How React Router Works

1️⃣ User clicks a link

2️⃣ URL changes using history.pushState (It is browser's feature that is History API.

3️⃣ React Router matches the URL

4️⃣ Corresponding component is rendered

5️⃣ Page does not reload

---

# 14. What is the Context API, and when should you use it instead of Redux ?

Context API is used for sharing simple global data in React, while Redux is preferred for managing complex, large-scale application state.

## What is Context API?

Context API is a built-in React feature used to share data globally between components without passing props manually at every level.

👉 It solves the problem of props drilling.

### How Context API Works

1️⃣ Create a Context

2️⃣ Provide data using Provider

3️⃣ Consume data using useContext

React internally stores this data and makes it available to any component that needs it.

(No need to pass props manually)

### When Should You Use Context API?

Use Context API when:

- Data is global
- Data changes rarely
- App is small to medium

Examples:

- Theme (dark/light)
- User authentication
- Language (i18n)

## What is Redux ?

Redux is an external state management library used to manage complex and large-scale application state.

It provides:

- Central store
- Predictable state updates
- Debugging tools
- Middleware (thunk, saga)

### When to Use Context API instead of Redux ?

👉 Use Context API instead of Redux when:

- App is small or medium
- State is simple
- No complex logic
- No heavy data flow
- You want less boilerplate

Context API is suitable for simple global state, while Redux is better for complex state management.

### When Redux is Better ?

Use Redux when:

- Large application
- Many shared states
- Complex async logic
- Multiple teams working
- Need time-travel debugging

---

# 15. What is prop drilling, and how can it be avoided ?

Prop drilling is when you pass data through multiple intermediate React components just to reach a deeply nested component.

### How to avoid:

- Use Context API – Share data globally without passing props manually.
- Use state management libraries – Redux, Zustand, or MobX.

---

# 16. What is React.memo, and how does it help with performance optimization ?

React.memo is a HOC that prevents a component from re-rendering if its props haven’t changed.

Performance benefit: It reduces unnecessary re-renders, improving efficiency.

## 1. What React.memo Does

By default, whenever a parent component re-renders, all its child components re-render, even if their props haven’t changed. This can be inefficient for heavy components.

React.memo wraps a functional component and tells React:

“Only re-render this component if its props change.”

## 2. Performance Benefits

- Reduces unnecessary renders – especially useful in large component trees.
- Helps with expensive rendering logic – components that do heavy calculations or render large UI.
- Works best when props rarely change.

## 3. When NOT to Use React.memo

- If your component is very simple and cheap to render, React.memo may add slight overhead due to prop comparison.
- If props change frequently, memoization may not help.

```jsx
const MyComponent = React.memo(({ data }) => {
  return <div>{data}</div>;
});
```

---

# 17. What is the difference between useMemo and useCallback ?

useMemo → memoizes a value. It recalculates the value only when dependencies change.

```jsx
const totalPrice = useMemo(() => {
  console.log("Calculating total...");
  return items.reduce((sum, item) => sum + item.price, 0);
}, [items]);
```

👉 Re-calculates only when items changes

### Use case

- Expensive calculations
- Derived data
- Avoid recalculating values on every render

useCallback → memoizes a function. It returns the same function instance unless dependencies change.

```jsx
const memoizedFn = useCallback(() => {
  doSomething(a);
}, [a]);
```

### Use case

- Prevents unnecessary re-renders of memoized child components
- When passing functions as props
- Event handlers

---

# 18. What is a Higher-Order Component (HOC), and how is it used ?

HOC is a function that takes a component and returns a new component with additional functionality, mainly used for code reuse in React.

```jsx
const EnhancedComponent = HOC(OriginalComponent);
```

### Use:

In React, we often repeat logic like:

- Authentication check 🔐
- Logging 📊
- Data fetching 🌐
- Permissions 🚫

Instead of repeating code → we reuse logic using HOCExample:

```jsx
const withAuth = (Component) => {
  return (props) => <Component {...props} />;
};
```

---

# When would you reach for `useReducer` over `useState`?

I use useReducer instead of useState when state becomes a bit complex.

### Simple answer:

- useState → for simple things like input, toggle, counter
- useReducer → when state is complex or related

### I use useReducer when:

- I have multiple related state values (like form, cart, login)
- State changes in different ways (add, remove, reset, update)
- I want all state logic in one place

👉 In short:

- useState = simple state
- useReducer = complex state management

---

# React.memo

React.memo prevents unnecessary re-render of a component if its props have not changed.

## ❌ Without React.memo (problem)

```jsx
import { useState } from "react";

function Child({ name }) {
  console.log("Child render hua");
  return <h2>Hello {name}</h2>;
}
```

## ✅ With React.memo (solution)

```jsx
const Child = React.memo(function Child({ name }) {
  console.log("Child render hua");
  return <h2>Hello {name}</h2>;
});

export default function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        Increase Count ({count})
      </button>

      <Child name="Rahul" />
    </>
  );
}
```

### 🎯 When to use React.memo

#### ✔ 1. When same props are passed again and again

Use it for components like:

- button
- card
- list item

👉 If props don’t change, re-render can be avoided.

#### ✔ 2. When component is heavy

Use it when:

- large lists are rendered
- UI is complex
- performance may become slow

👉 It helps reduce unnecessary re-rendering.

#### ✔ 3. When parent re-renders but child data does not change

👉 Even if parent updates, child will not re-render if props are same.

### ❌ When NOT to use React.memo

#### ✖ 1. Small components

Example:

- simple button
- simple text

👉 No performance issue, so memo is not needed.

#### ✖ 2. When props change every time

Example:

```jsx
<Button onClick={() => console.log("click")} />
```

👉 Here function is recreated every render

👉 So React.memo will not help

---

# React.memo + useCallback + useMemo

## 1. Pehle Problem Samjho

React me parent re-render hota hai → by default child bhi re-render hota hai.

Example:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        Increase
      </button>

      <Child />
    </>
  );
}
```

Agar count change hua → Parent re-render → Child bhi re-render.

Chahe Child ko koi kaam hi na ho.

## 2. React.memo

React.memo child component ko unnecessary re-render se bachata hai.

```jsx
const Child = React.memo(() => {
  console.log("Child Render");

  return <h1>Child</h1>;
});
```

Ab child tabhi render hoga jab uske props change honge.

## 3. Problem with Function Props

Ab dekho:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    console.log("clicked");
  };

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        Increase
      </button>

      <Child onClick={handleClick} />
    </>
  );
}

const Child = React.memo(({ onClick }) => {
  console.log("Child Render");

  return <button onClick={onClick}>Click</button>;
});
```

Interview trick yahi hai 👇

Parent jab bhi re-render hoga:

```jsx
const handleClick = () => {}
```

Ye naya function create hota hai memory me.

To React compare karega:

```text
oldFunction !== newFunction
```

So prop change maan lega.

Result:

- ❌ React.memo fail
- ❌ Child firse render

## 4. useCallback Solution

useCallback function ko memoize karta hai.

```jsx
const handleClick = useCallback(() => {
  console.log("clicked");
}, []);
```

Ab same function reference rahega.

So:

```text
oldFunction === newFunction
```

Ab React.memo kaam karega.

✅ Child re-render nahi hoga.

## 5. useMemo Relation

useMemo values ko memoize karta hai.

Example:

```jsx
const expensiveValue = useMemo(() => {
  return heavyCalculation(data);
}, [data]);
```

Ye heavy calculation ko baar baar chalne se bachata hai.

---

# What does `useCallback` do? When does it actually help, and when is it premature optimisation?

useCallback memoizes a function reference so React can reuse the same function between renders. It mainly helps with preventing unnecessary re-renders in memoized child components and stabilizing dependencies, but using it everywhere is premature optimisation because it also has its own overhead.

- useCallback → memoizes function
- useMemo → memoizes value
- React.memo → memoizes component rendering

---

# What is referential equality and why does it matter for `React.memo` / dependency arrays?

Referential equality means:

Two objects/functions/arrays are considered equal only if they point to the same memory reference.

In JavaScript:

```js
{} === {} // false
[] === [] // false

const a = {};
const b = a;
a === b // true
```

Because objects, arrays, and functions are compared by reference, not by value.

## Why it matters in React

React uses shallow comparison in places like:

- React.memo
- useEffect dependency array
- useCallback
- useMemo

So React checks:

```text
oldProp === newProp
```

NOT deep equality.

## 1. React.memo case

Example:

```jsx
const Child = React.memo(({ onClick }) => {
  console.log("Child render");
  return <button onClick={onClick}>Click</button>;
});

function Parent() {
  return <Child onClick={() => console.log("clicked")} />;
}
```

### Problem:

```jsx
() => console.log("clicked")
```

creates a new function object on every render.

So:

```text
oldFunction !== newFunction
```

React thinks prop changed.

Therefore:

- React.memo fails to prevent re-render

### Fix with useCallback

```jsx
function Parent() {
  const handleClick = useCallback(() => {
    console.log("clicked");
  }, []);

  return <Child onClick={handleClick} />;
}
```

Now same function reference is reused.

```text
oldFunction === newFunction
```

React.memo can skip child render.

## 2. Dependency array case

Example:

```jsx
useEffect(() => {
  console.log("effect ran");
}, [{}]);
```

This runs every render.

Why?

Because:

```js
{} !== {}
```

New object reference every render

### Correct way

```jsx
const obj = useMemo(() => ({}), []);

useEffect(() => {
  console.log("effect ran");
}, [obj]);
```

Now reference stays same.

### Super Important Interview Line

React compares dependencies and props using shallow referential equality (===).

## Primitive vs Reference Types

shallow referential equality-React checks whether two values point to the same memory reference, not whether their content looks the same.

### Primitive values

Compared by value:

```js
1 === 1 // true
"hi" === "hi" // true
```

### Reference values

Compared by memory reference:

```js
[] === [] // false
{} === {} // false
(() => {}) === (() => {}) // false
```

---

# Name two cases where memoising HURTS performance instead of helping.

## 1. Cheap calculations / small components

If the computation or component render is already very fast, memoisation adds extra overhead:

- React must store previous values
- Compare dependencies/props
- Keep extra memory

Example:

```jsx
const doubled = useMemo(() => count * 2, [count]);
```

## 2. Values/functions changing every render anyway

Memoisation becomes useless if dependencies always change.



