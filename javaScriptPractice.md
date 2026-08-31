# JavaScript Interview – Coding Based Questions

## Q1. `var` vs Block Scope

```js
var x = 10;

if (true) {
    var x = 20;
}

console.log(x);
```

**Question:** Output kya hoga aur kyun?

---

## Q2. `let` Block Scope

```js
let x = 10;

if (true) {
    let x = 20;
    console.log(x);
}

console.log(x);
```

**Question:** Output kya hoga?

---

## Q3. `var` Inside Loop

```js
for (var i = 0; i < 3; i++) {
    console.log(i);
}

console.log(i);
```

**Question:** Output kya hoga? `i` loop ke bahar kaise available hai?

---

## Q4. `let` Inside Loop

```js
for (let i = 0; i < 3; i++) {
    console.log(i);
}

console.log(i);
```

**Question:** Output kya hoga? Error kyun aayega?

---

## Q5. `const` with Object

```js
const user = {
    name: "Rahul",
    age: 25
};

user.age = 30;

console.log(user);
```

**Question:** Kya error aayega? Explain.

---

## Q6. `const` Reassignment

```js
const arr = [1, 2, 3];

arr = [4, 5, 6];

console.log(arr);
```

**Question:** Kya hoga aur kyun?

---

# Primitive vs Reference Types

## Q7. Primitive Value Copy

```js
let a = 10;
let b = a;

b = 20;

console.log(a);
console.log(b);
```

**Question:** Output kya hoga? Explain.

---

## Q8. Object Reference

```js
let obj1 = {
    name: "Rahul"
};

let obj2 = obj1;

obj2.name = "Gautam";

console.log(obj1.name);
console.log(obj2.name);
```

**Question:** Dono outputs same kyun hain?

---

## Q9. Array Reference

```js
let arr1 = [1, 2, 3];
let arr2 = arr1;

arr2.push(4);

console.log(arr1);
console.log(arr2);
```

**Question:** Output kya hoga?

---

## Q10. Array Comparison

```js
let a = [1, 2, 3];
let b = [1, 2, 3];

console.log(a === b);
```

**Question:** Output `false` kyun hai?

---

## Q11. Same Reference Comparison

```js
let a = [1, 2, 3];
let b = a;

console.log(a === b);
```

**Question:** Output kya hoga aur kyun?

---

# Undefined vs Null

## Q12. Undefined Variable

```js
let name;

console.log(name);
console.log(typeof name);
```

**Question:** Dono outputs kya honge?

---

## Q13. Missing Object Property

```js
const user = {
    name: "Rahul"
};

console.log(user.age);
```

**Question:** Output kya hoga? Error kyun nahi aata?

---

## Q14. Missing Function Argument

```js
function test(a) {
    console.log(a);
}

test();
```

**Question:** Output kya hoga?

---

## Q15. `null` vs `undefined`

```js
let a;
let b = null;

console.log(a == b);
console.log(a === b);
```

**Question:** Dono outputs kya honge? Explain.

---

## Q16. `typeof null`

```js
console.log(typeof null);
console.log(typeof undefined);
```

**Question:** Output kya hoga?

---

# NaN

## Q17. Creating NaN

```js
let x = "hello" * 10;

console.log(x);
console.log(typeof x);
```

**Question:** Output kya hoga?

---

## Q18. NaN Comparison

```js
let x = NaN;

console.log(x === NaN);
console.log(x == NaN);
```

**Question:** Dono `false` kyun hain?

---

## Q19. `isNaN()` vs `Number.isNaN()`

```js
console.log(isNaN("hello"));
console.log(Number.isNaN("hello"));

console.log(isNaN(NaN));
console.log(Number.isNaN(NaN));
```

**Question:** Output batao aur dono methods ka difference explain karo.

---

# Truthy and Falsy

## Q20. String `"0"`

```js
if ("0") {
    console.log("Hello");
} else {
    console.log("Bye");
}
```

**Question:** Output kya hoga? `"0"` truthy hai ya falsy?

---

## Q21. Empty Array

```js
if ([]) {
    console.log("True");
} else {
    console.log("False");
}
```

**Question:** Output kya hoga?

---

## Q22. Empty Object

```js
if ({}) {
    console.log("True");
} else {
    console.log("False");
}
```

**Question:** Output kya hoga?

---

## Q23. Boolean Conversion

```js
console.log(Boolean(0));
console.log(Boolean(""));
console.log(Boolean(" "));
console.log(Boolean(null));
console.log(Boolean(undefined));
console.log(Boolean([]));
console.log(Boolean({}));
console.log(Boolean("false"));
```

**Question:** Har line ka output batao.

---

## Q24. Logical OR `||`

```js
let username = "";

let result = username || "Guest";

console.log(result);
```

**Question:** `"Guest"` kyun print hoga?

---

# Mixed Interview Question

## Q25. JavaScript Concepts Mixed

```js
let a = 10;
let b = "10";
let c = null;
let d;
let e = NaN;
let f = [];
let g = {};

console.log(a == b);
console.log(a === b);
console.log(c == d);
console.log(c === d);
console.log(e === e);
console.log(Boolean(f));
console.log(Boolean(g));
```

**Question:**

1. Har line ka output batao.
2. `==` aur `===` ka difference explain karo.
3. `null == undefined` kyun `true` hai?
4. `NaN === NaN` kyun `false` hai?
5. Empty array aur empty object truthy kyun hain?

---

# Interview Practice Rules

For each question:

1. **Code ko dhyan se read karo**
2. **Output predict karo**
3. **Reason explain karo**
4. **Uske baad code run karke verify karo**

> **Interview Tip:** Sirf output mat bolo. Interviewer agar pooche **"Why?"**, to scope, reference, type coercion, truthy/falsy ya `NaN` ka concept explain karo.



## Topics Covered

* Truthy & Falsy Values
* `==` vs `===`
* Type Coercion
* Scope
* Lexical Scope
* Scope Chain
* Hoisting
* Temporal Dead Zone (TDZ)

> **Interview Rule:** Predict the output first. Do not run the code. Then explain **why**.

---

# Q5 – Truthy & Falsy Values

### Question 1

```js
if ("false") {
    console.log("A");
} else {
    console.log("B");
}
```

**Question:** What will be the output? Why?

---

### Question 2

```js
if ([]) {
    console.log("A");
}

if ({}) {
    console.log("B");
}

if ("") {
    console.log("C");
}
```

**Question:** What will be the output?

---

### Question 3

```js
console.log(Boolean(0));
console.log(Boolean("0"));
console.log(Boolean([]));
console.log(Boolean({}));
console.log(Boolean(null));
console.log(Boolean("false"));
```

**Question:** Predict the output of every line.

---

### Question 4

```js
let username = "";

let result = username || "Guest";

console.log(result);
```

**Question:** What will be the output? Why?

---

### Question 5

```js
let count = 0;

if (count) {
    console.log("Count exists");
} else {
    console.log("No count");
}
```

**Question:** What will be the output?

---

### Question 6 ⭐

```js
console.log(!!"");
console.log(!!"hello");
console.log(!![]);
console.log(!!{});
console.log(!!0);
console.log(!!1);
```

**Question:** Predict the output.

---

# Q6 – `==` vs `===`

### Question 7

```js
console.log(5 == "5");
console.log(5 === "5");
```

**Question:** What will be the output? Why?

---

### Question 8

```js
console.log(true == 1);
console.log(true === 1);

console.log(false == 0);
console.log(false === 0);
```

**Question:** Predict the output.

---

### Question 9

```js
console.log(null == undefined);
console.log(null === undefined);
```

**Question:** What will be the output?

---

### Question 10

```js
console.log("" == 0);
console.log("" === 0);

console.log("0" == 0);
console.log("0" === 0);
```

**Question:** Predict the output.

---

### Question 11 ⭐

```js
console.log(false == "0");
console.log(false === "0");
```

**Question:** What will be the output? Explain the type coercion.

---

### Question 12

```js
let a = [1, 2];
let b = [1, 2];

console.log(a == b);
console.log(a === b);
```

**Question:** Why are both results `false`?

---

### Question 13

```js
let a = [1, 2];
let b = a;

console.log(a == b);
console.log(a === b);
```

**Question:** What will be the output? Why?

---

### Question 14 ⭐

```js
console.log("" == 0);
console.log(0 == "0");
console.log("" == "0");
```

**Question:** Predict the output.

---

# Q7 – Type Coercion

### Question 15

```js
console.log("5" + 2);
console.log("5" - 2);
console.log("5" * 2);
console.log("5" / 2);
```

**Question:** Predict the output of every line.

---

### Question 16

```js
console.log(1 + "2" + 3);
console.log(1 + 2 + "3");
console.log("1" + 2 + 3);
```

**Question:** What will be the output?

---

### Question 17 ⭐

```js
console.log(true + true);
console.log(true + false);
console.log(false + false);
```

**Question:** Predict the output.

---

### Question 18

```js
console.log("10" - null);
console.log("10" + null);
console.log("10" - undefined);
```

**Question:** What will be the output? Why?

---

### Question 19

```js
console.log(Number("10"));
console.log(Number("hello"));
console.log(Number(null));
console.log(Number(undefined));
console.log(Number(true));
console.log(Number(false));
```

**Question:** Predict the output.

---

### Question 20

```js
console.log(String(100));
console.log(String(true));
console.log(String(null));

console.log(Boolean(0));
console.log(Boolean("hello"));
```

**Question:** What will be the output?

---

### Question 21 ⭐

```js
console.log([] + []);
console.log([] + {});
console.log([1, 2] + [3, 4]);
```

**Question:** Predict the output.

---

### Question 22

```js
console.log("5" + 3 - 2);
```

**Question:** What will be the output?

**Hint:** Think about left-to-right evaluation and type coercion.

---

# Q8 – Scope

### Question 23

```js
var x = 10;

if (true) {
    var x = 20;
}

console.log(x);
```

**Question:** What will be the output? Why?

---

### Question 24

```js
let x = 10;

if (true) {
    let x = 20;
    console.log(x);
}

console.log(x);
```

**Question:** What will be the output?

---

### Question 25

```js
if (true) {
    var a = 10;
    let b = 20;
}

console.log(a);
console.log(b);
```

**Question:** What will happen? Where will the error occur?

---

### Question 26

```js
var x = 10;

function test() {
    var x = 20;
    console.log(x);
}

test();

console.log(x);
```

**Question:** Predict the output.

---

### Question 27

```js
let x = 10;

function test() {
    let x = 20;

    if (true) {
        let x = 30;
        console.log(x);
    }

    console.log(x);
}

test();

console.log(x);
```

**Question:** What will be the output?

---

### Question 28 ⭐

```js
var x = 10;

function test() {
    var x = 20;

    if (true) {
        var x = 30;
        console.log(x);
    }

    console.log(x);
}

test();

console.log(x);
```

**Question:** Predict the output and explain why.

---

# Q9 – Lexical Scope & Scope Chain

### Question 29

```js
let x = "global";

function outer() {
    let x = "outer";

    function inner() {
        console.log(x);
    }

    inner();
}

outer();
```

**Question:** What will be the output?

---

### Question 30 ⭐

```js
let x = "global";

function first() {
    let x = "first";
    second();
}

function second() {
    console.log(x);
}

first();
```

**Question:** What will be the output?

**Important:** `second()` is called from inside `first()`. Will `second()` access `"first"`?

---

### Question 31

```js
let a = 10;

function outer() {
    let b = 20;

    function inner() {
        let c = 30;

        console.log(a);
        console.log(b);
        console.log(c);
    }

    inner();
}

outer();
```

**Question:** Are all three variables accessible? Why?

---

### Question 32

```js
let a = 10;

function outer() {
    let b = 20;

    function inner() {
        console.log(a + b);
    }

    inner();
}

outer();
```

**Question:** What will be the output?

---

### Question 33 ⭐

```js
let x = 10;

function outer() {
    let x = 20;

    function inner() {
        let x = 30;
        console.log(x);
    }

    inner();
    console.log(x);
}

outer();

console.log(x);
```

**Question:** Predict the output.

---

### Question 34

```js
function outer() {
    let message = "Hello";

    function inner() {
        console.log(message);
    }

    return inner;
}

const fn = outer();

fn();
```

**Question:** Why can `fn()` still access `message` after `outer()` has finished executing?

---

# Q10 – Hoisting

### Question 35

```js
console.log(x);

var x = 10;
```

**Question:** What will be the output?

---

### Question 36

```js
console.log(x);

let x = 10;
```

**Question:** Will this print a value or throw an error?

---

### Question 37

```js
console.log(x);

const x = 10;
```

**Question:** What will happen?

---

### Question 38 ⭐

```js
hello();

function hello() {
    console.log("Hello");
}
```

**Question:** What will be the output? Why does this work?

---

### Question 39

```js
hello();

var hello = function () {
    console.log("Hello");
};
```

**Question:** What will happen? What type of error will occur?

---

### Question 40

```js
hello();

let hello = function () {
    console.log("Hello");
};
```

**Question:** What will happen? Why?

---

### Question 41 ⭐

```js
console.log(a);

var a = 10;

console.log(a);
```

**Question:** What will both `console.log()` statements output?

---

### Question 42

```js
function test() {
    console.log(a);

    var a = 20;

    console.log(a);
}

test();
```

**Question:** Predict the output.

---

### Question 43

```js
function test() {
    console.log(a);

    let a = 20;
}

test();
```

**Question:** What will happen? Explain the TDZ.

---

### Question 44 ⭐

```js
console.log(foo);

var foo = "bar";

function foo() {
    console.log("function");
}
```

**Question:** What will be the output?

**Bonus:** Explain how function declaration and `var` interact during hoisting.

---

# 🔥 Mixed Interview Questions

## Question 45 – Scope + Hoisting

```js
var x = 10;

function test() {
    console.log(x);

    var x = 20;

    console.log(x);
}

test();
```

**Question:** What will be the output? Explain why.

---

## Question 46 – Scope + `let`

```js
let x = 10;

function test() {
    console.log(x);

    let x = 20;
}

test();
```

**Question:** What will happen? Why?

---

## Question 47 – Hoisting + Function Expression

```js
test();

var test = function () {
    console.log("Hello");
};
```

**Question:** What will happen? Why?

---

## Question 48 – Truthy + Type Coercion

```js
let x = "0";

if (x) {
    console.log("Truthy");
} else {
    console.log("Falsy");
}
```

**Question:** What will be the output? Why?

---

## Question 49 – Equality + Coercion

```js
let a = 0;
let b = false;
let c = "0";

console.log(a == b);
console.log(b == c);
console.log(a === c);
```

**Question:** Predict the output of every line.

---

# Question 50 ⭐⭐⭐ – Complete Interview Challenge

```js
var x = 10;

function test() {
    console.log(x);

    var x = 20;

    if (true) {
        var x = 30;
        console.log(x);
    }

    console.log(x);
}

test();

console.log(x);
```

### Interview Questions

1. What is the output of every `console.log()`?
2. How many `x` variables are actually created?
3. What is the scope of `var x` inside `test()`?
4. How does hoisting work here?
5. Why does the global `x` remain `10`?
6. Why does the `if` block not create a separate `x` for `var`?

---

## Interview Approach

For every coding question, answer in this order:

1. **Predict the output**
2. **Explain the reason**
3. **Mention the JavaScript concept**
4. **If there is an error, identify the error type**

Example:

> **Output:** `20`
> **Reason:** `var` is function-scoped, so the `var x` inside the `if` refers to the same variable within the function.


