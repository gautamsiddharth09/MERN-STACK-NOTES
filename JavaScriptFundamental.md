
# Table of Contents 

1. Variables & Data Types
2. Type Coercion & Conversion
3. Scope & Context
4. Hoisting & TDZ
5. Closures
6. `this` Keyword
7. `call()`, `apply()`, `bind()`
8. Objects
9. Functions
10. Arrays
11. Strings & Numbers
12. Map & Set
13. Miscellaneous Concepts


# Variables & Data Types

## Q1: What are the differences between var, let, and const?

### How to Answer:

"There are three ways to declare variables in JavaScript: var, let, and const. They differ in scope, hoisting behavior, and reassignment.

### var:

- Function-scoped or globally-scoped
- Can be re-declared and updated
- Hoisted and initialized with undefined
- Creates property on window object (in browsers)

```javascript
var x = 10;
var x = 20; // No error, re-declaration allowed
x = 30;     // Update allowed

function test() {
  var y = 5;
  if (true) {
    var y = 10; // Same variable, function-scoped
    console.log(y); // 10
  }
  console.log(y); // 10
}
```

### let:

- Block-scoped (within {})
- Cannot be re-declared in same scope
- Can be updated
- Hoisted but not initialized (TDZ)
- Does not create window property

```javascript
let a = 10;
// let a = 20; // Error: cannot re-declare
a = 30; // Update allowed

if (true) {
  let b = 5;
  console.log(b); // 5
}
// console.log(b); // Error: b is not defined
```

### const:

- Block-scoped
- Cannot be re-declared or updated
- Must be initialized at declaration
- Hoisted but not initialized (TDZ)
- For objects/arrays, reference is constant but content can change

```javascript
const PI = 3.14;
// PI = 3.15; // Error: cannot reassign

const obj = { name: 'John' };
obj.name = 'Jane'; // Allowed, modifying content
// obj = {}; // Error: cannot reassign reference

const arr = [1, 2, 3];
arr.push(4); // Allowed
// arr = []; // Error
```

### Key differences summary:

| Feature | var | let | const |
|---------|-----|-----|-------|
| Scope | Function | Block | Block |
| Re-declaration | Yes | No | No |
| Update | Yes | Yes | No |
| Hoisting | Yes, initialized | Yes, not initialized | Yes, not initialized |
| TDZ | No | Yes | Yes |

Best practice: Use const by default, use let when you need to reassign, avoid var in modern code."

---

## Q2: What are primitive and reference data types?

### How to Answer:

"JavaScript has two categories of data types: primitive and reference types. The main difference is how they're stored in memory and how they behave when assigned or passed around.

### Primitive Types (7 types):

- String
- Number
- Boolean
- Undefined
- Null
- Symbol
- BigInt

Primitives are stored directly in the variable's memory location. When you copy a primitive, you copy the actual value.

```javascript
let a = 10;
let b = a; // Copy of value
b = 20;

console.log(a); // 10 (unchanged)
console.log(b); // 20
```

### Reference Types:

- Objects
- Arrays
- Functions

Reference types store a reference (memory address) to the actual data. When you copy a reference type, you copy the reference, not the actual data.

```javascript
let obj1 = { name: 'John' };
let obj2 = obj1; // Copy of reference
obj2.name = 'Jane';

console.log(obj1.name); // 'Jane' (changed!)
console.log(obj2.name); // 'Jane'
// Both point to same object in memory
```

### Why this matters:

#### Comparison:

```javascript
// Primitives: compare by value
let x = 5;
let y = 5;
console.log(x === y); // true

// References: compare by reference
let arr1 = [1, 2, 3];
let arr2 = [1, 2, 3];
console.log(arr1 === arr2); // false (different references)

let arr3 = arr1;
console.log(arr1 === arr3); // true (same reference)
```

#### Passing to functions:

```javascript
function changePrimitive(num) {
  num = 100;
}

function changeReference(obj) {
  obj.name = 'Changed';
}

let number = 50;
changePrimitive(number);
console.log(number); // 50 (unchanged)

let person = { name: 'John' };
changeReference(person);
console.log(person.name); // 'Changed'
```

### Memory perspective:

Primitives are stored on the stack (faster, fixed size). References are stored on the heap (slower, dynamic size), and the variable holds a pointer to that memory location.

This is fundamental to understanding how JavaScript handles data."


## Q3: What is the difference between undefined and null?

### How to Answer:

"Both undefined and null represent 'no value,' but they have different meanings and use cases.

### undefined:

- Automatically assigned by JavaScript when a variable is declared but not initialized
- Means 'variable exists but has no value yet'
- It's a type itself: typeof undefined is 'undefined'

```javascript
let x;
console.log(x); // undefined

function test(param) {
  console.log(param); // undefined if no argument passed
}
test();

let obj = { name: 'John' };
console.log(obj.age); // undefined (property doesn't exist)
```

### null:

- Explicitly assigned by the developer
- Means 'intentionally no value' or 'empty'
- It's an object type (this is actually a bug in JavaScript): typeof null is 'object'

```javascript
let x = null; // Developer intentionally set it to null

function findUser(id) {
  // If user not found, return null
  return null;
}
```

### Key differences:

| Feature | undefined | null |
|---------|-----------|------|
| Meaning | Absence of value | Intentional absence |
| Type | undefined | object (bug) |
| Assignment | Automatic | Manual |
| Default | Function params, declared vars | Never default |

### Comparison:

```javascript
undefined == null   // true (loose equality)
undefined === null  // false (strict equality)

typeof undefined    // 'undefined'
typeof null         // 'object'
```

### When to use:

- undefined: Let JavaScript assign it naturally (don't manually set variables to undefined)
- null: Use when you want to explicitly indicate 'no value' or reset an object reference

### Common scenario:

```javascript
let user = null; // No user logged in yet

function login(userData) {
  user = userData; // Now has value
}

function logout() {
  user = null; // Explicitly clearing the user
}
```

The key point is: undefined is JavaScript's way of saying 'I don't know about this yet,' while null is your way of saying 'I know about this, and it's intentionally empty.'"

---

## Q4: What is NaN and how do you check for it?

### How to Answer:

"NaN stands for 'Not a Number,' but ironically, its type is 'number'. It represents a computational error when trying to perform a mathematical operation that doesn't result in a valid number.

### How NaN is created:

```javascript
console.log(0 / 0);              // NaN
console.log(Math.sqrt(-1));      // NaN
console.log(parseInt('hello'));  // NaN
console.log('text' * 5);         // NaN
console.log(undefined + 5);      // NaN
```

### Special property of NaN:

NaN is the only value in JavaScript that's not equal to itself:

```javascript
console.log(NaN === NaN);  // false
console.log(NaN == NaN);   // false
```

This is why you can't check for NaN using equality operators.

### How to check for NaN:

#### Method 1: isNaN() function (be careful!)

```javascript
isNaN(NaN);        // true
isNaN('hello');    // true (converts string to number first)
isNaN(undefined);  // true
isNaN({});         // true
isNaN(123);        // false
```

The problem with isNaN() is that it first tries to convert the value to a number, which can give unexpected results.

#### Method 2: Number.isNaN() (recommended)

```javascript
Number.isNaN(NaN);        // true
Number.isNaN('hello');    // false (doesn't convert)
Number.isNaN(undefined);  // false
Number.isNaN(123);        // false
```

Number.isNaN() is more reliable because it doesn't do type coercion. It only returns true if the value is actually NaN.

#### Method 3: Using the self-inequality property

```javascript
function isReallyNaN(value) {
  return value !== value;
}

isReallyNaN(NaN);  // true
isReallyNaN(5);    // false
```

This works because only NaN is not equal to itself.

### Practical example:

```javascript
function safeDivide(a, b) {
  const result = a / b;

  if (Number.isNaN(result)) {
    return 'Cannot divide';
  }

  return result;
}

console.log(safeDivide(10, 2));  // 5
console.log(safeDivide(0, 0));   // 'Cannot divide'
```

### Important points:

- typeof NaN is 'number'
- NaN is 'falsy' value
- Any operation with NaN results in NaN
- Use Number.isNaN() for reliable checking

Remember: NaN means 'invalid number,' not 'not a number'."

## Q5: What are truthy and falsy values in JavaScript?

### How to Answer:

"In JavaScript, every value has an inherent boolean nature - it's either truthy or falsy. This matters in conditions like if statements, where JavaScript automatically converts values to boolean.

### Falsy values (only 8):

There are exactly 8 falsy values in JavaScript:

- false - the boolean false
- 0 - the number zero
- -0 - negative zero
- 0n - BigInt zero
- '' (empty string) - empty string
- null - null
- undefined - undefined
- NaN - Not a Number

```javascript
if (false) { }        // Won't execute
if (0) { }            // Won't execute
if ('') { }           // Won't execute
if (null) { }         // Won't execute
if (undefined) { }    // Won't execute
if (NaN) { }          // Won't execute
```

### Everything else is truthy:

This includes:

- All numbers except 0 (including negative numbers)
- All strings with content (even '0' and 'false')
- All objects (even empty objects {})
- All arrays (even empty arrays [])
- All functions

```javascript
if (1) { }              // Executes (truthy)
if (-5) { }             // Executes (truthy)
if ('hello') { }        // Executes (truthy)
if ('0') { }            // Executes (truthy string!)
if ('false') { }        // Executes (truthy string!)
if ({}) { }             // Executes (truthy object!)
if ([]) { }             // Executes (truthy array!)
if (function() {}) { }  // Executes (truthy)
```

### Common gotchas:

```javascript
// These are truthy (surprising to many):
if ('0') { }        // Executes (non-empty string)
if ('false') { }    // Executes (non-empty string)
if ([]) { }         // Executes (object exists)
if ({}) { }         // Executes (object exists)

// These are falsy:
if (0) { }          // Doesn't execute
if ('') { }         // Doesn't execute
```

### Practical usage:

#### Default values:

```javascript
function greet(name) {
  name = name || 'Guest'; // If name is falsy, use 'Guest'
  console.log('Hello ' + name);
}

greet('John');  // Hello John
greet('');      // Hello Guest (empty string is falsy)
greet();        // Hello Guest (undefined is falsy)
```

#### Checking if value exists:

```javascript
function processData(data) {
  if (!data) {
    console.log('No data provided');
    return;
  }
  // Process data
}
```

#### Boolean conversion:

You can explicitly convert to boolean:

```javascript
Boolean(0);           // false
Boolean('');          // false
Boolean('hello');     // true
Boolean([]);          // true

// Double negation trick
!!0;                  // false
!!'hello';            // true
!![];                 // true
```

Interview tip: When asked about truthy/falsy, remember the 8 falsy values - everything else is truthy. This is a very common interview question!"


## Q5: What are truthy and falsy values in JavaScript?

### How to Answer:

"In JavaScript, every value has an inherent boolean nature - it's either truthy or falsy. This matters in conditions like if statements, where JavaScript automatically converts values to boolean.

### Falsy values (only 8):

There are exactly 8 falsy values in JavaScript:

- false - the boolean false
- 0 - the number zero
- -0 - negative zero
- 0n - BigInt zero
- '' (empty string) - empty string
- null - null
- undefined - undefined
- NaN - Not a Number

```javascript
if (false) { }        // Won't execute
if (0) { }            // Won't execute
if ('') { }           // Won't execute
if (null) { }         // Won't execute
if (undefined) { }    // Won't execute
if (NaN) { }          // Won't execute
```

### Everything else is truthy:

This includes:

- All numbers except 0 (including negative numbers)
- All strings with content (even '0' and 'false')
- All objects (even empty objects {})
- All arrays (even empty arrays [])
- All functions

```javascript
if (1) { }              // Executes (truthy)
if (-5) { }             // Executes (truthy)
if ('hello') { }        // Executes (truthy)
if ('0') { }            // Executes (truthy string!)
if ('false') { }        // Executes (truthy string!)
if ({}) { }             // Executes (truthy object!)
if ([]) { }             // Executes (truthy array!)
if (function() {}) { }  // Executes (truthy)
```

### Common gotchas:

```javascript
// These are truthy (surprising to many):
if ('0') { }        // Executes (non-empty string)
if ('false') { }    // Executes (non-empty string)
if ([]) { }         // Executes (object exists)
if ({}) { }         // Executes (object exists)

// These are falsy:
if (0) { }          // Doesn't execute
if ('') { }         // Doesn't execute
```

### Practical usage:

#### Default values:

```javascript
function greet(name) {
  name = name || 'Guest'; // If name is falsy, use 'Guest'
  console.log('Hello ' + name);
}

greet('John');  // Hello John
greet('');      // Hello Guest (empty string is falsy)
greet();        // Hello Guest (undefined is falsy)
```

#### Checking if value exists:

```javascript
function processData(data) {
  if (!data) {
    console.log('No data provided');
    return;
  }
  // Process data
}
```

#### Boolean conversion:

You can explicitly convert to boolean:

```javascript
Boolean(0);           // false
Boolean('');          // false
Boolean('hello');     // true
Boolean([]);          // true

// Double negation trick
!!0;                  // false
!!'hello';            // true
!![];                 // true
```

Interview tip: When asked about truthy/falsy, remember the 8 falsy values - everything else is truthy. This is a very common interview question!"

---

## Q6: What's the difference between == and ===?

### How to Answer:

"The difference between == (loose equality) and === (strict equality) is that == does type coercion while === does not.

### === (Strict Equality):

- Compares both value AND type
- No type conversion happens
- More predictable and recommended

```javascript
5 === 5            // true (same value, same type)
5 === '5'          // false (same value, different type)
true === 1         // false (different types)
null === undefined // false (different types)
```

### == (Loose Equality):

- Compares only value
- Does type coercion (converts types to match)
- Can have unexpected results

```javascript
5 == 5             // true
5 == '5'           // true (string '5' converted to number)
true == 1          // true (true converted to 1)
false == 0         // true (false converted to 0)
null == undefined  // true (special case)
'' == 0            // true (empty string to number)
```

### How type coercion works with ==:

JavaScript follows specific rules to convert types:

- If comparing null and undefined, return true
- If comparing number and string, convert string to number
- If comparing boolean with anything, convert boolean to number
- If comparing object with primitive, convert object to primitive

### Weird behaviors with ==:

```javascript
// These are all true with ==
'' == 0              // true
'0' == 0             // true
false == '0'         // true
false == ''          // true
[] == ''             // true
[] == 0              // true
[''] == ''           // true

// But look at this inconsistency:
'' == '0'            // false!
```

### Transitive property doesn't work:

```javascript
// A == B and B == C, but A != C
'' == 0        // true
0 == '0'       // true
'' == '0'      // false (inconsistent!)
```

### When == does work as expected:

```javascript
null == undefined      // true (only case where this matters)
null == null           // true
undefined == undefined // true

// For checking null or undefined:
if (value == null) {
  // True if value is null OR undefined
}

// Equivalent to:
if (value === null || value === undefined) {
}
```

### Best practices:

Always use === and !== by default:

```javascript
// Good
if (value === 0) { }
if (name === 'John') { }
if (result !== null) { }

// Avoid
if (value == 0) { }  // Unclear intent
```

### Exception: Checking for null/undefined:

```javascript
// This is the only acceptable use of ==
if (value == null) {
  // Handles both null and undefined
}
```

### Object comparison:

Both == and === compare objects by reference, not by content:

```javascript
let obj1 = { a: 1 };
let obj2 = { a: 1 };
let obj3 = obj1;

obj1 == obj2   // false (different objects)
obj1 === obj2  // false (different objects)
obj1 === obj3  // true (same reference)
```

### Summary:

- === checks value and type (strict)
- == checks only value with type coercion (loose)
- Use === for clarity and avoiding bugs
- Only use == for null/undefined checks

Remember: Using === makes your intent clear and prevents confusing bugs!"

---

# Type Coercion & Conversion

## Q7: What is type coercion? Explain implicit vs explicit conversion.

### How to Answer:

"Type coercion is the automatic or manual conversion of values from one data type to another. JavaScript does this in two ways: implicit (automatic) and explicit (manual).

### Implicit Coercion (Automatic):

JavaScript automatically converts types when needed:

#### String coercion (with + operator):

```javascript
'5' + 3        // '53' (number to string)
'Hello' + true // 'Hellotrue' (boolean to string)
'10' + null    // '10null' (null to string)
```

#### *Number coercion (with -, , /, operators):

```javascript
'5' - 2        // 3 (string to number)
'10' * '2'     // 20 (both to numbers)
'20' / '5'     // 4 (both to numbers)
true + 5       // 6 (true becomes 1)
false + 10     // 10 (false becomes 0)
```

#### Boolean coercion (in conditions):

```javascript
if ('hello') { }  // String to boolean (truthy)
if (0) { }        // Number to boolean (falsy)
if ([]) { }       // Array to boolean (truthy)
```

#### Equality coercion (with ==):

```javascript
5 == '5'          // true (string to number)
true == 1         // true (boolean to number)
false == 0        // true (boolean to number)
null == undefined // true (special case)
```

### Explicit Conversion (Manual):

You explicitly tell JavaScript to convert:

#### To String:

```javascript
String(123)           // '123'
String(true)          // 'true'
String(null)          // 'null'
(123).toString()      // '123'
123 + ''              // '123' (trick)
```

#### To Number:

```javascript
Number('123')         // 123
Number('12.5')        // 12.5
Number('hello')       // NaN
Number(true)          // 1
Number(false)         // 0
Number(null)          // 0
Number(undefined)     // NaN

parseInt('123')       // 123
parseFloat('12.5')    // 12.5
+'123'                // 123 (unary plus trick)
```

#### To Boolean:

```javascript
Boolean(1)            // true
Boolean(0)            // false
Boolean('hello')      // true
Boolean('')           // false
!!value               // Boolean conversion trick
```

### Common coercion scenarios:

#### Addition (+) is special:

```javascript
// If any operand is string, concatenate
1 + '2'        // '12'
'1' + 2        // '12'
'Hello' + 5    // 'Hello5'

// Both numbers: add
1 + 2          // 3
```

#### Other operators always convert to number:

```javascript
'10' - '5'     // 5
'10' * '2'     // 20
'20' / '4'     // 5
'5' > '2'      // true (compared as numbers)
```

#### Array coercion:

```javascript
[1, 2] + [3, 4]      // '1,23,4' (arrays to strings)
[] + []              // '' (empty strings)
[] + {}              // '[object Object]'
{} + []              // 0 (in some contexts)
```

#### Object to Primitive:

When object is coerced to primitive:

- Calls valueOf()
- If not primitive, calls toString()
- If still not primitive, throws error

```javascript
let obj = {
  valueOf() { return 42; },
  toString() { return 'hello'; }
};

obj + 10     // 52 (valueOf used)
String(obj)  // 'hello' (toString used)
```

### Tricky examples:

```javascript
true + false           // 1 (1 + 0)
'5' + null             // '5null'
'5' - null             // 5 (null becomes 0)
'hello' - 1            // NaN
[] + {}                // '[object Object]'
{} + []                // 0 (in some contexts)
!!'false'              // true (non-empty string)
!![]                   // true (truthy)
```

### Best practices:

- Prefer explicit conversion for clarity
- Use === instead of == to avoid coercion
- Be careful with + (use for addition or concatenation, not both)
- Know the falsy values to predict boolean coercion

Interview tip: Coercion questions are very common. Remember: + prefers strings, other operators prefer numbers, and know your falsy values!"


# Scope & Context

## Q8: What is scope in JavaScript? Explain global, function, and block scope.

### How to Answer:

"Scope determines where variables are accessible in your code. It's like asking 'where can I use this variable?' JavaScript has three types of scope: global, function, and block scope.

### Global Scope:

Variables declared outside any function or block are in global scope. They're accessible everywhere in your code.

```javascript
var globalVar = 'I am global';
let globalLet = 'Also global';

function test() {
  console.log(globalVar);  // Accessible
  console.log(globalLet);  // Accessible
}

if (true) {
  console.log(globalVar);  // Accessible
}
```

In browsers, global variables become properties of the window object:

```javascript
var x = 10;
console.log(window.x); // 10 (with var)

let y = 20;
console.log(window.y); // undefined (let doesn't attach to window)
```

### Function Scope:

Variables declared inside a function are only accessible within that function. This applies to var, let, and const.

```javascript
function myFunction() {
  var functionVar = 'I am function-scoped';
  let functionLet = 'Me too';
  
  console.log(functionVar);  // Accessible here
  console.log(functionLet);  // Accessible here
}

myFunction();
// console.log(functionVar);  // Error: not defined
// console.log(functionLet);  // Error: not defined
```

### Block Scope:

Block scope is created by curly braces {}. let and const are block-scoped, but var is NOT.

```javascript
if (true) {
  var blockVar = 'var is NOT block-scoped';
  let blockLet = 'let IS block-scoped';
  const blockConst = 'const IS block-scoped';
  
  console.log(blockVar);    // Accessible
  console.log(blockLet);    // Accessible
  console.log(blockConst);  // Accessible
}

console.log(blockVar);    // Accessible (var ignores block)
// console.log(blockLet);    // Error: not defined
// console.log(blockConst);  // Error: not defined
```

### Key difference between var and let/const:

```javascript
// var is function-scoped
for (var i = 0; i < 3; i++) {
  // i is accessible
}
console.log(i); // 3 (still accessible!)

// let is block-scoped
for (let j = 0; j < 3; j++) {
  // j is accessible
}
// console.log(j); // Error: j is not defined
```

### Nested scope (Scope Chain):

Inner scopes can access outer scopes, but not vice versa:

```javascript
var global = 'global';

function outer() {
  var outerVar = 'outer';
  
  function inner() {
    var innerVar = 'inner';
    
    console.log(global);    // Accessible
    console.log(outerVar);  // Accessible
    console.log(innerVar);  // Accessible
  }
  
  inner();
  // console.log(innerVar);  // Error: not accessible
}

outer();
// console.log(outerVar);  // Error: not accessible
```

### Visual representation:

```text
Global Scope
  │
  ├── Variable: globalVar
  │
  └── Function Scope (myFunction)
        │
        ├── Variable: functionVar
        │
        └── Block Scope (if statement)
              │
              └── Variable: blockLet
```

### Scope rules summary:

| Variable Type | Function Scope | Block Scope | Global Scope |
|---------------|----------------|-------------|--------------|
| var | Yes | No | Yes |
| let | Yes | Yes | Yes |
| const | Yes | Yes | Yes |

### Best practice:

- Use let and const (block-scoped) instead of var
- Keep variables in smallest scope possible
- Avoid polluting global scope

Understanding scope is crucial for avoiding variable naming conflicts and understanding closure!"

---

## Q9: What is lexical scope and scope chain?

### How to Answer:

"Lexical scope means that the accessibility of variables is determined by where they are written in the code, not where they are called from. It's also called static scope because it's fixed at the time you write your code.

### Lexical Scope:

The word 'lexical' refers to the fact that lexical scoping uses the location where a variable is declared in the source code to determine where that variable is available.

```javascript
function outer() {
  let outerVar = 'I am outer';
  
  function inner() {
    console.log(outerVar); // Can access outerVar
  }
  
  inner();
}

outer(); // 'I am outer'
```

The inner function can access outerVar because of where it's written (lexically), not where it's called.

### Key point about lexical scope:

```javascript
let name = 'Global';

function first() {
  let name = 'First';
  second();
}

function second() {
  console.log(name); // Which name?
}

first(); // Logs 'Global', not 'First'!
```

Even though second() is called from inside first(), it logs 'Global' because second() was defined in the global scope, not inside first(). This is lexical scoping - where the function is written matters, not where it's called.

### Scope Chain:

The scope chain is the mechanism JavaScript uses to resolve variable names. When you reference a variable, JavaScript looks:

1. First in the current scope
2. Then in the parent scope
3. Then in the parent's parent scope
4. And so on, until it reaches global scope
5. If not found anywhere, ReferenceError

```javascript
let global = 'global';

function level1() {
  let level1Var = 'level1';
  
  function level2() {
    let level2Var = 'level2';
    
    function level3() {
      let level3Var = 'level3';
      
      // Can access all:
      console.log(level3Var); // Found in level3
      console.log(level2Var); // Found in level2 (parent)
      console.log(level1Var); // Found in level1 (grandparent)
      console.log(global);    // Found in global (root)
    }
    
    level3();
  }
  
  level2();
}

level1();
```

### Visual Scope Chain:

```text
Global Scope { global }
    ↑
    │ (chain)
level1 Scope { level1Var }
    ↑
    │ (chain)
level2 Scope { level2Var }
    ↑
    │ (chain)
level3 Scope { level3Var }
```

When level3 needs a variable, it searches up this chain.

### Shadowing:

When inner scope has a variable with the same name as outer scope:

```javascript
let x = 10; // Global

function test() {
  let x = 20; // Shadows global x
  
  console.log(x); // 20 (uses local x)
  
  function inner() {
    let x = 30; // Shadows test's x
    console.log(x); // 30 (uses inner's x)
  }
  
  inner();
  console.log(x); // 20 (back to test's x)
}

test();
console.log(x); // 10 (global x)
```

Each x 'shadows' the outer x. JavaScript stops searching the chain once it finds the first match.

### Scope chain with closures:

```javascript
function makeCounter() {
  let count = 0;
  
  return function() {
    count++;
    return count;
  };
}

const counter = makeCounter();
console.log(counter()); // 1
console.log(counter()); // 2
```

The returned function maintains access to count through the scope chain, even after makeCounter has finished executing. This is closure.

### Important characteristics:

- Determined at write time: Scope is set when code is written, not when executed
- One-way street: Inner can access outer, but not vice versa
- Stops at first match: Once variable is found, search stops
- Preserved by closures: Functions remember their scope chain

Interview insight: Lexical scope + scope chain explains how closures work. The inner function 'closes over' variables from its lexical scope, creating the closure."



# Hoisting & TDZ

## Q10: What is hoisting in JavaScript?

### How to Answer:

"Hoisting is JavaScript's default behavior of moving declarations to the top of their scope before code execution. It's important to understand that only declarations are hoisted, not initializations.

Think of it like this: JavaScript reads through your code twice - first to set up declarations, then to execute the code.

### Function declarations are fully hoisted:

```javascript
// You can call the function before it's defined
greet(); // Works! Logs 'Hello'

function greet() {
  console.log('Hello');
}
```

JavaScript internally does this:

```javascript
// Behind the scenes:
function greet() {
  console.log('Hello');
}

greet(); // Now it makes sense
```

### var declarations are hoisted but not initialized:

```javascript
console.log(x); // undefined (not an error!)
var x = 5;
console.log(x); // 5
```

JavaScript internally does this:

```javascript
// Behind the scenes:
var x;           // Declaration hoisted
console.log(x);  // undefined
x = 5;           // Initialization stays in place
console.log(x);  // 5
```

### let and const are hoisted but not initialized (TDZ):

```javascript
// console.log(y); // ReferenceError: Cannot access 'y' before initialization
let y = 10;

// console.log(z); // ReferenceError: Cannot access 'z' before initialization
const z = 20;
```

let and const are hoisted, but they're in the Temporal Dead Zone (TDZ) until the actual declaration line is reached.

### Function expressions are NOT hoisted:

```javascript
// greetFunc(); // TypeError: greetFunc is not a function

var greetFunc = function() {
  console.log('Hello');
};

greetFunc(); // Now it works
```

Why? Because only the variable declaration is hoisted, not the function assignment:

```javascript
// Behind the scenes:
var greetFunc;        // Variable hoisted
greetFunc();          // greetFunc is undefined, not a function
greetFunc = function() { ... }; // Assignment stays here
```

### Arrow functions same as function expressions:

```javascript
// arrowFunc(); // TypeError: arrowFunc is not a function

var arrowFunc = () => {
  console.log('Hello');
};
```

### Hoisting in different scopes:

#### Global scope:

```javascript
console.log(a); // undefined
var a = 10;

// b(); // ReferenceError
let b = function() {};
```

#### Function scope:

```javascript
function test() {
  console.log(x); // undefined
  var x = 5;
  
  inner(); // Works
  
  function inner() {
    console.log('Inside');
  }
}
```

#### Block scope:

```javascript
{
  // console.log(x); // ReferenceError (TDZ)
  let x = 10;
}
```

### Order of hoisting:

When multiple things are hoisted, the order is:

1. Function declarations (highest priority)
2. Variable declarations

```javascript
var myFunc = 'I am a variable';

function myFunc() {
  console.log('I am a function');
}

console.log(typeof myFunc); // 'string'
// Function is hoisted first, then variable assignment overwrites it
```

### Key points to remember:

| Declaration | Hoisted? | Initialized? | TDZ? |
|-------------|----------|--------------|------|
| var | Yes | Yes (undefined) | No |
| let | Yes | No | Yes |
| const | Yes | No | Yes |
| function declaration | Yes | Yes (full function) | No |
| function expression | Depends on var/let/const | No | Depends |

### Best practices:

- Declare variables at the top of their scope (makes hoisting explicit)
- Use let/const instead of var (prevents hoisting confusion)
- Define functions before calling them (cleaner code)

Interview tip: When asked about hoisting, explain that declarations are hoisted but initializations are not. Give examples with var, let, and function declarations to show the differences!"

---

## Q11: What is the Temporal Dead Zone (TDZ)?

### How to Answer:

"The Temporal Dead Zone (TDZ) is the period between when a block scope is entered and when a let or const variable is actually declared. During this time, the variable exists but cannot be accessed - accessing it throws a ReferenceError.

### Why TDZ exists:

let and const variables ARE hoisted, but unlike var, they're not initialized with undefined. They remain uninitialized in the TDZ until their declaration line is reached.

### Example of TDZ:

```javascript
{
  // TDZ starts here for 'x'
  // console.log(x); // ReferenceError: Cannot access 'x' before initialization
  // console.log(y); // ReferenceError: Cannot access 'y' before initialization
  
  let x = 10; // TDZ ends for 'x'
  const y = 20; // TDZ ends for 'y'
  
  console.log(x); // 10 - now accessible
  console.log(y); // 20 - now accessible
}
```

### Contrast with var (no TDZ):

```javascript
{
  console.log(x); // undefined (no error, no TDZ)
  var x = 10;
  console.log(x); // 10
}
```

### TDZ timeline visualization:

```javascript
{
  // ⚠️ TDZ for myVar STARTS here
  
  console.log(typeof myVar); // ReferenceError
  
  // Still in TDZ
  
  let myVar = 5; // ✅ TDZ ENDS here - variable initialized
  
  console.log(myVar); // 5 - accessible now
}
```

### Function parameters and TDZ:

```javascript
function test(a = b, b = 2) {
  return [a, b];
}

// test(); // ReferenceError: Cannot access 'b' before initialization
// 'a' tries to use 'b', but 'b' is still in TDZ
```

### Correct order:

```javascript
function test(a = 1, b = a) {
  return [a, b];
}

test(); // [1, 1] - works because 'a' is already initialized when 'b' uses it
```

### TDZ with typeof:

Interestingly, typeof has different behavior:

```javascript
// Before ES6 (with var):
console.log(typeof x); // 'undefined' - no error
var x;

// With let/const (TDZ):
// console.log(typeof y); // ReferenceError - in TDZ
let y;

// But for undeclared variables:
console.log(typeof neverDeclared); // 'undefined' - no error
```

### TDZ in loops:

```javascript
// This works:
for (let i = 0; i < 3; i++) {
  console.log(i); // Each iteration has its own 'i'
}

// This causes TDZ error:
for (let i = 0; i < 3; i++) {
  // console.log(i); // Would work
  let i = 'shadowed'; // ReferenceError in TDZ
}
```

### Why TDZ is useful:

- Catches errors early: Using variable before declaration is likely a bug
- Prevents confusion: Makes const truly constant (can't use before assigning)
- More predictable: Encourages declaring variables at the top

### const and TDZ:

const must be initialized when declared, otherwise TDZ error:

```javascript
// const x; // SyntaxError: Missing initializer
const x = 10; // Must initialize immediately
```

### Practical example:

```javascript
function calculate() {
  // TDZ starts for 'result'
  
  const multiplier = 2;
  
  // console.log(result); // ReferenceError: in TDZ
  
  const result = multiplier * 10; // TDZ ends
  
  return result; // OK now
}
```

### Key differences from var:

| Aspect | var | let/const |
|--------|-----|-----------|
| Hoisted? | Yes | Yes |
| Initialized? | Yes (undefined) | No |
| TDZ? | No | Yes |
| Error before declaration | No (undefined) | Yes (ReferenceError) |

Interview tip: TDZ exists to make code safer by catching bugs where you try to use a variable before it's declared. It's the 'dead zone' between entering a scope and reaching the declaration line."


## Q12: What is a closure? Explain with examples.

### How to Answer:

A closure is created when a function “remembers” variables from its lexical scope even after the outer function has finished execution.

- JavaScript closures provide data privacy without classes.
- Closures allow encapsulation by hiding variables from the outside world.
- Closures prevent garbage collection of variables they reference.

In simple terms: A closure gives you access to an outer function's scope from an inner function.

### Basic example:

```javascript
function outer() {
  let count = 0; // This variable will be 'closed over'

  function inner() {
    count++;
    console.log(count);
  }

  return inner;
}

const counter = outer();
counter(); // 1
counter(); // 2
counter(); // 3
```

Even though outer() has finished executing, the inner function still has access to count. This is closure.

### How closures work:

When a function is created, it maintains a reference to its lexical scope. This scope chain stays alive as long as the function exists, even if the outer function has returned.

### Another example - Private variables:

```javascript
function createBankAccount(initialBalance) {
  let balance = initialBalance; // Private variable

  return {
    deposit: function(amount) {
      balance += amount;
      return balance;
    },
    withdraw: function(amount) {
      if (amount > balance) {
        return 'Insufficient funds';
      }
      balance -= amount;
      return balance;
    },
    getBalance: function() {
      return balance;
    }
  };
}

const myAccount = createBankAccount(1000);
console.log(myAccount.deposit(500));    // 1500
console.log(myAccount.withdraw(200));   // 1300
console.log(myAccount.getBalance());    // 1300
// console.log(myAccount.balance);      // undefined - private!
```

balance is private - only accessible through the methods. This is closure creating encapsulation.




# this Keyword

## Q13: What is the 'this' keyword? How does its value change in different contexts?

### How to Answer:

"The 'this' keyword in JavaScript refers to the object that is executing the current function. Its value depends on HOW the function is called, not where it's defined.

There are different rules for determining what 'this' refers to:

### 1. Global Context:

In global scope, 'this' refers to the global object (window in browsers, global in Node.js).

```javascript
console.log(this); // window (in browser)

function globalFunction() {
  console.log(this); // window (in non-strict mode)
}

globalFunction();
```

In strict mode, 'this' is undefined in global functions:

```javascript
'use strict';

function strictFunction() {
  console.log(this); // undefined
}
```

### 2. Object Method:

When a function is called as a method of an object, 'this' refers to that object.

```javascript
const person = {
  name: 'John',
  greet: function() {
    console.log(this.name); // 'this' is person object
  }
};

person.greet(); // 'John'
```

### 3. Regular Function Call:

When called as a standalone function, 'this' is the global object (or undefined in strict mode).

```javascript
const person = {
  name: 'John',
  greet: function() {
    console.log(this.name);
  }
};

const greetFunc = person.greet;
greetFunc(); // undefined (this is window, window.name is undefined)
```

### 4. Constructor Function (with 'new'):

When using 'new', 'this' refers to the newly created object.

```javascript
function Person(name) {
  this.name = name;
  this.greet = function() {
    console.log('Hello ' + this.name);
  };
}

const john = new Person('John');
john.greet(); // 'Hello John' - this is john object
```

### 5. Arrow Functions:

Arrow functions DON'T have their own 'this'. They inherit 'this' from the parent scope (lexical this).

```javascript
const person = {
  name: 'John',
  greet: function() {
    const arrowFunc = () => {
      console.log(this.name); // Inherits 'this' from greet
    };
    arrowFunc();
  }
};

person.greet(); // 'John'
```

Compare arrow vs regular:

```javascript
const person = {
  name: 'John',
  regularFunc: function() {
    console.log(this.name); // 'John'
  },
  arrowFunc: () => {
    console.log(this.name); // undefined (this is global)
  }
};

person.regularFunc(); // 'John'
person.arrowFunc(); // undefined
```

### 6. Event Handlers:

In DOM event handlers, 'this' refers to the element that triggered the event.

```javascript
button.addEventListener('click', function() {
  console.log(this); // The button element
});
```

With arrow function:

```javascript
button.addEventListener('click', () => {
  console.log(this); // Window (inherits from parent scope)
});
```

### 7. Explicit Binding (call, apply, bind):

You can explicitly set 'this' using these methods.

```javascript
const person = {
  name: 'John'
};

function greet() {
  console.log('Hello ' + this.name);
}

greet.call(person); // 'Hello John'
greet.apply(person); // 'Hello John'

const boundGreet = greet.bind(person);
boundGreet(); // 'Hello John'
```

### Common pitfalls:

#### Callback functions:

```javascript
const person = {
  name: 'John',
  greet: function() {
    setTimeout(function() {
      console.log(this.name); // undefined - this is window!
    }, 1000);
  }
};
```

Solution 1: Arrow function

```javascript
const person = {
  name: 'John',
  greet: function() {
    setTimeout(() => {
      console.log(this.name); // 'John' - inherits this
    }, 1000);
  }
};
```

Solution 2: Store this

```javascript
const person = {
  name: 'John',
  greet: function() {
    const self = this;
    setTimeout(function() {
      console.log(self.name); // 'John'
    }, 1000);
  }
};
```

#### Method extraction:

```javascript
const person = {
  name: 'John',
  greet() {
    console.log(this.name);
  }
};

const greet = person.greet;
greet(); // undefined - lost context
```

### Decision tree for 'this':

- Arrow function? → Use lexical 'this' (from parent)
- Called with 'new'? → New object
- Called with call/apply/bind? → Explicitly set object
- Called as method? → Object before the dot
- In strict mode? → undefined
- Default → Global object

### Quick reference table:

| Context | 'this' refers to |
|---------|-------------------|
| Global | window/global |
| Object method | The object |
| Regular function | window/undefined |
| Constructor (new) | New object |
| Arrow function | Lexical scope |
| Event handler | Element |
| call/apply/bind | Explicitly set |

Interview tip: When asked about 'this', always mention that it depends on HOW the function is called. Give examples of different contexts and explain arrow function behavior."


# call, apply, bind

## Q14: What are call, apply, and bind? What are the differences?

### How to Answer:

"call, apply, and bind are methods that allow you to explicitly set the value of 'this' in a function. They're all used for function borrowing and setting execution context, but they work slightly differently.

### call():

Calls a function with a given 'this' value and arguments provided individually.

```javascript
const person1 = { name: 'John' };
const person2 = { name: 'Jane' };

function greet(greeting, punctuation) {
  console.log(greeting + ' ' + this.name + punctuation);
}

greet.call(person1, 'Hello', '!'); // 'Hello John!'
greet.call(person2, 'Hi', '.'); // 'Hi Jane.'
```

**Syntax:**

```javascript
function.call(thisArg, arg1, arg2, ...)
```

### apply():

Same as call, but arguments are passed as an array.

```javascript
const person1 = { name: 'John' };

function greet(greeting, punctuation) {
  console.log(greeting + ' ' + this.name + punctuation);
}

greet.apply(person1, ['Hello', '!']); // 'Hello John!'
```

**Syntax:**

```javascript
function.apply(thisArg, [argsArray])
```

### bind():

Returns a NEW function with 'this' bound to the specified value. It doesn't invoke the function immediately.

```javascript
const person1 = { name: 'John' };

function greet(greeting, punctuation) {
  console.log(greeting + ' ' + this.name + punctuation);
}

const greetJohn = greet.bind(person1);
greetJohn('Hello', '!'); // 'Hello John!'

// Can also bind with partial arguments
const sayHelloToJohn = greet.bind(person1, 'Hello');
sayHelloToJohn('!'); // 'Hello John!'
```

**Syntax:**

```javascript
function.bind(thisArg, arg1, arg2, ...)
```

### Practical use cases:

#### 1. Function borrowing:

```javascript
const person = {
  firstName: 'John',
  lastName: 'Doe',
  fullName: function() {
    return this.firstName + ' ' + this.lastName;
  }
};

const person2 = {
  firstName: 'Jane',
  lastName: 'Smith'
};

// Borrow person's fullName method
console.log(person.fullName.call(person2)); // 'Jane Smith'
```

#### 2. Event handlers with bind:

```javascript
const person = {
  name: 'John',
  handleClick: function() {
    console.log(this.name + ' clicked');
  }
};

// Problem: loses context
// button.addEventListener('click', person.handleClick); // undefined clicked

// Solution: bind
button.addEventListener('click', person.handleClick.bind(person)); // 'John clicked'
```

### When to use which:

#### Use call when:

- You know the arguments individually.
- You want immediate execution.

```javascript
greet.call(person, 'Hello', 'World');
```

#### Use apply when:

- You have arguments in an array.
- You want immediate execution.

```javascript
greet.apply(person, ['Hello', 'World']);
```

#### Use bind when:

- You want to create a new function with fixed 'this'.
- You're passing function as callback.
- You want partial application.

```javascript
const greetPerson = greet.bind(person);
setTimeout(greetPerson, 1000);
```

### Chaining bind:

```javascript
function greet() {
  console.log(this.name);
}

const person1 = { name: 'John' };
const person2 = { name: 'Jane' };

const greetPerson1 = greet.bind(person1);
const greetPerson2 = greetPerson1.bind(person2);

greetPerson2(); // 'John' - first bind wins!
```

Once bound, you can't change 'this' again.

### Remember the mnemonic:

- call - 'c' for comma-separated arguments
- apply - 'a' for array of arguments
- bind - 'b' for bind and return

Interview tip: Explain what each does, give practical examples (especially function borrowing and event handlers), and mention the key difference that bind doesn't invoke immediately."


# Objects in JavaScript

## Q15: What are different ways to create objects in JavaScript?

### How to Answer:

An object is a collection of key-value pairs where each key (also called a property) is a string (or symbol) and its value can be any data type including numbers, strings, arrays, functions, or even other objects.

Objects are used to store and organize related data in a structured way.

"There are several ways to create objects in JavaScript, each with different use cases.

### 1. Object Literal (Most common):

The simplest and most common way.

```javascript
const person = {
  name: 'John',
  age: 30,
  greet: function() {
    console.log('Hello ' + this.name);
  }
};

// ES6 shorthand
const name = 'John';
const age = 30;

const person2 = {
  name,  // Same as name: name
  age,   // Same as age: age
  greet() {  // Method shorthand
    console.log('Hello ' + this.name);
  }
};
```

### 2. Object Constructor (new Object()):

Less common, same as literal.

```javascript
const person = new Object();

person.name = 'John';
person.age = 30;
person.greet = function() {
  console.log('Hello ' + this.name);
};
```

### 3. Constructor Function:

Used before ES6 classes. Good for creating multiple similar objects.

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.greet = function() {
    console.log('Hello ' + this.name);
  };
}

const john = new Person('John', 30);
const jane = new Person('Jane', 25);
```

### 4. Object.create():

Creates object with specific prototype.

```javascript
const personProto = {
  greet: function() {
    console.log('Hello ' + this.name);
  }
};

const john = Object.create(personProto);
john.name = 'John';
john.age = 30;

john.greet(); // 'Hello John'
```

### 5. ES6 Classes:

Modern, clean syntax (syntactic sugar over constructor functions).

```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    console.log('Hello ' + this.name);
  }
}

const john = new Person('John', 30);
```

### 6. Factory Functions:

Regular functions that return objects.

```javascript
function createPerson(name, age) {
  return {
    name: name,
    age: age,
    greet() {
      console.log('Hello ' + this.name);
    }
  };
}

const john = createPerson('John', 30);
```

### Comparison:

#### Object Literal:

- ✅ Simple, readable
- ✅ Fast
- ❌ Not reusable for multiple objects

#### Constructor Function:

- ✅ Reusable
- ✅ Can use instanceof
- ❌ Verbose
- ❌ Methods recreated for each instance (unless on prototype)

#### Object.create():

- ✅ Precise prototype control
- ✅ Inheritance without constructor
- ❌ Less intuitive
- ❌ Property initialization separate from creation

#### ES6 Class:

- ✅ Clean, modern syntax
- ✅ Easy inheritance with extends
- ✅ Static methods easy
- ❌ Just syntactic sugar (not new concept)

#### Factory Function:

- ✅ No 'new' needed
- ✅ Can return different objects conditionally
- ✅ Private variables easy (closure)
- ❌ No prototype benefits
- ❌ Can't use instanceof

### When to use which:

```javascript
// Single object → Object Literal
const config = { api: 'url', timeout: 5000 };

// Multiple similar objects → Constructor/Class
class User {
  constructor(name) {
    this.name = name;
  }
}

// Inheritance needed → Class (modern) or Constructor
class Admin extends User { }

// Custom prototype chain → Object.create()
const child = Object.create(parent);

// Private data needed → Factory Function
function createCounter() {
  let count = 0; // Private

  return {
    increment() { count++; },
    getCount() { return count; }
  };
}
```

Interview tip: Know all methods, but emphasize object literals for simple cases and classes for complex objects with inheritance. Mention that classes are the modern standard." 

# Object Destructuring, Spread and Rest Operators

## Q16: What is Object destructuring? Explain spread and rest operators with objects.

### How to Answer:

"Destructuring is a way to extract properties from objects (or elements from arrays) into distinct variables. The spread and rest operators (...) have different uses with objects.

## Object Destructuring:

Basic syntax to extract properties:

```javascript
const person = {
  name: 'John',
  age: 30,
  city: 'New York'
};

// Old way
const name = person.name;
const age = person.age;

// Destructuring way
const { name, age, city } = person;

console.log(name); // 'John'
console.log(age);  // 30
```

### Renaming variables:

```javascript
const person = { name: 'John', age: 30 };

const { name: personName, age: personAge } = person;

console.log(personName); // 'John'
console.log(personAge);  // 30
```

### Default values:

```javascript
const person = { name: 'John' };

const { name, age = 25, city = 'Unknown' } = person;

console.log(age);  // 25 (default)
console.log(city); // 'Unknown' (default)
```

### Nested destructuring:

```javascript
const person = {
  name: 'John',
  address: {
    city: 'New York',
    zip: '10001'
  }
};

const { name, address: { city, zip } } = person;

console.log(city); // 'New York'
console.log(zip);  // '10001'
```

### Function parameters:

```javascript
function greet({ name, age }) {
  console.log(`Hello ${name}, age ${age}`);
}

greet({ name: 'John', age: 30 }); // 'Hello John, age 30'
```

With defaults:

```javascript
function greet({ name = 'Guest', age = 0 } = {}) {
  console.log(`Hello ${name}, age ${age}`);
}

greet(); // 'Hello Guest, age 0'
```

## Spread Operator (...):

Used to copy or merge objects.

### Copying objects:

```javascript
const person = { name: 'John', age: 30 };

const personCopy = { ...person };

console.log(personCopy); // { name: 'John', age: 30 }

// Modifying copy doesn't affect original
personCopy.age = 31;

console.log(person.age); // 30 (unchanged)
```

Note: This is a shallow copy:

```javascript
const person = {
  name: 'John',
  address: { city: 'New York' }
};

const copy = { ...person };

copy.address.city = 'Boston';

console.log(person.address.city); // 'Boston' (changed!)
// Nested objects are still referenced
```

### Merging objects:

```javascript
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };

const merged = { ...obj1, ...obj2 };

console.log(merged); // { a: 1, b: 2, c: 3, d: 4 }

// Later properties override earlier ones
const obj3 = { a: 1, b: 2 };
const obj4 = { b: 3, c: 4 };

const merged2 = { ...obj3, ...obj4 };

console.log(merged2); // { a: 1, b: 3, c: 4 }
```

### Adding/overriding properties:

```javascript
const person = { name: 'John', age: 30 };

const updatedPerson = {
  ...person,
  age: 31,
  city: 'New York'
};

console.log(updatedPerson);
// { name: 'John', age: 31, city: 'New York' }
```

## Rest Operator (...):

Used to collect remaining properties.

### In destructuring:

```javascript
const person = {
  name: 'John',
  age: 30,
  city: 'New York',
  country: 'USA'
};

const { name, age, ...rest } = person;

console.log(name); // 'John'
console.log(age);  // 30
console.log(rest); // { city: 'New York', country: 'USA' }
```

### In function parameters:

```javascript
function printInfo({ name, ...otherInfo }) {
  console.log('Name:', name);
  console.log('Other info:', otherInfo);
}

printInfo({
  name: 'John',
  age: 30,
  city: 'New York'
});

// Name: John
// Other info: { age: 30, city: 'New York' }
```

## Practical examples:

### Removing a property:

```javascript
const person = { name: 'John', age: 30, password: '123' };

const { password, ...publicInfo } = person;

console.log(publicInfo); // { name: 'John', age: 30 }
```

### Updating state (React pattern):

```javascript
const state = { user: 'John', count: 0, theme: 'dark' };

// Update one property
const newState = { ...state, count: state.count + 1 };
```

### Combining multiple objects:

```javascript
const defaults = { theme: 'light', lang: 'en' };
const userPrefs = { theme: 'dark' };

const settings = { ...defaults, ...userPrefs };

console.log(settings); // { theme: 'dark', lang: 'en' }
```

## Spread vs Rest - Key Difference:

### Spread:

Expands an object into individual properties (spreading OUT)

```javascript
const copy = { ...original }; // Spreading out
```

### Rest:

Collects properties into an object (gathering IN)

```javascript
const { name, ...rest } = obj; // Collecting remaining
```

Interview tip: Show you understand both destructuring for extracting properties and spread/rest for copying/collecting. Give practical examples like removing properties or updating objects immutably."


# Shallow Copy and Deep Copy

## Q17: What is the difference between shallow copy and deep copy?

### How to Answer:

"Shallow copy and deep copy refer to how nested objects are copied. This is important because objects in JavaScript are reference types.

## Shallow Copy:

Copies only the first level of properties. Nested objects are still referenced, not copied.

```javascript
const original = {
  name: 'John',
  address: {
    city: 'New York',
    zip: '10001'
  }
};

// Shallow copy using spread
const shallowCopy = { ...original };

// Modify top-level property
shallowCopy.name = 'Jane';
console.log(original.name); // 'John' (unchanged) ✅

// Modify nested property
shallowCopy.address.city = 'Boston';
console.log(original.address.city); // 'Boston' (changed!) ❌
```

The problem: address object is shared between original and copy.

### Methods that create shallow copies:

```javascript
// 1. Spread operator
const copy1 = { ...original };

// 2. Object.assign()
const copy2 = Object.assign({}, original);

// 3. Array methods (for arrays)
const arrCopy1 = [...originalArray];
const arrCopy2 = originalArray.slice();
```

## Deep Copy:

Copies all levels recursively. Nested objects are also copied, not referenced.

```javascript
const original = {
  name: 'John',
  address: {
    city: 'New York',
    zip: '10001'
  }
};

// Deep copy using JSON (simple objects only)
const deepCopy = JSON.parse(JSON.stringify(original));

// Modify nested property
deepCopy.address.city = 'Boston';
console.log(original.address.city); // 'New York' (unchanged) ✅
```

### Methods to create deep copies:

#### 1. JSON.parse(JSON.stringify()) - Quick but limited:

```javascript
const deepCopy = JSON.parse(JSON.stringify(original));
```

### Limitations:

- Doesn't work with functions
- Doesn't work with undefined
- Doesn't work with Symbols
- Doesn't work with Dates (converts to string)
- Doesn't work with circular references

```javascript
const obj = {
  func: function() {},    // Lost!
  undef: undefined,       // Lost!
  date: new Date(),       // Becomes string
  nested: { a: 1 }
};

const copy = JSON.parse(JSON.stringify(obj));

console.log(copy);
// { date: '2024-...', nested: { a: 1 } }
// func and undef are missing!
```

## Visual comparison:

```javascript
const original = {
  level1: 'value',
  level2: {
    nested: 'value'
  }
};

// Shallow copy
const shallow = { ...original };
// ┌─────────┐
// │ shallow │ → { level1: 'value', level2: → [shared reference] }
// └─────────┘                               ↓
// ┌─────────┐                     { nested: 'value' }
// │original │ → { level1: 'value', level2: ↗ }
// └─────────┘

// Deep copy
const deep = JSON.parse(JSON.stringify(original));
// ┌─────────┐
// │  deep   │ → { level1: 'value', level2: → { nested: 'value' } }
// └─────────┘
// ┌─────────┐
// │original │ → { level1: 'value', level2: → { nested: 'value' } }
// └─────────┘
```

## When to use which:

### Use Shallow Copy when:

- Object has only primitive values
- You control all modifications
- Performance is critical (faster)

### Use Deep Copy when:

- Object has nested objects/arrays
- You need complete independence
- Passing object to external code

## When to use which:

### Use Shallow Copy when:

- Object has only primitive values
- You control all modifications
- Performance is critical (faster)

### Use Deep Copy when:

- Object has nested objects/arrays
- You need complete independence
- Passing object to external code

# Object.freeze() vs Object.seal()

## Q18: Explain Object.freeze() vs Object.seal().

### How to Answer:

"Object.freeze() and Object.seal() are methods to restrict modifications to objects, but they have different levels of restriction.

## Object.freeze():

Makes object completely immutable - can't add, delete, or modify properties.

```javascript
const person = {
  name: 'John',
  age: 30
};

Object.freeze(person);

// Try to modify
person.age = 31;           // Silently fails (strict mode: error)
person.city = 'New York';  // Silently fails
delete person.name;        // Silently fails

console.log(person);       // { name: 'John', age: 30 }
```

## Object.seal():

Prevents adding/deleting properties, but allows modifying existing ones.

```javascript
const person = {
  name: 'John',
  age: 30
};

Object.seal(person);

// Try to modify
person.age = 31;           // ✅ Works! (modification allowed)
person.city = 'New York';  // ❌ Fails (adding not allowed)
delete person.name;        // ❌ Fails (deleting not allowed)

console.log(person);       // { name: 'John', age: 31 }
```

## Checking if object is frozen/sealed:

```javascript
const person = { name: 'John' };

Object.freeze(person);

console.log(Object.isFrozen(person));  // true
console.log(Object.isSealed(person));  // true (frozen is also sealed)

const person2 = { name: 'Jane' };

Object.seal(person2);

console.log(Object.isFrozen(person2)); // false
console.log(Object.isSealed(person2)); // true
```

## Important limitation - Shallow only:

Both freeze and seal work only on the first level:

```javascript
const person = {
  name: 'John',
  address: {
    city: 'New York'
  }
};

Object.freeze(person);

// Can't modify person
person.name = 'Jane'; // Fails

// But CAN modify nested object!
person.address.city = 'Boston'; // Works!

console.log(person.address.city); // 'Boston'
```   

# Functions

## Q19: What's the difference between function declaration and function expression?

### How to Answer:

"Function declarations and function expressions are two ways to define functions, but they differ in how they're created, hoisted, and used.

## Function Declaration:

Declared with the `function` keyword followed by a name.

```javascript
function greet(name) {
  return 'Hello ' + name;
}

console.log(greet('John')); // 'Hello John'
```

## Function Expression:

Function is assigned to a variable. Can be named or anonymous.

```javascript
// Anonymous function expression
const greet = function(name) {
  return 'Hello ' + name;
};

// Named function expression
const greet2 = function greetFunc(name) {
  return 'Hello ' + name;
};

console.log(greet('John')); // 'Hello John'
```

## Key Differences:

### 1. Hoisting:

```javascript
// Function declaration - hoisted, works!
greet1('John'); // 'Hello John'

function greet1(name) {
  return 'Hello ' + name;
}

// Function expression - NOT hoisted, error!
// greet2('John'); // TypeError: greet2 is not a function

const greet2 = function(name) {
  return 'Hello ' + name;
};

greet2('John'); // Now works
```

### 2. Name requirement:

```javascript
// Declaration - name required
function greet() { }  // Must have name

// Expression - name optional
const greet = function() { };          // Anonymous
const greet = function greet() { };    // Named
```

### 3. Usage context:

```javascript
// Declaration - statement
function greet() { }

// Expression - can be part of an expression
const myFunc = function() { }; // Assignment
array.map(function() { });      // Callback
(function() { })();             // IIFE
```

## Comparison table:

| Feature | Declaration | Expression |
|---------|-------------|------------|
| Hoisting | Fully hoisted | Variable hoisted, not function |
| Name | Required | Optional |
| Usage | Statement | Expression |
| When available | Before declaration | After assignment |

## Arrow Function Expression (ES6):

A special type of function expression with shorter syntax:

```javascript
// Regular function expression
const add1 = function(a, b) {
  return a + b;
};

// Arrow function
const add2 = (a, b) => {
  return a + b;
};

// Arrow function (implicit return)
const add3 = (a, b) => a + b;
```

Arrow functions have different `this` behavior:

```javascript
const person = {
  name: 'John',

  // Regular function
  greet1: function() {
    console.log('Hello ' + this.name); // 'this' is person
  },

  // Arrow function
  greet2: () => {
    console.log('Hello ' + this.name); // 'this' is NOT person!
  }
};

person.greet1(); // 'Hello John'
person.greet2(); // 'Hello undefined'
```

## When to use which:

### Use Function Declaration when:

- You want hoisting (function available anywhere in scope)
- Creating named functions for better stack traces
- Top-level functions

```javascript
function calculateTotal(items) {
  // Available throughout scope
}
```

### Use Function Expression when:

- Assigning function to variable
- Passing function as argument
- Creating closures
- Conditional function creation

```javascript
const calculate = condition ? function() { } : function() { };

array.map(function(item) {
  return item * 2;
});
```

### Use Arrow Function when:

- Short callbacks
- You need lexical `this`
- One-liner functions

```javascript
array.map(item => item * 2);

setTimeout(() => console.log('Done'), 1000);
```

## Named vs Anonymous Function Expressions:

```javascript
// Anonymous - harder to debug
const greet = function(name) {
  // Stack trace shows 'anonymous function'
  return 'Hello ' + name;
};

// Named - better for debugging
const greet = function greetFunction(name) {
  // Stack trace shows 'greetFunction'
  return 'Hello ' + name;
};
```

## IIFE (Immediately Invoked Function Expression):

Only possible with function expression:

```javascript
// Function expression IIFE
(function() {
  console.log('I run immediately!');
})();

// Arrow function IIFE
(() => {
  console.log('I also run immediately!');
})();

// Function declaration can't be IIFE
// function() { }(); // Syntax error
```

Interview tip: The main difference is hoisting - declarations are fully hoisted and can be called before they're defined, while expressions are not. Give examples of when each is useful."


# Arrow Functions

## Q20: What are arrow functions and how are they different from regular functions?

### How to Answer:

"Arrow functions are a shorter syntax for writing functions introduced in ES6. They have some important differences from regular functions, especially regarding 'this' binding.

## Syntax:

```javascript
// Regular function
function add(a, b) {
  return a + b;
}

// Arrow function
const add = (a, b) => {
  return a + b;
};

// Arrow function (implicit return)
const add = (a, b) => a + b;

// Single parameter (parentheses optional)
const double = n => n * 2;

// No parameters
const greet = () => 'Hello';
```

## Key Differences:

### 1. 'this' binding (Most Important):

Arrow functions don't have their own 'this'. They inherit 'this' from the parent scope (lexical 'this').

```javascript
const person = {
  name: 'John',

  // Regular function - 'this' is person
  greet: function() {
    console.log('Hello ' + this.name);
  },

  // Arrow function - 'this' is NOT person!
  greetArrow: () => {
    console.log('Hello ' + this.name); // undefined
  }
};

person.greet();      // 'Hello John'
person.greetArrow(); // 'Hello undefined'
```

### Why this matters - callbacks:

```javascript
const person = {
  name: 'John',
  hobbies: ['reading', 'gaming'],

  // Regular function with regular callback
  printHobbies1: function() {
    this.hobbies.forEach(function(hobby) {
      console.log(this.name + ' likes ' + hobby);
      // 'this' is undefined! Lost context
    });
  },

  // Regular function with arrow callback
  printHobbies2: function() {
    this.hobbies.forEach(hobby => {
      console.log(this.name + ' likes ' + hobby);
      // 'this' is person! Inherited from parent
    });
  }
};

person.printHobbies2(); // Works correctly
```

### 2. No 'arguments' object:

```javascript
// Regular function - has arguments
function regularFunc() {
  console.log(arguments); // [1, 2, 3]
}

regularFunc(1, 2, 3);

// Arrow function - no arguments
const arrowFunc = () => {
  // console.log(arguments); // ReferenceError
};

// Use rest parameters instead
const arrowFunc = (...args) => {
  console.log(args); // [1, 2, 3]
};

arrowFunc(1, 2, 3);
```

### 3. Cannot be used as constructor:

```javascript
// Regular function - can use 'new'
function Person(name) {
  this.name = name;
}

const john = new Person('John'); // Works

// Arrow function - cannot use 'new'
const PersonArrow = (name) => {
  this.name = name;
};

// const jane = new PersonArrow('Jane'); // TypeError
```

### 4. No prototype property:

```javascript
function regularFunc() {}

console.log(regularFunc.prototype); // {}

const arrowFunc = () => {};

console.log(arrowFunc.prototype);   // undefined
```

### 5. Cannot be used as methods (in object literals):

```javascript
const calculator = {
  value: 0,

  // Regular function - works
  add: function(n) {
    this.value += n;
    return this.value;
  },

  // Arrow function - doesn't work as expected
  subtract: (n) => {
    this.value -= n; // 'this' is not calculator!
    return this.value;
  }
};

calculator.add(5);      // 5 - works
calculator.subtract(2); // NaN - doesn't work
```

### 6. Cannot be generators:

```javascript
// Regular function - can be generator
function* generator() {
  yield 1;
  yield 2;
}

// Arrow function - cannot be generator
// const generator = *() => {}; // Syntax error
```

## Comparison table:

| Feature | Regular Function | Arrow Function |
|---------|-------------------|----------------|
| this | Dynamic (call-site) | Lexical (parent) |
| arguments | ✅ Has | ❌ No |
| new | ✅ Can use | ❌ Cannot use |
| prototype | ✅ Has | ❌ No |
| Hoisting | ✅ Declaration only | ❌ No |
| Implicit return | ❌ No | ✅ Yes (one-liner) |

## When to use arrow functions:

✅ Use arrow functions for:

- Callbacks
- Array methods (map, filter, etc.)
- Short utility functions
- When you need lexical 'this'

Interview tip: The most important difference is 'this' binding. Arrow functions inherit 'this' from parent scope, while regular functions have dynamic 'this' based on how they're called. Give examples showing when each is appropriate!"

# Higher-Order Functions

## Q21: What are higher-order functions?

### How to Answer:

"A higher-order function is a function that either takes a function as an argument, or returns a function, or both. It's a core concept in functional programming.

## Taking function as argument:

```javascript
// Higher-order function
function processArray(arr, callback) {
  const result = [];

  for (let item of arr) {
    result.push(callback(item));
  }

  return result;
}

// Using it
const numbers = [1, 2, 3, 4];

const doubled = processArray(numbers, n => n * 2);

console.log(doubled); // [2, 4, 6, 8]
```

## Returning a function:

```javascript
// Higher-order function
function multiplyBy(multiplier) {
  return function(number) {
    return number * multiplier;
  };
}

// Using it
const double = multiplyBy(2);
const triple = multiplyBy(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
```

## Built-in higher-order functions:

JavaScript has many built-in higher-order functions.

### Array methods:

```javascript
const numbers = [1, 2, 3, 4, 5];

// map - transforms each element
const doubled = numbers.map(n => n * 2);
// [2, 4, 6, 8, 10]

// filter - keeps elements that pass test
const evens = numbers.filter(n => n % 2 === 0);
// [2, 4]

// reduce - reduces to single value
const sum = numbers.reduce((total, n) => total + n, 0);
// 15

// forEach - executes function for each
numbers.forEach(n => console.log(n));

// find - finds first element
const found = numbers.find(n => n > 3);
// 4

// some - checks if any element passes test
const hasEven = numbers.some(n => n % 2 === 0);
// true

// every - checks if all elements pass test
const allPositive = numbers.every(n => n > 0);
// true
```

## Practical examples:

1. Function composition:

2. Currying:

3. Memoization:

4. Debounce:

## Benefits of higher-order functions:

- Code reusability - Write generic functions
- Abstraction - Hide implementation details
- Composition - Build complex functions from simple ones
- Declarative - Say what you want, not how to do it

## Example showing benefits:

```javascript
// Imperative (how)
const numbers = [1, 2, 3, 4, 5];

const doubled = [];

for (let i = 0; i < numbers.length; i++) {
  doubled.push(numbers[i] * 2);
}

// Declarative (what) - using higher-order function
const doubled = numbers.map(n => n * 2);
```

## Real-world example:

```javascript
// Array processing pipeline
const users = [
  { name: 'John', age: 25, active: true },
  { name: 'Jane', age: 30, active: false },
  { name: 'Bob', age: 35, active: true }
];

const result = users
  .filter(user => user.active)           // Keep active users
  .map(user => user.name.toUpperCase())  // Get uppercase names
  .sort();                               // Sort alphabetically

console.log(result); // ['BOB', 'JOHN']
```

Interview tip: Explain that higher-order functions are functions that work with other functions. Give examples of built-in array methods and show how they make code more declarative and reusable. Mention practical applications like debounce and memoization."


# Function Currying

## Q22: What is function currying?

### How to Answer:

"Currying is a technique of transforming a function with multiple arguments into a sequence of functions, each taking a single argument. Instead of calling `f(a, b, c)`, you call `f(a)(b)(c)`.

The name comes from mathematician Haskell Curry, not from Indian curry!

## Basic example:

```javascript
// Normal function
function add(a, b, c) {
  return a + b + c;
}

console.log(add(1, 2, 3)); // 6

// Curried function
function addCurried(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

console.log(addCurried(1)(2)(3)); // 6

// With arrow functions (cleaner)
const addCurried = a => b => c => a + b + c;

console.log(addCurried(1)(2)(3)); // 6
```

## Benefits of currying:

- Reusability: Create specialized functions from general ones
- Readability: More expressive function names
- Testability: Easier to test with fixed parameters
- Composition: Build complex functions from simple ones

Interview tip: Explain that currying transforms a multi-argument function into nested single-argument functions. Show practical benefits like creating specialized functions and improving code reusability. Give a clear before/after example."


# Arrays

## Q23: Explain array methods: map, filter, reduce, forEach.

### How to Answer:

"These are higher-order array methods that make array manipulation easier and more readable. Each has a specific purpose.

## map() - Transform every element:

Creates a NEW array by transforming each element. Doesn't modify original array.

```javascript
const numbers = [1, 2, 3, 4];

// Transform each number
const doubled = numbers.map(num => num * 2);

console.log(doubled); // [2, 4, 6, 8]
console.log(numbers); // [1, 2, 3, 4] (original unchanged)

// More complex transformation
const users = [
  { name: 'John', age: 25 },
  { name: 'Jane', age: 30 }
];

const names = users.map(user => user.name);

console.log(names); // ['John', 'Jane']
```

### When to use map:

- Need to transform every element
- Want a new array of same length
- Converting data structure

## filter() - Keep only matching elements:

Creates a NEW array with elements that pass a test. Doesn't modify original.

```javascript
const numbers = [1, 2, 3, 4, 5, 6];

// Keep only even numbers
const evens = numbers.filter(num => num % 2 === 0);

console.log(evens); // [2, 4, 6]

// More complex filtering
const users = [
  { name: 'John', age: 25, active: true },
  { name: 'Jane', age: 30, active: false },
  { name: 'Bob', age: 35, active: true }
];

const activeUsers = users.filter(user => user.active);

console.log(activeUsers); // John and Bob
```

### When to use filter:

- Need subset of array
- Removing elements based on condition
- Searching for matching elements

## reduce() - Reduce array to single value:

Accumulates array elements into a single value. Most powerful but complex.

```javascript
const numbers = [1, 2, 3, 4, 5];

// Sum all numbers
const sum = numbers.reduce((total, num) => {
  return total + num;
}, 0); // 0 is initial value

console.log(sum); // 15

// Find maximum
const max = numbers.reduce((max, num) => {
  return num > max ? num : max;
}, numbers[0]);

console.log(max); // 5

// Group by property
const users = [
  { name: 'John', role: 'admin' },
  { name: 'Jane', role: 'user' },
  { name: 'Bob', role: 'admin' }
];

const groupedByRole = users.reduce((acc, user) => {
  if (!acc[user.role]) {
    acc[user.role] = [];
  }

  acc[user.role].push(user);
  return acc;
}, {});

console.log(groupedByRole);

// {
//   admin: [{ name: 'John', ... }, { name: 'Bob', ... }],
//   user: [{ name: 'Jane', ... }]
// }
```

### When to use reduce:

- Converting array to different type (array to object, array to number)
- Accumulating values
- Complex transformations
- Grouping or counting

## forEach() - Execute function for each element:

Executes a function for each element. Doesn't return anything. DOES NOT create new array.

```javascript
const numbers = [1, 2, 3, 4];

// Just log each number
numbers.forEach(num => {
  console.log(num);
});

// Common use - side effects
const users = ['John', 'Jane', 'Bob'];

users.forEach((user, index) => {
  console.log(`${index + 1}. ${user}`);
});

// 1. John
// 2. Jane
// 3. Bob
```

### When to use forEach:

- Need side effects (logging, DOM manipulation)
- Don't need return value
- Simpler syntax than for loop for iteration

## Key differences:

| Method | Returns | Modifies Original | Use Case |
|--------|---------|-------------------|----------|
| map | New array | No | Transform elements |
| filter | New array | No | Select elements |
| reduce | Single value | No | Accumulate/combine |
| forEach | undefined | No | Side effects |

## Chaining methods:

These methods can be chained for complex operations.

```javascript
const numbers = [1, 2, 3, 4, 5, 6];

const result = numbers
  .filter(n => n % 2 === 0)        // [2, 4, 6]
  .map(n => n * 2)                 // [4, 8, 12]
  .reduce((sum, n) => sum + n, 0); // 24

console.log(result); // 24
```

## Real-world example:

```javascript
const products = [
  { name: 'Laptop', price: 1000, inStock: true },
  { name: 'Mouse', price: 20, inStock: true },
  { name: 'Keyboard', price: 50, inStock: false },
  { name: 'Monitor', price: 300, inStock: true }
];

// Get total price of available products
const totalPrice = products
  .filter(p => p.inStock)
  .map(p => p.price)
  .reduce((sum, price) => sum + price, 0);

console.log(totalPrice); // 1320
```

## Performance considerations:

```javascript
// Less efficient - multiple iterations
const result = numbers
  .map(n => n * 2)                     // Iteration 1
  .filter(n => n > 10)                 // Iteration 2
  .reduce((sum, n) => sum + n, 0);     // Iteration 3

// More efficient - single iteration with reduce
const result = numbers.reduce((sum, n) => {
  const doubled = n * 2;

  if (doubled > 10) {
    return sum + doubled;
  }

  return sum;
}, 0);
```

## Common patterns:

### Convert array to object:

```javascript
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' }
];

const usersById = users.reduce((acc, user) => {
  acc[user.id] = user;
  return acc;
}, {});

// { 1: {...}, 2: {...} }
```

### Flatten array:

```javascript
const nested = [[1, 2], [3, 4], [5]];

const flat = nested.reduce((acc, arr) => {
  return acc.concat(arr);
}, []);

// [1, 2, 3, 4, 5]

// Or use flat()
const flat2 = nested.flat();
```

### Count occurrences:

```javascript
const fruits = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple'];

const count = fruits.reduce((acc, fruit) => {
  acc[fruit] = (acc[fruit] || 0) + 1;
  return acc;
}, {});

// { apple: 3, banana: 2, orange: 1 }
```

Interview tip: Explain what each method does and when to use it. map for transformation, filter for selection, reduce for accumulation, forEach for side effects. Show you can chain them for complex operations. Give practical examples like calculating totals or filtering data."


# Arrays

## Q24: Explain other important array methods.

### How to Answer:

"Beyond map, filter, and reduce, there are many other useful array methods.

## find() - Find first matching element:

Returns the first element that matches the condition, or undefined.

```javascript
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' },
  { id: 3, name: 'Bob' }
];

const user = users.find(u => u.id === 2);
console.log(user); // { id: 2, name: 'Jane' }

const notFound = users.find(u => u.id === 10);
console.log(notFound); // undefined
```

## findIndex() - Find index of first match:

```javascript
const numbers = [10, 20, 30, 40];

const index = numbers.findIndex(n => n > 25);
console.log(index); // 2 (30 is at index 2)

const notFound = numbers.findIndex(n => n > 100);
console.log(notFound); // -1
```

## some() - Check if ANY element matches:

Returns true if at least one element passes the test.

```javascript
const numbers = [1, 3, 5, 8, 9];

const hasEven = numbers.some(n => n % 2 === 0);
console.log(hasEven); // true (8 is even)

const hasNegative = numbers.some(n => n < 0);
console.log(hasNegative); // false
```

## every() - Check if ALL elements match:

Returns true only if all elements pass the test.

```javascript
const numbers = [2, 4, 6, 8];

const allEven = numbers.every(n => n % 2 === 0);
console.log(allEven); // true

const allPositive = numbers.every(n => n > 0);
console.log(allPositive); // true
```

## includes() - Check if value exists:

```javascript
const fruits = ['apple', 'banana', 'orange'];

console.log(fruits.includes('banana')); // true
console.log(fruits.includes('grape')); // false

// Works with fromIndex
console.log(fruits.includes('apple', 1)); // false (starts from index 1)
```

## indexOf() / lastIndexOf() - Find index of value:

```javascript
const numbers = [1, 2, 3, 2, 4];

console.log(numbers.indexOf(2)); // 1 (first occurrence)
console.log(numbers.lastIndexOf(2)); // 3 (last occurrence)
console.log(numbers.indexOf(10)); // -1 (not found)
```

## push() / pop() - Add/remove from end:

```javascript
const arr = [1, 2, 3];

arr.push(4); // Add to end
console.log(arr); // [1, 2, 3, 4]

const last = arr.pop(); // Remove from end
console.log(last); // 4
console.log(arr); // [1, 2, 3]
```

## shift() / unshift() - Add/remove from start:

```javascript
const arr = [1, 2, 3];

arr.unshift(0); // Add to start
console.log(arr); // [0, 1, 2, 3]

const first = arr.shift(); // Remove from start
console.log(first); // 0
console.log(arr); // [1, 2, 3]
```

## slice() - Copy portion (doesn't modify original):

```javascript
const numbers = [1, 2, 3, 4, 5];

const slice1 = numbers.slice(1, 3); // From index 1 to 3 (exclusive)
console.log(slice1); // [2, 3]

const slice2 = numbers.slice(2); // From index 2 to end
console.log(slice2); // [3, 4, 5]

const copy = numbers.slice(); // Copy entire array

console.log(numbers); // [1, 2, 3, 4, 5] (original unchanged)
```

## splice() - Add/remove at position (modifies original):

```javascript
const numbers = [1, 2, 3, 4, 5];

// Remove 2 elements starting at index 1
const removed = numbers.splice(1, 2);

console.log(removed); // [2, 3]
console.log(numbers); // [1, 4, 5]

// Add elements at index 1
numbers.splice(1, 0, 10, 20);
console.log(numbers); // [1, 10, 20, 4, 5]

// Replace elements
numbers.splice(1, 2, 100);
console.log(numbers); // [1, 100, 4, 5]
```

## concat() - Merge arrays:

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
const arr3 = [5, 6];

const merged = arr1.concat(arr2, arr3);
console.log(merged); // [1, 2, 3, 4, 5, 6]

// Modern alternative: spread
const merged2 = [...arr1, ...arr2, ...arr3];
```

## join() - Array to string:

```javascript
const words = ['Hello', 'World'];

console.log(words.join(' ')); // 'Hello World'
console.log(words.join('-')); // 'Hello-World'
console.log(words.join('')); // 'HelloWorld'
```

## reverse() - Reverse array (modifies original):

```javascript
const numbers = [1, 2, 3, 4];

numbers.reverse();

console.log(numbers); // [4, 3, 2, 1]
```

## sort() - Sort array (modifies original):

```javascript
// Alphabetical sort (default)
const fruits = ['banana', 'apple', 'orange'];

fruits.sort();

console.log(fruits); // ['apple', 'banana', 'orange']

// Number sort (need compare function!)
const numbers = [10, 5, 40, 25];

numbers.sort(); // [10, 25, 40, 5] - Wrong! Sorted as strings

// Correct number sort
numbers.sort((a, b) => a - b); // Ascending
console.log(numbers); // [5, 10, 25, 40]

numbers.sort((a, b) => b - a); // Descending
console.log(numbers); // [40, 25, 10, 5]
```

## flat() - Flatten nested arrays:

```javascript
const nested = [1, [2, 3], [4, [5, 6]]];

console.log(nested.flat()); // [1, 2, 3, 4, [5, 6]] (1 level)
console.log(nested.flat(2)); // [1, 2, 3, 4, 5, 6] (2 levels)
console.log(nested.flat(Infinity)); // Flatten all levels
```

## flatMap() - Map then flatten:

```javascript
const sentences = ['Hello world', 'How are you'];

const words = sentences.flatMap(s => s.split(' '));

console.log(words); // ['Hello', 'world', 'How', 'are', 'you']
```

## Quick reference table:

| Method | Modifies Original | Returns | Use Case |
|--------|-------------------|---------|----------|
| push/pop | Yes | Element | Add/remove end |
| shift/unshift | Yes | Element | Add/remove start |
| slice | No | New array | Copy portion |
| splice | Yes | Removed items | Add/remove anywhere |
| concat | No | New array | Merge arrays |
| sort | Yes | Sorted array | Sort elements |
| reverse | Yes | Reversed array | Reverse order |

Interview tip: Know the difference between methods that modify the original array (splice, sort, reverse) and those that don't (slice, concat, map). Be ready to explain when to use each method."


# Strings & Numbers

## Q25: What are important String methods?

### How to Answer:

"JavaScript has many built-in string methods for manipulation and searching.

### Length and character access:

```javascript
const str = 'Hello World';

console.log(str.length); // 11
console.log(str[0]); // 'H'
console.log(str.charAt(0)); // 'H'
console.log(str.charAt(100)); // '' (empty string, not undefined)
```

### Case conversion:

```javascript
const str = 'Hello World';

console.log(str.toUpperCase()); // 'HELLO WORLD'
console.log(str.toLowerCase()); // 'hello world'
console.log(str); // 'Hello World' (original unchanged - strings are immutable)
```

### Searching:

```javascript
const str = 'Hello World Hello';

// includes - check if contains
console.log(str.includes('World')); // true
console.log(str.includes('world')); // false (case-sensitive)

// indexOf - find first position
console.log(str.indexOf('Hello')); // 0
console.log(str.indexOf('Hello', 1)); // 12 (start searching from index 1)
console.log(str.indexOf('xyz')); // -1 (not found)

// lastIndexOf - find last position
console.log(str.lastIndexOf('Hello')); // 12

// startsWith / endsWith
console.log(str.startsWith('Hello')); // true
console.log(str.endsWith('Hello')); // true
console.log(str.startsWith('World')); // false
```

### Extracting substrings:

```javascript
const str = 'Hello World';

// slice(start, end) - most flexible
console.log(str.slice(0, 5)); // 'Hello'
console.log(str.slice(6)); // 'World' (from index 6 to end)
console.log(str.slice(-5)); // 'World' (last 5 characters)
console.log(str.slice(-5, -1)); // 'Worl' (negative indices)

// substring(start, end) - similar to slice, no negative indices
console.log(str.substring(0, 5)); // 'Hello'
console.log(str.substring(6)); // 'World'

// substr(start, length) - deprecated, don't use
```

### Splitting and joining:

```javascript
// split - string to array
const str = 'apple,banana,orange';
const fruits = str.split(',');
console.log(fruits); // ['apple', 'banana', 'orange']

const words = 'Hello World'.split(' ');
console.log(words); // ['Hello', 'World']

const chars = 'Hello'.split('');
console.log(chars); // ['H', 'e', 'l', 'l', 'o']

// Limit splits
console.log(str.split(',', 2)); // ['apple', 'banana']
```

### Trimming:

```javascript
const str = '  Hello World  ';

console.log(str.trim()); // 'Hello World'
console.log(str.trimStart()); // 'Hello World  '
console.log(str.trimEnd()); // '  Hello World'
```

### Replacing:

```javascript
const str = 'Hello World Hello';

// replace - replaces first occurrence
console.log(str.replace('Hello', 'Hi')); // 'Hi World Hello'

// replaceAll - replaces all occurrences
console.log(str.replaceAll('Hello', 'Hi')); // 'Hi World Hi'

// With regex
console.log(str.replace(/Hello/g, 'Hi')); // 'Hi World Hi' (g = global)
```

### Padding:

```javascript
const str = '5';

console.log(str.padStart(3, '0')); // '005'
console.log(str.padEnd(3, '0')); // '500'

const price = '9.99';
console.log(price.padStart(10, ' ')); // '      9.99'
```

### Repeating:

```javascript
console.log('Ha'.repeat(3)); // 'HaHaHa'
console.log('-'.repeat(10)); // '----------'
```

### Template literals:

```javascript
const name = 'John';
const age = 25;

// Old way
const message1 = 'My name is ' + name + ' and I am ' + age;

// Template literal
const message2 = `My name is ${name} and I am ${age}`;

// Multi-line
const multiLine = `
  Line 1
  Line 2
  Line 3
`;

// Expressions
const result = `2 + 2 = ${2 + 2}`; // '2 + 2 = 4'
```

### String immutability:

```javascript
const str = 'Hello';
str[0] = 'h'; // Doesn't work - strings are immutable
console.log(str); // 'Hello' (unchanged)

// Must create new string
const newStr = 'h' + str.slice(1);
console.log(newStr); // 'hello'
```

Interview tip: Know the most common methods: includes, indexOf, slice, split, trim, replace. Remember strings are immutable - all methods return new strings, they don't modify the original."



# Strings & Numbers

## Q26: What are important Number methods and the Math object?

### How to Answer:

"JavaScript has built-in methods for working with numbers and the Math object for mathematical operations.

### Number conversion:

```javascript
// String to number
console.log(Number('123')); // 123
console.log(Number('12.5')); // 12.5
console.log(Number('hello')); // NaN

console.log(parseInt('123')); // 123
console.log(parseInt('123.99')); // 123 (integer only)
console.log(parseInt('123px')); // 123 (stops at non-digit)

console.log(parseFloat('12.5')); // 12.5
console.log(parseFloat('12.5.5')); // 12.5

// Using + operator
console.log(+'123'); // 123
console.log(+'12.5'); // 12.5
```

### Checking for numbers:

```javascript
console.log(isNaN(NaN)); // true
console.log(isNaN('hello')); // true (converts to NaN)
console.log(isNaN(123)); // false

console.log(Number.isNaN(NaN)); // true
console.log(Number.isNaN('hello')); // false (doesn't convert)

console.log(isFinite(123)); // true
console.log(isFinite(Infinity)); // false
console.log(Number.isFinite('123')); // false (strict check)

console.log(Number.isInteger(123)); // true
console.log(Number.isInteger(12.5)); // false
```

### Number formatting:

```javascript
const num = 123.456789;

// toFixed - fixed decimal places (returns string)
console.log(num.toFixed(2)); // '123.46' (rounds)
console.log(num.toFixed(0)); // '123'

// toPrecision - significant figures (returns string)
console.log(num.toPrecision(4)); // '123.5'
console.log(num.toPrecision(2)); // '1.2e+2' (scientific notation)

// toString - convert to string
console.log(num.toString()); // '123.456789'
console.log((255).toString(16)); // 'ff' (hexadecimal)
console.log((8).toString(2)); // '1000' (binary)
```

### Math object - Basic operations:

```javascript
// Rounding
console.log(Math.round(4.5)); // 5 (rounds to nearest)
console.log(Math.round(4.4)); // 4
console.log(Math.ceil(4.1)); // 5 (rounds up)
console.log(Math.floor(4.9)); // 4 (rounds down)
console.log(Math.trunc(4.9)); // 4 (removes decimal)

// Absolute value
console.log(Math.abs(-5)); // 5
console.log(Math.abs(5)); // 5

// Min and Max
console.log(Math.min(5, 10, 2, 8)); // 2
console.log(Math.max(5, 10, 2, 8)); // 10

const numbers = [5, 10, 2, 8];
console.log(Math.min(...numbers)); // 2 (spread array)
```

### Math object - Powers and roots:

```javascript
console.log(Math.pow(2, 3)); // 8 (2^3)
console.log(2 ** 3); // 8 (ES6 exponentiation operator)

console.log(Math.sqrt(16)); // 4 (square root)
console.log(Math.cbrt(27)); // 3 (cube root)
```

### Math object - Random numbers:

```javascript
// Random between 0 (inclusive) and 1 (exclusive)
console.log(Math.random()); // 0.123456...

// Random integer between min and max (inclusive)
function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

console.log(getRandomInt(1, 10)); // Random number between 1-10

// Random from array
const colors = ['red', 'blue', 'green'];
const randomColor = colors[Math.floor(Math.random() * colors.length)];
```

### Math constants:

```javascript
console.log(Math.PI); // 3.141592653589793
console.log(Math.E); // 2.718281828459045 (Euler's number)
```

### Practical examples:

Calculating percentage:

```javascript
function calculatePercentage(value, total) {
  return ((value / total) * 100).toFixed(2) + '%';
}

console.log(calculatePercentage(45, 200)); // '22.50%'
```

Random color:

```javascript
function getRandomColor() {
  const r = Math.floor(Math.random() * 256);
  const g = Math.floor(Math.random() * 256);
  const b = Math.floor(Math.random() * 256);
  return `rgb(${r}, ${g}, ${b})`;
}
```

Rounding to nearest multiple:

```javascript
function roundToNearest(value, multiple) {
  return Math.round(value / multiple) * multiple;
}

console.log(roundToNearest(47, 5)); // 45
console.log(roundToNearest(53, 5)); // 55
```

Interview tip: Know parseInt vs parseFloat, understand toFixed returns a string, and be able to generate random numbers in a range. Math.random() is very commonly asked in interviews."


# Map & Set

## Q27: What are Map and Set? How are they different from Objects and Arrays?

### How to Answer:

"Map and Set are data structures introduced in ES6. They're similar to Objects and Arrays but with important differences.

### Map - Key-value pairs (like Object):

Map is like an Object but with more flexibility in keys.

```javascript
// Creating a Map
const map = new Map();

// Adding entries
map.set('name', 'John');
map.set('age', 30);
map.set(1, 'number key');
map.set(true, 'boolean key');

// Can even use objects as keys!
const keyObj = { id: 1 };
map.set(keyObj, 'object key');

// Getting values
console.log(map.get('name')); // 'John'
console.log(map.get(1)); // 'number key'

// Checking existence
console.log(map.has('name')); // true
console.log(map.has('city')); // false

// Deleting
map.delete('age');

// Size
console.log(map.size); // 4

// Clear all
// map.clear();
```

### Map vs Object:

| Feature | Map | Object |
| --- | --- | --- |
| Keys | Any type | String/Symbol only |
| Order | Insertion order | No guarantee |
| Size | map.size | Object.keys(obj).length |
| Iteration | Built-in | Need Object.keys() |
| Performance | Better for frequent add/remove | Better for simple lookups |

### Iterating Map:

```javascript
const map = new Map([
  ['name', 'John'],
  ['age', 30],
  ['city', 'NYC']
]);

// forEach
map.forEach((value, key) => {
  console.log(`${key}: ${value}`);
});

// for...of
for (const [key, value] of map) {
  console.log(`${key}: ${value}`);
}

// Just keys
for (const key of map.keys()) {
  console.log(key);
}

// Just values
for (const value of map.values()) {
  console.log(value);
}

// Entries
for (const [key, value] of map.entries()) {
  console.log(key, value);
}
```

### Converting between Map and Object:

```javascript
// Object to Map
const obj = { name: 'John', age: 30 };
const map = new Map(Object.entries(obj));

// Map to Object
const mapToObj = Object.fromEntries(map);
```

### Set - Unique values (like Array but unique):

Set stores unique values only.

```javascript
// Creating a Set
const set = new Set();

// Adding values
set.add(1);
set.add(2);
set.add(3);
set.add(2); // Ignored, already exists
set.add(1); // Ignored

console.log(set); // Set { 1, 2, 3 }

// From array
const set2 = new Set([1, 2, 3, 2, 1]);
console.log(set2); // Set { 1, 2, 3 }

// Checking existence
console.log(set.has(2)); // true
console.log(set.has(5)); // false

// Deleting
set.delete(2);

// Size
console.log(set.size); // 2

// Clear all
// set.clear();
```

### Set vs Array:

| Feature | Set | Array |
| --- | --- | --- |
| Duplicates | No | Yes |
| Order | Insertion order | Index order |
| Access | No index access | array[0] |
| Lookup | O(1) | O(n) |
| Use case | Unique values | Ordered list |

### Iterating Set:

```javascript
const set = new Set([1, 2, 3]);

// forEach
set.forEach(value => {
  console.log(value);
});

// for...of
for (const value of set) {
  console.log(value);
}

// Convert to array
const arr = [...set];
// or
const arr2 = Array.from(set);
```

### Practical use cases:

#### 1. Remove duplicates from array:

```javascript
const numbers = [1, 2, 3, 2, 4, 1, 5];
const unique = [...new Set(numbers)];
console.log(unique); // [1, 2, 3, 4, 5]
```

#### 2. Count unique values:

```javascript
const fruits = ['apple', 'banana', 'apple', 'orange', 'banana'];
const uniqueCount = new Set(fruits).size;
console.log(uniqueCount); // 3
```

#### 3. Check for common elements:

```javascript
const set1 = new Set([1, 2, 3, 4]);
const set2 = new Set([3, 4, 5, 6]);

// Intersection
const intersection = new Set([...set1].filter(x => set2.has(x)));
console.log(intersection); // Set { 3, 4 }

// Union
const union = new Set([...set1, ...set2]);
console.log(union); // Set { 1, 2, 3, 4, 5, 6 }

// Difference
const difference = new Set([...set1].filter(x => !set2.has(x)));
console.log(difference); // Set { 1, 2 }
```

#### 4. Map with object keys:

```javascript
const userRoles = new Map();

const user1 = { id: 1, name: 'John' };
const user2 = { id: 2, name: 'Jane' };

userRoles.set(user1, 'admin');
userRoles.set(user2, 'user');

console.log(userRoles.get(user1)); // 'admin'
```

### When to use what:

#### Use Map when:

- Need key-value pairs with non-string keys
- Need to maintain insertion order
- Frequently adding/removing entries
- Need to iterate in order

#### Use Set when:

- Need unique values only
- Need to check existence quickly
- Remove duplicates from data
- Set operations (union, intersection)

#### Use Object when:

- Simple key-value with string keys
- JSON serialization needed
- Property access with dot notation

#### Use Array when:

- Need order with duplicates
- Need index access
- Array methods (map, filter, reduce)

Interview tip: The main advantages are: Map allows any type as key and maintains order; Set automatically enforces uniqueness. Show practical examples like removing duplicates or using objects as keys."



# Loops

## Q28: What are the different types of loops in JavaScript?

### How to Answer:

"JavaScript has several ways to iterate over data, each with specific use cases.

### 1. for loop - Traditional:

Most flexible, good for any counting or iteration.

```javascript
for (let i = 0; i < 5; i++) {
  console.log(i); // 0, 1, 2, 3, 4
}

// Can go backwards
for (let i = 5; i > 0; i--) {
  console.log(i); // 5, 4, 3, 2, 1
}

// Can skip elements
for (let i = 0; i < 10; i += 2) {
  console.log(i); // 0, 2, 4, 6, 8
}
```

### 2. while loop - Condition-based:

Continues while condition is true. Good when you don't know iterations beforehand.

```javascript
let i = 0;
while (i < 5) {
  console.log(i); // 0, 1, 2, 3, 4
  i++;
}

// Be careful of infinite loops
let x = 0;
while (x < 10) {
  console.log(x);
  // Forgot to increment - infinite loop!
}
```

### 3. do...while loop - Execute at least once:

Like while, but checks condition after execution. Runs at least once.

```javascript
let i = 0;
do {
  console.log(i); // 0, 1, 2, 3, 4
  i++;
} while (i < 5);

// Executes once even if condition false
let x = 10;
do {
  console.log(x); // 10 (runs once)
} while (x < 5);
```

### 4. for...of loop - Iterate values:

Iterates over values of iterables (arrays, strings, Maps, Sets).

```javascript
const arr = [10, 20, 30];

for (const value of arr) {
  console.log(value); // 10, 20, 30
}

// With strings
const str = 'hello';
for (const char of str) {
  console.log(char); // 'h', 'e', 'l', 'l', 'o'
}

// With index (using entries)
for (const [index, value] of arr.entries()) {
  console.log(index, value); // 0 10, 1 20, 2 30
}
```

### 5. for...in loop - Iterate keys:

Iterates over object properties (keys). Also works with arrays but not recommended.

```javascript
const person = {
  name: 'John',
  age: 30,
  city: 'NYC'
};

for (const key in person) {
  console.log(key, person[key]);
  // 'name' 'John'
  // 'age' 30
  // 'city' 'NYC'
}

// With arrays (not recommended)
const arr = [10, 20, 30];
for (const index in arr) {
  console.log(index, arr[index]); // Index is string!
}
```

### 6. forEach - Array method:

Executes function for each element. Cannot break or return.

```javascript
const numbers = [1, 2, 3, 4];

numbers.forEach((value, index, array) => {
  console.log(value); // 1, 2, 3, 4
});

// Can't break out
numbers.forEach(value => {
  if (value === 2) {
    // break; // Error! Can't use break
    // return; // Only skips this iteration
  }
  console.log(value);
});
```

### Comparison:

### for vs for...of:

```javascript
const arr = [10, 20, 30];

// for - traditional, need index
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]);
}

// for...of - cleaner, direct access
for (const value of arr) {
  console.log(value);
}
```

### for...of vs for...in:

```javascript
const arr = [10, 20, 30];

// for...of - values
for (const value of arr) {
  console.log(value); // 10, 20, 30
}

// for...in - keys/indices
for (const key in arr) {
  console.log(key); // '0', '1', '2' (strings!)
}
```

### Breaking out of loops:

```javascript
// break - exit loop completely
for (let i = 0; i < 10; i++) {
  if (i === 5) {
    break; // Stops at 5
  }
  console.log(i); // 0, 1, 2, 3, 4
}

// continue - skip current iteration
for (let i = 0; i < 5; i++) {
  if (i === 2) {
    continue; // Skip 2
  }
  console.log(i); // 0, 1, 3, 4
}
```

### Nested loops:

```javascript
// Multiplication table
for (let i = 1; i <= 3; i++) {
  for (let j = 1; j <= 3; j++) {
    console.log(`${i} x ${j} = ${i * j}`);
  }
}
```

### When to use which:

#### Use for when:

- Need index
- Need to control step size
- Need to go backwards

#### Use while when:

- Iterations depend on condition
- Don't know how many iterations

#### Use for...of when:

- Iterating array values
- Working with iterables
- Don't need index

#### Use for...in when:

- Iterating object properties
- Need to access keys

#### Use forEach when:

- Simple array iteration
- Don't need to break
- Functional style

Interview tip: Know the difference between for...of (values) and for...in (keys). Explain that for...of is modern and cleaner for arrays, while for...in is for objects. Mention that forEach can't break/continue."

# Strict Mode

## Q29: What is 'use strict' and why use it?

### How to Answer:

"'use strict' is a directive that enables strict mode in JavaScript. It catches common coding mistakes and prevents certain actions, making code more secure and optimized.

### How to enable:

```javascript
// Strict mode for entire script
'use strict';

function test() {
  // All code here is strict
}

// Or strict mode for single function
function strictFunc() {
  'use strict';
  // Only this function is strict
}
```

### What strict mode does:

1. Prevents accidental globals.
2. Prevents duplicate parameters.
3. Makes assignments to non-writable properties throw errors.
4. Prevents deleting non-deletable properties.
5. Requires `this` to be `undefined` in functions (not `window`).
6. Prevents `with` statement.

### Benefits:

- Catches errors early - mistakes become errors
- Prevents bad practices - no accidental globals
- Better performance - engines can optimize better
- Safer code - prevents some unsafe actions
- Future-proof - reserved keywords for future JS

### Common in modern code:

ES6 modules and classes are automatically strict:

```javascript
// Automatically strict mode
export function test() {
  // This is strict mode
}

class MyClass {
  // This is strict mode
}
```

Interview tip: Explain that strict mode catches common mistakes like accidental globals and makes JavaScript more secure. Mention that ES6 modules are automatically strict. It's generally recommended for modern code."

# Typeof Operator

## Q30: What is the `typeof` operator and what does it return?

### How to Answer:

"`typeof` is an operator that returns a string indicating the type of a value.

### Basic usage:

```javascript
typeof 42;           // 'number'
typeof 'hello';      // 'string'
typeof true;         // 'boolean'
typeof undefined;    // 'undefined'
typeof null;         // 'object' (this is a bug!)
typeof {};           // 'object'
typeof [];           // 'object'
typeof function(){}; // 'function'
typeof Symbol();     // 'symbol'
typeof 123n;         // 'bigint'
```

### Checking types:

```javascript
const value = 42;

if (typeof value === 'number') {
  console.log('It\'s a number');
}

function isString(val) {
  return typeof val === 'string';
}
```

### Special cases:

#### 1. `null` is `'object'` (JavaScript bug):

```javascript
typeof null; // 'object' (should be 'null')

// Correct way to check for null
const value = null;
if (value === null) {
  console.log('It\'s null');
}
```

#### 2. Arrays are `'object'`:

```javascript
typeof []; // 'object'

// Correct way to check for arrays
Array.isArray([]); // true
```

#### 3. Functions are `'function'` (even though they're objects):

```javascript
typeof function(){}; // 'function'
```

### Complete return values:

```javascript
typeof undefined;      // 'undefined'
typeof true;           // 'boolean'
typeof 42;             // 'number'
typeof '42';           // 'string'
typeof Symbol();       // 'symbol'
typeof 123n;           // 'bigint'
typeof {};             // 'object'
typeof [];             // 'object'
typeof null;           // 'object' (bug!)
typeof function(){};   // 'function'
```

### `typeof` vs `instanceof`:

```javascript
// typeof - primitive type
typeof 'hello'; // 'string'

// instanceof - object type
'hello' instanceof String; // false (primitive)
new String('hello') instanceof String; // true (object)

const arr = [];
typeof arr;              // 'object'
arr instanceof Array;    // true
```

Interview tip: Know all return values and mention that `typeof null` returns `'object'` (it's a famous JavaScript bug). Use `Array.isArray()` for arrays since `typeof` returns `'object'`."


# Optional Chaining & Nullish Coalescing

## Q31: What is optional chaining (`?.`) and nullish coalescing (`??`)?

### How to Answer:
"Optional chaining is a feature in JavaScript that allows you to safely access deeply nested
"properties or methods of an object without causing an error if a property doesn’t exist

### Optional Chaining (`?.`):

Safely access nested properties without checking each level.

### Problem it solves:

```javascript
// Without optional chaining
const user = getUser();
let city;

if (user && user.address && user.address.city) {
  city = user.address.city;
}

// Lots of checking!
```

### With optional chaining:

```javascript
const city = user?.address?.city;

// If user is null/undefined, returns undefined
// If address is null/undefined, returns undefined
// Otherwise, returns city
```

### How it works:

```javascript
const user = {
  name: 'John',
  address: {
    city: 'NYC'
  }
};

console.log(user?.address?.city); // 'NYC'
console.log(user?.address?.zip); // undefined (no error)
console.log(user?.contact?.phone); // undefined (contact doesn't exist)

const noUser = null;
console.log(noUser?.address?.city); // undefined (no error)
```

### With arrays:

```javascript
const users = [{ name: 'John' }, { name: 'Jane' }];

console.log(users?.[0]?.name); // 'John'
console.log(users?.[10]?.name); // undefined (no error)
```

### With functions:

```javascript
const obj = {
  method: function() {
    return 'Hello';
  }
};

console.log(obj.method?.()); // 'Hello'
console.log(obj.missing?.()); // undefined (no error, doesn't call)
```

### Nullish Coalescing (`??`):

Returns right side only if left side is null or undefined.

### Problem it solves:

```javascript
// Using || (OR operator) - problem with falsy values
const port = config.port || 3000;

// If port is 0, uses 3000 (bug!)
// If port is '', uses 3000 (bug!)
// If port is false, uses 3000 (bug!)
```

### With nullish coalescing:

```javascript
const port = config.port ?? 3000;

// Only uses 3000 if port is null or undefined
// 0, '', false are kept!
```

### Comparison:

```javascript
const values = {
  zero: 0,
  empty: '',
  nullVal: null,
  undefinedVal: undefined,
  false: false
};

// With OR (||)
console.log(values.zero || 'default'); // 'default' (0 is falsy)
console.log(values.empty || 'default'); // 'default' ('' is falsy)
console.log(values.false || 'default'); // 'default'

// With Nullish Coalescing (??)
console.log(values.zero ?? 'default'); // 0 (keeps 0!)
console.log(values.empty ?? 'default'); // '' (keeps ''!)
console.log(values.false ?? 'default'); // false (keeps false!)
console.log(values.nullVal ?? 'default'); // 'default'
console.log(values.undefinedVal ?? 'default'); // 'default'
```

### When to use `??` vs `||`:

```javascript
// Use || when you want to replace ALL falsy values
const name = userName || 'Guest';
// '' → 'Guest' ✓
// null → 'Guest' ✓

// Use ?? when you ONLY want to replace null/undefined
const count = userCount ?? 0;
// 0 → 0 ✓ (keeps 0)
// null → 0 ✓
// undefined → 0 ✓
```

### Combining both:

```javascript
// Practical example - deeply nested optional data
const streetName = user?.address?.street?.name ?? 'Unknown';

// If any level is null/undefined, use 'Unknown'
```

### Real-world example:

```javascript
// API response handling
function getUserCity(response) {
  return response?.data?.user?.address?.city ?? 'City not provided';
}

// Configuration with defaults
function getConfig(options) {
  return {
    timeout: options?.timeout ?? 5000,
    retries: options?.retries ?? 3,
    debug: options?.debug ?? false
  };
}

getConfig({ timeout: 0 }); // { timeout: 0, retries: 3, debug: false }
// Note: timeout 0 is kept, not replaced!
```

### Benefits:

- Safer code - no more nested if checks
- More readable - clear intent
- Less verbose - fewer lines
- Handles edge cases - 0, '', false preserved with ??

Interview tip: Explain that optional chaining (`?.`) safely accesses nested properties, and nullish coalescing (`??`) provides defaults only for null/undefined (not all falsy values like `||` does). Show practical example with API data or configuration objects."

# Q32 Template Literals

## 13. Template Literals

### Definition

Template Literals are a modern way to create strings in JavaScript using backticks (` `) instead of quotes. They allow variable interpolation, expressions, and multi-line strings easily.

```javascript
`string text ${expression}`
```

### 1. Variable Interpolation - What it means

You can insert variables directly inside a string using `${}`.

#### Example

```javascript
let name = "Rahul";
let age = 22;

let message = `My name is ${name} and I am ${age} years old.`;

console.log(message);
```

### 2. Embed Expressions Inside Template Literals

You can also write any JavaScript expression inside `${}`.

#### Example

```javascript
let a = 10;
let b = 20;

console.log(`Sum is ${a + b}`);
```

### 3. Multi-line Strings

Template literals support multi-line text without using `\n`.

### 4. Function Calls Inside Template Literals

You can call functions inside `${}`.

---

# Error Handling

## 33. How does JavaScript handle errors?

JavaScript handles errors using `try…catch` blocks to catch problems, `throw` to create custom errors, and `finally` to run code no matter what.


# Q33 Memory Leaks in JavaScript

## 4. Explain memory leaks in JavaScript and how to prevent them?

A memory leak occurs when unused memory is not released because it is still referenced.

It can be prevented by clearing timers, removing event listeners, avoiding global variables, and cleaning up unused references.

JavaScript has Garbage Collection (GC), but leaks still happen when:

- Objects are still referenced
- GC thinks they are still in use

If something is reachable (referenced), GC will **NOT** remove it.

### Result:

- App becomes slow
- High memory usage
- Browser tab crashes

### 1. Global Variables (Most Common)

```javascript
function foo() {
  data = { name: "Rahul" }; // ❌ no var / let / const
}

foo();
```

#### ❌ Problem

- `data` becomes a global variable
- Lives for entire app lifetime

#### ✅ Fix

```javascript
function foo() {
  const data = { name: "Rahul" };
}
```

### 2. Forgotten Timers (`setInterval`, `setTimeout`)

```javascript
setInterval(() => {
  console.log("running");
}, 1000);
```

#### ❌ Problem

- Interval keeps running forever
- References are never released

#### ✅ Fix

```javascript
const id = setInterval(() => {
  console.log("running");
}, 1000);

clearInterval(id);
```

### 3. Closures Holding Large Data

```javascript
function heavy() {
  const bigArray = new Array(1e6);

  return function () {
    console.log("hello");
  };
}

const fn = heavy();
```

#### ❌ Problem

- Closure keeps `bigArray` in memory
- Even though it's not used

#### ✅ Fix

```javascript
bigArray = null; // release memory
```

## 🔹 How to Prevent Memory Leaks (Best Practices)

- ✅ Use `let` / `const`
- ✅ Clear timers and intervals
- ✅ Remove event listeners
- ✅ Avoid unnecessary global variables
- ✅ Null out unused references
- ✅ Be careful with closures
- ✅ Clean up in React (`useEffect` cleanup)




# Q34 Garbage Collection in JavaScript

## 8. How does garbage collection work in JavaScript?

Garbage Collection is the process by which JavaScript automatically frees memory that is no longer needed.

### GC starts checking memory from these roots.

- Global scope
- Current execution context
- Call stack
- Closures

These are called **roots**.

## Main Garbage Collection Algorithm

### Mark-and-Sweep

### 1. Mark Phase

- Start from roots
- Mark all reachable objects

### 2. Sweep Phase

- Delete all unmarked (unreachable) objects
- Free memory



# Rendering Strategies

## Q35. What is the difference between SSR, CSR, and SSG (from a JS perspective)?

“SSR, CSR, and SSG are different rendering strategies that define where and when JavaScript runs to generate HTML.

In CSR, rendering happens in the browser using JavaScript after the page loads.

In SSR, HTML is generated on the server for every request and then hydrated by JavaScript in the browser.

In SSG, HTML is generated at build time and served as static files, with JavaScript adding interactivity afterward.”

**CSR → Baad me, SSR → Request pe, SSG → Pehle se ready**

### In details:

## 1. CSR (Client Side Rendering)

🗣 “JS browser me page banata hai”

- Browser downloads minimal HTML
- JS bundle loads
- JS renders UI using DOM APIs / React
- API calls happen in browser

```javascript
// React CSR
useEffect(() => {
  fetch("/api/products").then(...);
}, []);
```

- Server → Empty HTML
- Browser → JS load
- JS → Data laata hai
- JS → Page banata hai

---

## 2. SSR – Server Side Rendering

### How it works (JS view):

- Request hits server
- Server runs JS (Node.js)
- HTML is generated per request
- Browser receives HTML
- JS hydrates the page

- Request → Server JS run
- Server → Ready HTML
- Browser → Page dikha
- JS → Button click kaam kare

### Real life example

- 👉 Amazon product page
- 👉 Turant content dikh jata hai
- 👉 Phir Add to Cart kaam karta hai

### Simple line

🗣 “JS server pe page banata hai”

```javascript
// Next.js SSR
export async function getServerSideProps() {
  const data = await fetchData();
  return { props: { data } };
}
```

---

## 3. SSG – Static Site Generation

🗣 “JS ek baar build time pe page banata hai”

### How it works (JS view):

- JS runs at build time
- HTML files are pre-generated
- Served via CDN
- JS hydrates for interactivity

### Real life example

- 👉 Blog website
- 👉 Turant open hoti hai
- 👉 Content same rehta hai

```javascript
// Next.js SSG
export async function getStaticProps() {
  const data = await fetchData();
  return { props: { data } };
}
```

---

# JavaScript Performance Optimization

## 15. How would you optimize a JavaScript-heavy web application?

“To optimize a JavaScript-heavy web application, I try to make the app load faster and run smoother.

I do this by reducing how much JavaScript is loaded, avoiding unnecessary re-renders, and making sure heavy work does not block the browser.”

### 1. Load Less JavaScript

- Remove unused code
- Load code only when needed (lazy loading)

👉 Less JS = faster page load

### 2. Avoid Unnecessary Re-Rendering

- Update UI only when data really changes
- Use memoization if needed

👉 Less re-render = smoother app

### 3. Do Heavy Work in Background

- Move heavy calculations to Web Workers
- Use debounce/throttle for events like scroll, search

👉 UI stays responsive

### 4. Use Browser Caching

- Cache API responses
- Use CDN and browser cache

👉 Faster repeat visits

### 5. Use Proper Rendering Strategy

- Use SSR / SSG for faster first paint & SEO
- Hydrate only when needed (partial hydration)


