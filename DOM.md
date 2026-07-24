# 📘 JavaScript DOM Interview Notes


---

## Table of Contents


DOM (Document Object Model)
What is the DOM?
DOM Selection Methods
DOM Manipulation
Creating and Removing Elements
DOM Traversal
Attributes vs Properties


---

## Events:-

Event Handling
Event Bubbling and Capturing
Event Delegation
preventDefault and stopPropagation


---

## Browser APIs:-

Window vs Document
localStorage vs sessionStorage
Browser Navigation and History


---

## OOP Fundamentals:-

What is OOP?
Classes and Objects
Constructor Functions
The 'new' Keyword
Static Methods and Properties


---

## Prototypes & Inheritance:-

What is Prototype?
Prototype Chain
__proto__ vs prototype
Prototypal Inheritance
Classical vs Prototypal Inheritance


---

## Advanced OOP:-

Inheritance with Classes
Method Overriding
Private Fields and Methods
Getters and Setters
instanceof Operator


---

## OOP Principles:-

Encapsulation
Abstraction
Inheritance
Polymorphism


---

## Design Patterns:-

Module Pattern
Revealing Module Pattern
Singleton Pattern
Factory Pattern
Observer Pattern


---

## Q1: What is the DOM ?

"The DOM (Document Object Model) is a programming interface for HTML and XML documents. It represents the page as a tree structure where each HTML element is a node that can be accessed and manipulated using JavaScript.

Key points about DOM:

The DOM is NOT JavaScript - it's a Web API provided by the browser. JavaScript uses this API to interact with the webpage.

How DOM works:

When a browser loads an HTML page, it creates a DOM tree:

Document
  └── html
       ├── head
       │    ├── title
       │    └── meta
       └── body
            ├── h1
            ├── p
            └── div
                 ├── span
                 └── button

Each element, text, and attribute becomes a node in this tree.

Why DOM is important:

Dynamic manipulation - Change content without reloading page

Interactivity - Respond to user actions

Real-time updates - Update parts of page dynamically

Structure - Provides organized way to access elements


---

# Q2: What are the different ways to select DOM elements?

How to Answer:**

JavaScript provides several methods to select DOM elements, each with different use cases.

1. getElementById() - Select by ID:

Returns a single element with the specified ID. IDs should be unique.
const element = document.getElementById('myId');
When to use: When you have a unique ID and need that specific element.

2. getElementsByClassName() - Select by class:

Returns a live HTMLCollection of all elements with specified class.
const elements = document.getElementsByClassName('myClass');
When to use: When you need all elements with a specific class.

3. getElementsByTagName() - Select by tag:

Returns live HTMLCollection of all elements with specified tag name.

const paragraphs = document.getElementsByTagName('p');

const allElements = document.getElementsByTagName('*'); // All elements

When to use: When you need all elements of a specific type (all paragraphs, all divs, etc.).

4. querySelector() - CSS selector (single element):

Returns the FIRST element matching the CSS selector.

const element = document.querySelector('.myClass');

const element = document.querySelector('#myId');

const element = document.querySelector('div.container > p');

const element = document.querySelector('[data-id="123"]');

When to use: When you need the first matching element with complex selection criteria.

5. querySelectorAll() - CSS selector (all elements):

Returns a static NodeList of ALL elements matching the CSS selector.

const elements = document.querySelectorAll('.myClass');

const elements = document.querySelectorAll('div p'); // All p inside div

const elements = document.querySelectorAll('[data-active="true"]');

When to use: Modern approach for selecting multiple elements with complex criteria.


---

# Q3: How do you manipulate DOM elements?

How to Answer:**

"DOM manipulation means changing the content, structure, or styling of elements after the page has loaded. There are several ways to do this.

1. Changing content:

textContent - Plain text only:

const element = document.querySelector('p');

// Get text
console.log(element.textContent); // "Hello World"

// Set text (safe, no HTML parsing)

element.textContent = 'New text';

element.textContent = '<strong>Text</strong>'; // Shows literally, not as HTML

innerHTML - HTML content:

const element = document.querySelector('div');

// Get HTML

console.log(element.innerHTML); // "<p>Hello</p>"

// Set HTML (parses HTML)

element.innerHTML = '<p>New content</p>';

element.innerHTML = '<strong>Bold text</strong>'; // Renders as bold

innerText vs textContent:

// innerText - respects CSS styling (slower)

// textContent - returns all text, ignores styling (faster)

const element = document.querySelector('div');

element.innerHTML = '<span style="display:none">Hidden</span> Visible';

console.log(element.innerText); // 'Visible' (skips hidden)

console.log(element.textContent); // 'Hidden Visible' (includes hidden)


---

## Q4. DOM Manipulation: Create, Insert & Remove Elements


## 1. Create Element

Creates an element **in memory** (not visible until inserted into the DOM).

```js
const div = document.createElement("div");

div.textContent = "Hello";

div.classList.add("box");

div.setAttribute("id", "myDiv");
```

---

## 2. Insert Element

### `appendChild()` → Add as **last child**

```js
parent.appendChild(div);
```

### `append()` → Add multiple elements/text

```js
parent.append(div, "Hello");
```

### `prepend()` → Add as **first child**

```js
parent.prepend(div);
```

### `insertBefore()` → Insert before a specific child

```js
parent.insertBefore(newDiv, oldDiv);
```

### `insertAdjacentElement()`

```js
element.insertAdjacentElement("beforebegin", div);
element.insertAdjacentElement("afterbegin", div);
element.insertAdjacentElement("beforeend", div);
element.insertAdjacentElement("afterend", div);
```

### `insertAdjacentHTML()` → Insert HTML string

```js
element.insertAdjacentHTML("beforeend", "<p>Hello</p>");
```

---

## 3. Remove Element

### `remove()` (Modern)

```js
element.remove();
```

### `removeChild()`

```js
parent.removeChild(child);
```

### Remove all children

```js
parent.innerHTML = "";
// OR
parent.replaceChildren();
```

---

## 4. Replace Element

### `replaceChild()`

```js
parent.replaceChild(newNode, oldNode);
```

### `replaceWith()`

```js
oldNode.replaceWith(newNode);
```

# Interview Points
* `createElement()` → Creates element in memory.
* `appendChild()` → Adds last child.
* `append()` → Adds multiple elements/text.
* `prepend()` → Adds first child.
* `insertBefore()` → Inserts before a specific node.
* `remove()` → Removes element.
* `replaceWith()` → Replaces element.
* `cloneNode(true)` → Deep copy (includes children).
* `DocumentFragment` → Best for inserting many elements efficiently.



---

# Q5. What's the difference between attributes and properties?

How to Answer:**

"Attributes and properties are related but different concepts in the DOM. This is a common source of confusion.

Attributes:

Attributes are defined in HTML. They're the things you write in your HTML tags.

<input type="text" value="Hello" id="myInput" data-custom="123">

Here, type, value, id, and data-custom are attributes.

Properties:

Properties are defined on the DOM object in JavaScript. When the browser parses HTML, it creates DOM objects with properties.

Key differences:

1. Attributes are in HTML, Properties are in JavaScript:

<!-- Attribute in HTML -->
<input value="Hello">

// Property in JavaScript

const input = document.querySelector('input');

console.log(input.value); // Property

<input id="username" value="Hello">

here attributs are - value, id

now browser read the HTML and create a java script object(DOM Object)

input = {

   id: "username",

   value: "Hello"

}

this is not actual browser browser object only for understanding purpose.
id → Property

value → Property

for example-:

const input = document.querySelector("input");

console.log(input.id);     // Property

console.log(input.value);  // Property

input → DOM Object

id,value → Property

"Hello", "username" → Property ki current value

2. Attributes are always strings, Properties have types:

HTML stores attributes as text (strings).

checked is a boolean attribute. If it is present, the checkbox is initially checked.

<input type="checkbox" checked>

Property (JavaScript)

DOM properties can have different data types such as string, boolean, and number.

const input = document.querySelector("input");

console.log(input.checked);         // true

console.log(typeof input.checked);  // "boolean"


---

## Q6.How do you handle events in JavaScript?


What is an Event?

An event is an action performed by the user or browser.

such as:- click, submit, change,keydown, keyup, mouseover,load etc..

1.DOM Property

const btn = document.querySelector("button");

btn.onclick = () => {

  console.log("Clicked");

};

Limitation: Only one handler can be attached.

2. addEventListener() (Best Practice)

const btn = document.querySelector("button");

btn.addEventListener("click", () => {

  console.log("Clicked");

});

Advantages

Multiple listeners allowed.

Can remove listeners.

Cleaner and more flexible.

Event Object (event or e)

Automatically passed to the callback.

button.addEventListener("click", (e) => {

  console.log(e.target);

  console.log(e.type);

});

Common properties:

e.target → Element that triggered the event.

e.currentTarget → Element where listener is attached.

e.type → Event name.

e.preventDefault() → Stops browser's default behavior.

e.stopPropagation() → Stop event bubbling or Stops event from reaching parent elements.

7.Suppose You click a button… but suddenly another element also responds.

Why does this happen ? Did you think ?

The secret behind this behavior is JavaScript Event Flow — Bubbling, Capturing, and Delegation.

The question that arises in the mind here is this: What is an event flow ?

When you click any element on a webpage, JavaScript does NOT handle it directly.

It follows a path (flow).

Just imagine here ...I am going to click the button (child).

<div id="parent"> <button id="child">Click me</button> </div>

After my clicking ...Event Flow happens in 3 steps :-

➡️1- Capturing phase (Top → Bottom) ---→ Event travels DOWN toward the clicked element

➡️2- Target phase (Actual element) ---→ Event reaches the element I clicked.

➡️3- Bubbling phase (Bottom → Top) ---→ Event goes back up.

here we can imagine like a dropping ball...

The ball falls from the rooftop to the ground (capturing phase), hits the ground (target phase), and then moves back upward toward the rooftop (bubbling phase).

1.Q. What is Event Capturing ?

Event Capturing is a phase of JavaScript event flow where the event starts from the top (document) and moves down toward the target element Window

→ Document → Parent → Child (target element)

Now the question is ..How to enable capturing in JS,let's understand it :- By default, capturing is OFF.

We enable it using true:

What is the Syntax of capturing phase ? element.addEventListener(event, callback, true); event → the type of event (like "click", "mouseover") 

callback → function that runs when event happens true → enables capturing phase

HTML

<div id="grandparent"> <button id="parentBtn"> Parent Button <button id="childBtn">Child Button</button> </button> </div>

JAVA SCRIPT

document.getElementById("grandparent").addEventListener( "click", () => console.log("Grandparent clicked"), true );

document.getElementById("parentBtn").addEventListener( "click", () => console.log("Parent Button clicked"), true );

document.getElementById("childBtn").addEventListener( "click", () => console.log("Child Button clicked"), true );

when I click Child Button

Grandparent clicked 👈 first Parent Button clicked 👈 second Child Button clicked 👈 last

It rarely use.

2.Q. What is Event Bubbling ?
Event bubbling is the phase where an event starts from the target element (where you clicked) and then moves upwards to parent elements. Flow: Child → Parent → Grandparent → Document → Window

HTML

<div id="parent"> <button id="btn1">Button 1</button> <button id="btn2">Button 2</button> </div>

```javascript
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

when I click Button 1

Button 1 clicked 👈 first (target)

Parent clicked 👈 second (bubbling up)

when you click Button 2

Button 2 clicked 👈 first (target)

Parent clicked 👈 second (bubbling up)

Here, I have to add a separate event for each button. This is bad way to handle the event. Instead of writing separate code for each button, we use Event Delegation. so here Event Delegation comes in picture.

Q 3. Event Delegation ?

Basically Event delegation is NOT a phase — it is a technique using bubbling. In JavaScript where we attach a single event listener to a parent element instead of adding separate listeners to each child, It happens because of bubblling. Event propogate from child to parent.

HTML

<div id="parent"> <button id="btn1">Button 1</button> <button id="btn2">Button 2</button> <button id="btn3">Button 3</button> </div>

Java Script
```javascript
document.getElementById("parent").addEventListener("click", (e) => {
  if (e.target.tagName === "BUTTON") {
    console.log("Clicked:", e.target.id);
  }
});
```
If I click:

Button 1 → logs "btn1" Button 2 → logs "btn2" Button 3 → logs "btn3" Only one event listener on parent handles all buttons.

what is advantage of it ? 1. Better performance Instead of 100 buttons = 100 listeners --no

You use 1 parent listener -yes

2. Clean code Less repeated code, easier to manage.

3. Works for dynamic elements Even if new buttons are added later, it still works:



---

## Q7. What's the difference between preventDefault() and stopPropagation()?

preventDefault() prevents the browser's default action, while stopPropagation() prevents the event from propagating to parent elements. They solve different problems and are often used together.

1. preventDefault()

What it does

Cancels the browser's default behavior.

Common Use Cases

Prevent form submission

Prevent page reload

Prevent link navigation

Prevent context menu

Prevent drag & drop default behavior

<a href="https://google.com" id="link">Google</a>

const link = document.getElementById("link");

```link.addEventListener("click", (event) => {
  event.preventDefault();
  console.log("Navigation prevented");
});```

normal behavior google will open but after this preventDefault google will not open , only print navigation prevented.there are many example such as form , link etc

2. stopPropagation()

What it does

Stops the event from propagating (bubbling/capturing) to parent elements.

Common Use Cases

Nested buttons inside cards

Dropdown menus

Modal dialogs

Prevent parent click handlers from firing

Example

<div id="parent">
  <button id="child">Click Me</button>
</div> 

```parent.addEventListener("click", () => {
  console.log("Parent clicked");
});```

```child.addEventListener("click", () => {
  console.log("Child clicked");
});```

if will click on button then output will be - 

Child clicked

Parent clicked

but the question is who clicked parent, this happened becoz of bubbling - parent to child

```child.addEventListener("click", (event) => {
  event.stopPropagation();
  console.log("Child clicked");
});
```
now if i click on button then out is - Child clicked

This will:

Prevent the browser's default action.

Stop the event from reaching parent elements.