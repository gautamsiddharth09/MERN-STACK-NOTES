# 📘 JavaScript Interview Notes

> **Interview-Focused Roadmap** covering **DOM**, **Events**, **Browser APIs**, **OOP**, **Prototypes**, **Inheritance**.

---

# 📑 Table of Contents

- [DOM (Document Object Model)](#-dom-document-object-model)
  - What is the DOM?
  - DOM Selection Methods
  - DOM Manipulation
  - Creating and Removing Elements
  - DOM Traversal
  - Attributes vs Properties
- [Events](#-events)
  - Event Handling
  - Event Bubbling and Capturing
  - Event Delegation
  - `preventDefault()` vs `stopPropagation()`
- [Browser APIs](#-browser-apis)
  - Window vs Document
  - localStorage vs sessionStorage
  - Browser Navigation and History
- [OOP Fundamentals](#-oop-fundamentals)
  - What is OOP?
  - Classes and Objects
  - Constructor Functions
  - The `new` Keyword
  - Static Methods and Properties
- [Prototypes & Inheritance](#-prototypes--inheritance)
  - What is Prototype?
  - Prototype Chain
  - `__proto__` vs `prototype`
  - Prototypal Inheritance
  - Classical vs Prototypal Inheritance
- [Advanced OOP](#-advanced-oop)
  - Inheritance with Classes
  - Method Overriding
  - Private Fields and Methods
  - Getters and Setters
  - `instanceof` Operator
- [OOP Principles](#-oop-principles)
  - Encapsulation
  - Abstraction
  - Inheritance
  - Polymorphism
---



## Q1: What is the DOM?

### Definition

The **DOM (Document Object Model)** is a **programming interface** for **HTML** and **XML** documents.

It represents the webpage as a **tree structure**, where every HTML element is a **node** that can be accessed and manipulated using **JavaScript**.

---

### ⭐ Key Points

- The **DOM is NOT JavaScript**.
- It is a **Web API** provided by the **browser**.
- **JavaScript uses the DOM API** to interact with webpages.

---

### 🌳 How DOM Works

When the browser loads an HTML page, it creates a **DOM Tree**.

```text
Document
└── html
    ├── head
    │   ├── title
    │   └── meta
    └── body
        ├── h1
        ├── p
        └── div
            ├── span
            └── button
```

- Every **element** is a node.
- Every **text** is a node.
- Every **attribute** is also represented inside the DOM.

---

### ✅ Why the DOM is Important

- **Dynamic Manipulation** → Change page content without reloading.
- **Interactivity** → Respond to user actions.
- **Real-Time Updates** → Update only specific parts of the webpage.
- **Structured Access** → Provides an organized way to access HTML elements.

---

> 💡 **Interview Tip**
>
> If asked **"What is the DOM?"**, always mention:
>
> - It is a **tree structure**.
> - It is a **browser-provided Web API**.
> - **JavaScript uses it** to manipulate HTML dynamically.

---

# Q2: What are the Different Ways to Select DOM Elements?

### Answer

JavaScript provides several methods to select DOM elements, and each has different use cases.

---

## 1️⃣ `getElementById()`

Selects an element by its **ID**.

### Features

- Returns **one element**
- IDs should be **unique**

### Syntax

```js
const element = document.getElementById("myId");
```

### When to Use

- When selecting a **single unique element**.

---

## 2️⃣ `getElementsByClassName()`

Selects elements by **class name**.

### Features

- Returns a **live HTMLCollection**
- Automatically updates if the DOM changes

### Syntax

```js
const elements = document.getElementsByClassName("myClass");
```

### When to Use

- When selecting **all elements having the same class**.

---

## 3️⃣ `getElementsByTagName()`

Selects elements by **tag name**.

### Features

- Returns a **live HTMLCollection**

### Syntax

```js
const paragraphs = document.getElementsByTagName("p");

const allElements = document.getElementsByTagName("*");
```

### When to Use

- Selecting all `<div>`, `<p>`, `<li>`, etc.

---

## 4️⃣ `querySelector()`

Selects the **first element** matching a **CSS selector**.

### Features

- Returns the **first matching element**
- Supports **all CSS selectors**

### Syntax

```js
const element = document.querySelector(".myClass");

const element = document.querySelector("#myId");

const element = document.querySelector("div.container > p");

const element = document.querySelector('[data-id="123"]');
```

### When to Use

- When using **complex CSS selectors**.
- When only the **first matching element** is needed.

---

## 5️⃣ `querySelectorAll()`

Selects **all elements** matching a CSS selector.

### Features

- Returns a **static NodeList**
- Does **not** automatically update when the DOM changes

### Syntax

```js
const elements = document.querySelectorAll(".myClass");

const elements = document.querySelectorAll("div p");

const elements = document.querySelectorAll('[data-active="true"]');
```

### When to Use

- Modern and preferred way to select **multiple elements**.

---

## 📊 Comparison Table

| Method | Returns | Collection Type | Supports CSS Selectors | Live / Static |
|---------|----------|-----------------|-------------------------|---------------|
| `getElementById()` | Single Element | Element | ❌ No | N/A |
| `getElementsByClassName()` | Multiple | HTMLCollection | ❌ No | ✅ Live |
| `getElementsByTagName()` | Multiple | HTMLCollection | ❌ No | ✅ Live |
| `querySelector()` | First Match | Element | ✅ Yes | N/A |
| `querySelectorAll()` | All Matches | NodeList | ✅ Yes | ❌ Static |

---

> 💡 **Interview Tip**
>
> - **`querySelector()`** → First matching element.
> - **`querySelectorAll()`** → All matching elements.
> - **`getElementsByClassName()`** and **`getElementsByTagName()`** return **live HTMLCollections**.
> - **`querySelectorAll()`** returns a **static NodeList**, making it the preferred modern approach.

---

# ❓ Common Interview Questions

1. What is the DOM?
2. Is the DOM a part of JavaScript?
3. What is the difference between **DOM** and **HTML**?
4. What is a DOM Tree?
5. Explain different DOM selection methods.
6. What is the difference between **`querySelector()`** and **`querySelectorAll()`**?
7. What is the difference between **HTMLCollection** and **NodeList**?
8. Which DOM selection method do you prefer and why?
9. Which methods return **live collections**?
10. Why is `querySelectorAll()` commonly preferred in modern JavaScript?

---

# Q3: How do you Manipulate DOM Elements?

### Answer

**DOM Manipulation** means changing the **content**, **structure**, or **styling** of HTML elements **after the page has loaded**.

---

## 1️⃣ Changing Content

### `textContent` → Plain Text Only

- Gets or sets the **text content** of an element.
- **Does not parse HTML**.
- **Safe** against HTML injection.

### Example

```js
const element = document.querySelector("p");

// Get text
console.log(element.textContent); // "Hello World"

// Set text
element.textContent = "New text";

// HTML is treated as plain text
element.textContent = "<strong>Text</strong>";
```

**Output**

```text
<strong>Text</strong>
```

---

### `innerHTML` → HTML Content

- Gets or sets the **HTML content** inside an element.
- **Parses HTML tags** before rendering.

### Example

```js
const element = document.querySelector("div");

// Get HTML
console.log(element.innerHTML); // "<p>Hello</p>"

// Set HTML
element.innerHTML = "<p>New content</p>";

element.innerHTML = "<strong>Bold text</strong>";
```

**Output**

```html
<strong>Bold text</strong>
```

Rendered as **Bold text**.

---

## `innerText` vs `textContent`

### Example

```js
const element = document.querySelector("div");

element.innerHTML =
  '<span style="display:none">Hidden</span> Visible';

console.log(element.innerText); // "Visible"

console.log(element.textContent); // "Hidden Visible"
```

---

## 📊 Comparison

| Feature | `innerText` | `textContent` |
|---------|-------------|---------------|
| Respects CSS (`display:none`) | ✅ Yes | ❌ No |
| Includes Hidden Text | ❌ No | ✅ Yes |
| Performance | Slower | Faster |
| Parses HTML | ❌ No | ❌ No |
| Returns Visible Text Only | ✅ Yes | ❌ No |

---

> 💡 **Interview Tip**
>
> - Use **`textContent`** when working with plain text because it is **faster** and **safer**.
> - Use **`innerHTML`** only when you intentionally want to insert HTML.

---

# Q4: DOM Manipulation — Create, Insert & Remove Elements

---

## 1️⃣ Create Element

Creates an element **in memory**. It is **not visible** until it is inserted into the DOM.

```js
const div = document.createElement("div");

div.textContent = "Hello";

div.classList.add("box");

div.setAttribute("id", "myDiv");
```

---

## 2️⃣ Insert Element

### `appendChild()` → Add as **last child**

```js
parent.appendChild(div);
```

---

### `append()` → Add multiple elements or text

```js
parent.append(div, "Hello");
```

---

### `prepend()` → Add as **first child**

```js
parent.prepend(div);
```

---

### `insertBefore()` → Insert before a specific child

```js
parent.insertBefore(newDiv, oldDiv);
```

---

### `insertAdjacentElement()`

```js
element.insertAdjacentElement("beforebegin", div);

element.insertAdjacentElement("afterbegin", div);

element.insertAdjacentElement("beforeend", div);

element.insertAdjacentElement("afterend", div);
```

---

### `insertAdjacentHTML()` → Insert HTML String

```js
element.insertAdjacentHTML("beforeend", "<p>Hello</p>");
```

---

## 3️⃣ Remove Element

### `remove()` (Modern)

```js
element.remove();
```

---

### `removeChild()`

```js
parent.removeChild(child);
```

---

### Remove All Children

```js
parent.innerHTML = "";

// OR

parent.replaceChildren();
```

---

## 4️⃣ Replace Element

### `replaceChild()`

```js
parent.replaceChild(newNode, oldNode);
```

---

### `replaceWith()`

```js
oldNode.replaceWith(newNode);
```

---

## ⭐ Interview Points

- **`createElement()`** → Creates an element in memory.
- **`appendChild()`** → Adds the last child.
- **`append()`** → Adds multiple elements or text.
- **`prepend()`** → Adds the first child.
- **`insertBefore()`** → Inserts before a specific node.
- **`remove()`** → Removes an element.
- **`replaceWith()`** → Replaces an element.
- **`cloneNode(true)`** → Creates a deep copy (includes child elements).
- **`DocumentFragment`** → Efficient for inserting many elements.

---

> 💡 **Interview Tip**
>
> **`append()`** is more flexible than **`appendChild()`** because it can insert both **elements** and **text**.

---

# Q5: What's the Difference Between Attributes and Properties?

### Answer

**Attributes** and **Properties** are related but different concepts in the DOM and are a common interview topic.

---

## 1️⃣ Attributes

Attributes are defined in **HTML**.

They are the values written inside HTML tags.

### Example

```html
<input
  type="text"
  value="Hello"
  id="myInput"
  data-custom="123"
/>
```

Here,

- `type`
- `value`
- `id`
- `data-custom`

are **attributes**.

---

## 2️⃣ Properties

Properties belong to the **DOM Object** created by the browser.

When the browser parses HTML, it creates a JavaScript object.

### Example

```html
<input id="username" value="Hello" />
```

Browser creates a DOM object similar to:

```js
input = {
  id: "username",
  value: "Hello",
};
```

> **Note:** This is **not** the actual browser object. It is only for understanding the concept.

---

### Accessing Properties

```js
const input = document.querySelector("input");

console.log(input.id);

console.log(input.value);
```

Here,

- `input` → DOM Object
- `id` → Property
- `value` → Property
- `"username"` and `"Hello"` → Current property values

---

## 3️⃣ Attributes are Always Strings, Properties Have Data Types

### HTML

Attributes are stored as **text (strings)**.

```html
<input type="checkbox" checked />
```

The presence of the `checked` attribute means the checkbox is **initially checked**.

---

### JavaScript Property

Properties can have different data types such as:

- String
- Boolean
- Number

```js
const input = document.querySelector("input");

console.log(input.checked); // true

console.log(typeof input.checked); // "boolean"
```

---

## 📊 Attributes vs Properties

| Feature | Attributes | Properties |
|----------|------------|------------|
| Defined In | HTML | JavaScript DOM Object |
| Stored As | Strings | Any Data Type |
| Created By | Developer | Browser |
| Accessed Using | `getAttribute()` | Dot Notation (`element.value`) |
| Can Be Boolean | ❌ Stored as string/presence | ✅ Boolean |
| Represents | Initial HTML Value | Current State |

---

> 💡 **Interview Tip**
>
> A common interview question is:
>
> **"What is the difference between an Attribute and a Property?"**
>
> The easiest way to remember:
>
> - **Attribute = HTML**
> - **Property = JavaScript DOM Object**
> - **Attribute stores the initial value**, while **Property reflects the current value**.

---

# ❓ Common Interview Questions

1. What is DOM Manipulation?
2. What is the difference between **`textContent`** and **`innerHTML`**?
3. What is the difference between **`innerText`** and **`textContent`**?
4. How do you create an element in JavaScript?
5. What is the difference between **`appendChild()`** and **`append()`**?
6. Explain **`insertAdjacentHTML()`**.
7. What is **`replaceWith()`**?
8. What is the difference between **Attributes** and **Properties**?
9. Why is `checked` a boolean property?
10. What does `cloneNode(true)` do?

---

# Q6: How do you Handle Events in JavaScript?

### Answer

An **event** is an action performed by the **user** or the **browser**.

### Common Events

- `click`
- `submit`
- `change`
- `keydown`
- `keyup`
- `mouseover`
- `load`

---

## 1️⃣ DOM Property

Attach an event using a DOM property.

```js
const btn = document.querySelector("button");

btn.onclick = () => {
  console.log("Clicked");
};
```

### Limitation

- ❌ Only **one event handler** can be attached.

---

## 2️⃣ `addEventListener()` (Best Practice)

Attach an event listener using **`addEventListener()`**.

```js
const btn = document.querySelector("button");

btn.addEventListener("click", () => {
  console.log("Clicked");
});
```

### Advantages

- ✅ Multiple listeners can be attached.
- ✅ Event listeners can be removed.
- ✅ Cleaner and more flexible.
- ✅ Preferred modern approach.

---

## Event Object (`event` or `e`)

The browser automatically passes an **Event Object** to the callback function.

```js
button.addEventListener("click", (e) => {
  console.log(e.target);
  console.log(e.type);
});
```

### Common Properties

| Property | Description |
|----------|-------------|
| **`e.target`** | Element that triggered the event |
| **`e.currentTarget`** | Element where the listener is attached |
| **`e.type`** | Event name |
| **`e.preventDefault()`** | Stops the browser's default behavior |
| **`e.stopPropagation()`** | Stops the event from propagating to parent elements |

---

# JavaScript Event Flow

Sometimes, when you click a button, **another element also responds**.

This happens because of **JavaScript Event Flow**.

---

## What is Event Flow?

When you click an element, JavaScript does **not** handle the event directly.

The event follows a path called **Event Flow**.

Example:

```html
<div id="parent">
  <button id="child">Click Me</button>
</div>
```

---

## Event Flow Happens in Three Steps

### 1️⃣ Capturing Phase (Top → Bottom)

Event travels **down** toward the clicked element.

```
Window
   ↓
Document
   ↓
Parent
   ↓
Child
```

---

### 2️⃣ Target Phase

The event reaches the element that was actually clicked.

```
Child (Target)
```

---

### 3️⃣ Bubbling Phase (Bottom → Top)

The event travels **back up**.

```
Child
   ↑
Parent
   ↑
Document
   ↑
Window
```

---

### 🎯 Easy Analogy

Imagine a **ball**.

- Ball falls from the **rooftop to the ground** → **Capturing**
- Ball hits the **ground** → **Target**
- Ball bounces **back upward** → **Bubbling**

---

# Q1: What is Event Capturing?

**Event Capturing** is a phase of JavaScript event flow where the event starts from the **top** and moves **down** toward the target element.

### Flow

```
Window
↓
Document
↓
Parent
↓
Child (Target)
```

---

## By Default

Capturing is **OFF**.

Enable it by passing **`true`** as the third argument.

### Syntax

```js
element.addEventListener(event, callback, true);
```

Where:

- **event** → Event type (`click`, `mouseover`, etc.)
- **callback** → Function to execute
- **true** → Enables capturing phase

---

### Example

#### HTML

```html
<div id="grandparent">
  <button id="parentBtn">
    Parent Button
    <button id="childBtn">Child Button</button>
  </button>
</div>
```

#### JavaScript

```js
document
  .getElementById("grandparent")
  .addEventListener(
    "click",
    () => console.log("Grandparent clicked"),
    true
  );

document
  .getElementById("parentBtn")
  .addEventListener(
    "click",
    () => console.log("Parent Button clicked"),
    true
  );

document
  .getElementById("childBtn")
  .addEventListener(
    "click",
    () => console.log("Child Button clicked"),
    true
  );
```

### Output (Click Child Button)

```text
Grandparent clicked
Parent Button clicked
Child Button clicked
```

> **Note:** Event Capturing is **rarely used**.

---

# Q2: What is Event Bubbling?

**Event Bubbling** is the phase where the event starts from the **target element** and moves upward through its parent elements.

### Flow

```
Child
↑
Parent
↑
Grandparent
↑
Document
↑
Window
```

---

### Example

#### HTML

```html
<div id="parent">
  <button id="btn1">Button 1</button>
  <button id="btn2">Button 2</button>
</div>
```

#### JavaScript

```js
document.getElementById("parent").addEventListener("click", () => {
  console.log("Parent clicked");
});

document.getElementById("btn1").addEventListener("click", () => {
  console.log("Button 1 clicked");
});

document.getElementById("btn2").addEventListener("click", () => {
  console.log("Button 2 clicked");
});
```

### Output

#### Click Button 1

```text
Button 1 clicked
Parent clicked
```

#### Click Button 2

```text
Button 2 clicked
Parent clicked
```

Here, a separate listener is attached to every button.

This is **not an efficient approach** when many child elements exist.

---

# Q3: What is Event Delegation?

**Event Delegation** is **not a phase**.

It is a **technique** that uses **Event Bubbling**.

Instead of attaching separate listeners to every child element, we attach **one listener to the parent element**.

The event bubbles from the child to the parent, allowing the parent to determine which child triggered the event.

---

### Example

#### HTML

```html
<div id="parent">
  <button id="btn1">Button 1</button>
  <button id="btn2">Button 2</button>
  <button id="btn3">Button 3</button>
</div>
```

#### JavaScript

```js
document.getElementById("parent").addEventListener("click", (e) => {
  if (e.target.tagName === "BUTTON") {
    console.log("Clicked:", e.target.id);
  }
});
```

### Output

```text
Button 1 → btn1

Button 2 → btn2

Button 3 → btn3
```

Only **one event listener** handles all buttons.

---

## Advantages of Event Delegation

### ✅ Better Performance

Instead of:

```text
100 Buttons = 100 Event Listeners
```

Use:

```text
100 Buttons = 1 Parent Listener
```

---

### ✅ Cleaner Code

- Less repeated code
- Easier to maintain

---

### ✅ Supports Dynamic Elements

Even if new buttons are added later, the same parent listener continues to work.

---

> 💡 **Interview Tip**
>
> **Event Delegation** is one of the most frequently asked JavaScript interview topics.
>
> Remember:
>
> - It is **not** an event phase.
> - It is a **technique** built on **Event Bubbling**.

---

# Q7: What's the Difference Between `preventDefault()` and `stopPropagation()`?

Both methods solve different problems and are often used together.

| Method | Purpose |
|---------|----------|
| **`preventDefault()`** | Stops the browser's default behavior |
| **`stopPropagation()`** | Stops the event from reaching parent elements |

---

# 1️⃣ `preventDefault()`

### What it Does

Cancels the browser's **default action**.

---

## Common Use Cases

- Prevent form submission
- Prevent page reload
- Prevent link navigation
- Prevent context menu
- Prevent default drag & drop behavior

---

### Example

#### HTML

```html
<a href="https://google.com" id="link">Google</a>
```

#### JavaScript

```js
const link = document.getElementById("link");

link.addEventListener("click", (event) => {
  event.preventDefault();
  console.log("Navigation prevented");
});
```

### Normal Behavior

```text
Google opens.
```

### After `preventDefault()`

```text
Navigation prevented
```

The browser **does not open Google**.

---

# 2️⃣ `stopPropagation()`

### What it Does

Stops the event from propagating to parent elements during **capturing** or **bubbling**.

---

## Common Use Cases

- Nested buttons inside cards
- Dropdown menus
- Modal dialogs
- Prevent parent click handlers

---

### Example

#### HTML

```html
<div id="parent">
  <button id="child">Click Me</button>
</div>
```

#### JavaScript

```js
parent.addEventListener("click", () => {
  console.log("Parent clicked");
});

child.addEventListener("click", () => {
  console.log("Child clicked");
});
```

### Output

```text
Child clicked

Parent clicked
```

The parent is triggered because of **Event Bubbling**.

---

### Using `stopPropagation()`

```js
child.addEventListener("click", (event) => {
  event.stopPropagation();
  console.log("Child clicked");
});
```

### Output

```text
Child clicked
```

The event **does not reach the parent**.

---

## 📊 `preventDefault()` vs `stopPropagation()`

| Feature | `preventDefault()` | `stopPropagation()` |
|----------|--------------------|---------------------|
| Stops Browser Default Action | ✅ Yes | ❌ No |
| Stops Bubbling | ❌ No | ✅ Yes |
| Stops Capturing | ❌ No | ✅ Yes |
| Prevents Link Navigation | ✅ Yes | ❌ No |
| Prevents Form Submission | ✅ Yes | ❌ No |
| Prevents Parent Click | ❌ No | ✅ Yes |

---

> 💡 **Interview Tip**
>
> A very common interview question:
>
> **"Can we use `preventDefault()` and `stopPropagation()` together?"**
>
> ✅ **Yes.**
>
> - `preventDefault()` stops the browser's default action.
> - `stopPropagation()` prevents the event from reaching parent elements.

---

# ❓ Common Interview Questions

1. What is an Event in JavaScript?
2. What is the difference between `onclick` and `addEventListener()`?
3. What is the Event Object?
4. Explain `event.target` and `event.currentTarget`.
5. What is JavaScript Event Flow?
6. Explain Capturing, Target, and Bubbling phases.
7. What is Event Delegation?
8. Why is Event Delegation better than attaching multiple listeners?
9. What is the difference between `preventDefault()` and `stopPropagation()`?
10. Can both methods be used together?

---

 # 8. What's the difference between `window` and `document`?

## How to Answer

> "window and document are both global objects in the browser, but they represent different things.

---

## `window`

The global object representing the browser window. It's the top-level object containing everything.

### Example

```javascript
// Global object
console.log(window); // The window object itself

// Global variables are properties of window
var myVar = 'hello';
console.log(window.myVar); // 'hello'

// Window properties
console.log(window.innerWidth);   // Viewport width
console.log(window.innerHeight);  // Viewport height
console.log(window.location);     // Current URL
console.log(window.history);      // Browser history
console.log(window.localStorage); // Local storage
```

---

## `document`

Represents the HTML document loaded in the window. It's a property of window.

### Example

```javascript
// document is property of window
console.log(window.document === document); // true

// Document represents the DOM
console.log(document.body);          // Body element
console.log(document.title);         // Page title
console.log(document.querySelector); // DOM methods
```

---

# Key differences

| `window` | `document` |
|----------|------------|
| Browser window | HTML document |
| Global scope | DOM tree |
| `window.alert()` | `document.querySelector()` |
| `window.location` | `document.body` |
| `window.innerWidth` | `document.title` |

---

# `window` methods and properties

## Dialogs

```javascript
window.alert('Message');
window.confirm('Are you sure?');
window.prompt('Enter name:');
```

## Timing

```javascript
window.setTimeout(() => {}, 1000);
window.setInterval(() => {}, 1000);
```

## Navigation

```javascript
window.open('url');
window.close();
window.location.href = 'url';
```

## Dimensions

```javascript
window.innerWidth;
window.innerHeight;
window.outerWidth;
window.outerHeight;
```

## Scroll

```javascript
window.scrollTo(0, 0);
window.scrollBy(0, 100);
```

---

# `document` methods and properties

## DOM selection

```javascript
document.getElementById('id');
document.querySelector('.class');
```

## DOM manipulation

```javascript
document.createElement('div');
document.createTextNode('text');
```

## Document info

```javascript
document.title;
document.URL;
document.domain;
document.cookie;
```

## Events

```javascript
document.addEventListener('click', handler);
```

---

# Hierarchy

Interview tip: window is the global browser object containing everything including document. document represents the HTML DOM tree and is a property of window. window has browser-related APIs, document has DOM manipulation methods.


# 9. What's the difference between `localStorage`, `sessionStorage`, and `cookies`?

## How to Answer

> "All three store data in the browser, but they differ in capacity, expiration, and scope.

---

# `localStorage`

Stores data with no expiration. Data persists even after browser is closed.

## Set item

```javascript
localStorage.setItem('username', 'John');
localStorage.setItem('user', JSON.stringify({ name: 'John', age: 30 }));
```

## Get item

```javascript
const username = localStorage.getItem('username'); // 'John'
const user = JSON.parse(localStorage.getItem('user'));
```

## Remove item

```javascript
localStorage.removeItem('username');
```

## Clear all

```javascript
localStorage.clear();
```

## Check length

```javascript
console.log(localStorage.length);
```

## Iterate

```javascript
for (let i = 0; i < localStorage.length; i++) {
  const key = localStorage.key(i);
  console.log(key, localStorage.getItem(key));
}
```

---

# `sessionStorage`

Same API as `localStorage`, but data clears when tab/browser closes.

## Example

```javascript
// Same methods as localStorage
sessionStorage.setItem('temp', 'data');
sessionStorage.getItem('temp');
sessionStorage.removeItem('temp');
sessionStorage.clear();
```

---

# `Cookies`

Older storage method, sent to server with every request.

## Set cookie

```javascript
document.cookie = 'username=John; max-age=3600; path=/';
```

## Get all cookies (as string)

```javascript
console.log(document.cookie); // 'username=John; theme=dark'
```

## Delete cookie (set expired)

```javascript
document.cookie = 'username=; max-age=0';
```

---

# Comparison table

| Feature | `localStorage` | `sessionStorage` | `Cookies` |
|----------|----------------|------------------|-----------|
| Capacity | ~10MB | ~10MB | ~4KB |
| Expiration | Never | Tab close | Set manually |
| Scope | All tabs/windows | Single tab | Domain |
| Sent to server | No | No | Yes (auto) |
| API | Simple | Simple | String-based |
| Use case | Long-term | Temporary | Auth tokens |

---

# When to use

## `localStorage`

- User preferences (theme, language)
- Cart data (persists across sessions)
- Cached data
- Draft content

---

## `sessionStorage`

- Form data during session
- Temporary state
- Multi-step processes
- Tab-specific data

---

## `Cookies`

- Authentication tokens
- Session IDs
- Server needs the data
- Cross-domain situations

---

# Security considerations

```javascript
// localStorage/sessionStorage - visible in DevTools
// Don't store sensitive data!
localStorage.setItem('password', '123'); // Bad!

// For sensitive data, use httpOnly cookies (server-side only)
// Can't be accessed by JavaScript
```

---

# Interview tip

> localStorage persists forever, sessionStorage clears on tab close, cookies expire based on settings and are sent to server. localStorage/sessionStorage hold ~10MB, cookies only ~4KB. Use localStorage for user preferences, sessionStorage for temporary data, cookies for auth.

# OOP Fundamentals

# Q10: What is Object-Oriented Programming?

## How to Answer

> "Object-Oriented Programming (OOP) is a programming paradigm based on the concept of objects, which contain data (properties) and code (methods).

---

# Core concept

Instead of having separate functions and data, OOP bundles them together into objects. An object represents a real-world entity with characteristics (properties) and behaviors (methods).

---

# Example without OOP

```javascript
// Procedural approach
const userName = 'John';
const userAge = 30;

function greetUser() {
  console.log(`Hello ${userName}`);
}

function getUserInfo() {
  return `${userName} is ${userAge}`;
}
```

---

# Example with OOP

```javascript
// Object-oriented approach
const user = {
  name: 'John',
  age: 30,

  greet() {
    console.log(`Hello ${this.name}`);
  },

  getInfo() {
    return `${this.name} is ${this.age}`;
  }
};

user.greet(); // 'Hello John'
```

---

# Four pillars of OOP

1. **Encapsulation** - Bundle data and methods together
2. **Abstraction** - Hide complex details, show only necessary
3. **Inheritance** - Reuse code from parent classes
4. **Polymorphism** - Same interface, different implementations

These are covered in detail in later questions.

---

# Benefits of OOP

- Organization - Code is structured and logical
- Reusability - Inheritance allows code reuse
- Maintainability - Easier to update and debug
- Scalability - Better for large applications
- Modularity - Independent objects
- Real-world modeling - Objects represent real entities

---

# OOP in JavaScript

JavaScript uses prototypal inheritance, not classical inheritance like Java/C++, but achieves OOP principles.

---

# Interview tip

> OOP organizes code around objects that contain both data and behavior. The four pillars are encapsulation, abstraction, inheritance, and polymorphism. Benefits include better organization, reusability, and maintainability.

# Q11: What are classes in JavaScript?

## How to Answer

> "Classes in JavaScript (ES6+) are templates for creating objects. They provide a cleaner syntax for creating objects and dealing with inheritance.

---

# Basic class

```javascript
class Person {
  // Constructor - runs when object is created
  constructor(name, age) {
    this.name = name; // Instance property
    this.age = age;
  }

  // Method
  greet() {
    console.log(`Hello, I'm ${this.name}`);
  }

  // Method
  getInfo() {
    return `${this.name} is ${this.age} years old`;
  }
}

// Create instance
const john = new Person('John', 30);
john.greet(); // 'Hello, I'm John'
```

---

# Constructor

Special method that runs when creating an instance:

```javascript
class Car {
  constructor(brand, model) {
    console.log('Creating car...');
    this.brand = brand;
    this.model = model;
    this.speed = 0; // Default value
  }
}

const myCar = new Car('Toyota', 'Camry');
// Logs: 'Creating car...'
```

---

# Class vs Object Literal

## Object literal - single object

```javascript
const john = {
  name: 'John',
  greet() { console.log('Hello'); }
};
```

## Class - template for multiple objects

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    console.log('Hello');
  }
}

const john = new Person('John');
const jane = new Person('Jane'); // Reusable!
```

---

# Interview tip

> Classes are templates for creating objects introduced in ES6. They have a constructor method that initializes properties and can have methods. Classes are syntactic sugar over JavaScript's prototypal inheritance.

# Q12: What is Prototype in JavaScript?

## How to Answer

> "Prototype is the mechanism by which JavaScript objects inherit properties and methods from other objects. Every object in JavaScript has an internal link to another object called its prototype.

---

# How prototype works

```javascript
function Person(name) {
  this.name = name;
}

// Add method to prototype
Person.prototype.greet = function() {
  console.log(`Hello, I'm ${this.name}`);
};

const john = new Person('John');
john.greet(); // 'Hello, I'm John'
// john doesn't have greet(), but its prototype does
```

---

# Prototype chain

When you access a property on an object:

1. JavaScript first looks on the object itself
2. If not found, looks at the object's prototype
3. If not found, looks at the prototype's prototype
4. Continues until reaches null (end of chain)

```javascript
const obj = { a: 1 };

// obj → obj.__proto__ (Object.prototype) → null

console.log(obj.toString); // Found on Object.prototype
// Even though obj doesn't have toString, it inherits it
```

---

# Why use prototype

## Without prototype (inefficient)

```javascript
function Person(name) {
  this.name = name;
  this.greet = function() { // New function for EACH instance
    console.log(`Hello ${this.name}`);
  };
}

const john = new Person('John');
const jane = new Person('Jane');

console.log(john.greet === jane.greet); // false (different functions)
// Memory waste - 1000 instances = 1000 copies of greet!
```

---

## With prototype (efficient)

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function() { // Shared by ALL instances
  console.log(`Hello ${this.name}`);
};

const john = new Person('John');
const jane = new Person('Jane');

console.log(john.greet === jane.greet); // true (same function)
// One function in memory, shared by all instances
```

---

# Checking prototype

```javascript
const john = new Person('John');

// Get prototype
console.log(Object.getPrototypeOf(john));
console.log(john.__proto__); // Non-standard but widely supported

// Check if property is own or inherited
console.log(john.hasOwnProperty('name')); // true (own)
console.log(john.hasOwnProperty('greet')); // false (inherited)
```

---

# Interview tip

> Prototype is JavaScript's inheritance mechanism. Properties/methods on prototype are shared by all instances (memory efficient). When accessing a property, JavaScript searches the prototype chain until it finds it or reaches null.

# Q13: What is the prototype chain?

## How to Answer

> "The prototype chain is how JavaScript implements inheritance. It's a series of links between objects through their prototypes.

---

# Visual representation

```text
instance → Constructor.prototype → Object.prototype → null
```

---

# Example

```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log(`${this.name} is eating`);
};

function Dog(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
}

Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

Dog.prototype.bark = function() {
  console.log('Woof!');
};

const myDog = new Dog('Max', 'Labrador');

// Prototype chain:
// myDog → Dog.prototype → Animal.prototype → Object.prototype → null

myDog.bark();      // Found on Dog.prototype
myDog.eat();       // Found on Animal.prototype
myDog.toString();  // Found on Object.prototype
```

---

# How lookup works

## `myDog.bark();`

```javascript
// 1. Check myDog itself? No
// 2. Check Dog.prototype? YES - found!
// Execute it
```

---

## `myDog.eat();`

```javascript
// 1. Check myDog itself? No
// 2. Check Dog.prototype? No
// 3. Check Animal.prototype? YES - found!
// Execute it
```

---

# End of chain

```javascript
console.log(Object.getPrototypeOf(Object.prototype)); // null
// null is the end of the prototype chain
```

---

# Interview tip

> The prototype chain is a linked list of prototypes. When accessing a property, JavaScript walks up the chain until it finds it or reaches null. This is how inheritance works in JavaScript.

# OOP Principles

# Q14: What is Encapsulation?

## How to Answer

> "Encapsulation is the process of combining data and methods into a single object while hiding the internal data from direct access.

---

# Benefits

- Data hiding - Internal state is protected
- Controlled access - Only expose what's necessary
- Easier maintenance - Implementation can change without affecting external code
- Prevent accidents - Can't accidentally modify internal state

---

# Example with private fields (ES2022)

```javascript
class BankAccount {
  #balance = 0; // Private field (# makes it private)

  constructor(initialBalance) {
    this.#balance = initialBalance;
  }

  // Public method to access private data
  getBalance() {
    return this.#balance;
  }

  deposit(amount) {
    if (amount > 0) {
      this.#balance += amount;
    }
  }

  withdraw(amount) {
    if (amount > 0 && amount <= this.#balance) {
      this.#balance -= amount;
    }
  }
}

const account = new Bank Account(1000);
console.log(account.getBalance()); // 1000
account.deposit(500);
// account.#balance = 0; // Error! Can't access private field
```

---

# Interview tip

> Encapsulation means bundling data and methods together and hiding internal details. Use private fields (#) to restrict direct access. Expose only necessary methods (public interface) to interact with the object."

---

# 15: What is Inheritance?

## How to Answer

> "Inheritance allows a class to inherit properties and methods from another class, promoting code reuse.

---

# ES6 class inheritance

```javascript
// Parent class
class Animal {
  constructor(name) {
    this.name = name;
  }

  eat() {
    console.log(`${this.name} is eating`);
  }
}

// Child class inherits from Animal
class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Call parent constructor
    this.breed = breed;
  }

  bark() {
    console.log('Woof!');
  }
}

const myDog = new Dog('Max', 'Labrador');
myDog.eat();  // Inherited from Animal
myDog.bark(); // Own method
```

---

# Key points

- `extends` creates inheritance
- `super()` calls parent constructor
- Child has access to parent methods
- Child can add own methods
- Child can override parent methods

---

# Interview tip

> Inheritance lets one class inherit from another using extends keyword. Use super() to call parent constructor. Child class gets all parent methods plus can add its own. This promotes code reuse."

# Q16: What is Polymorphism?

## How to Answer

> "Polymorphism means 'many forms' - the ability of different objects to respond to the same method call in different ways.

---

# Example

```javascript
class Animal {
  makeSound() {
    console.log('Some generic sound');
  }
}

class Dog extends Animal {
  makeSound() {
    console.log('Woof!');
  }
}

class Cat extends Animal {
  makeSound() {
    console.log('Meow!');
  }
}

const animals = [new Dog(), new Cat(), new Animal()];

// Same method call, different behavior
animals.forEach(animal => {
  animal.makeSound();
});
// Woof!
// Meow!
// Some generic sound
```

---

# Benefits

- Same interface, different implementations
- Easier to add new types
- More flexible code
- Treat objects generically

---

# Interview tip

> Polymorphism allows different classes to respond differently to the same method. Method overriding is a form of polymorphism - child class provides its own implementation of parent method."
