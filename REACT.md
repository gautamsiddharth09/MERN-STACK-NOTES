Q. What is React and how does it work?

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



