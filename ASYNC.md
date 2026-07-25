# Table of Contents

1. Introduction to Async JavaScript
2. Call Stack & Synchronous Execution
3. setTimeout & setInterval
4. Callbacks & Callback Hell
5. Promises
6. Promise Methods (all, race, allSettled, any)
7. Promise Chaining
8. async/await
9. Error Handling in Async Code
10. fetch API
11. Event Loop
12. Microtasks vs Macrotasks
13. Global Execution Context
14. Closures in Async Context
15. this Keyword with Callbacks
16. Modules (import/export)
17. NPM & NPX Basics
18. Throttling & Debouncing
19. AbortController
20. Common Patterns & Best Practices



# Introduction to Async JavaScript

## Q1: What is Asynchronous JavaScript and why do we need it?

### How to Answer:

> "JavaScript is a single-threaded language, which means it can only execute one thing at a time. But if we had to wait for every operation to complete before moving to the next one - like waiting for a file to load or an API call to finish - our applications would freeze and become unusable.
>
> That's where asynchronous JavaScript comes in. It allows us to start long-running operations and continue executing other code while waiting for those operations to complete.
>
> For example, imagine you're making a request to fetch user data from a server. Without async code, your entire application would freeze until the server responds. With async code, you can make the request and continue doing other things - like handling user clicks or updating the UI - while waiting for the response.
>
> The main scenarios where we use async JavaScript are:
>
> - Making API calls with fetch or AJAX
> - Reading or writing files
> - Setting timers with setTimeout or setInterval
> - Database operations
> - Any operation that takes time and we don't want to block the main thread
>
> JavaScript achieves this using mechanisms like callbacks, promises, and async/await, all working together with the event loop."

### Key Point

> "Even though JavaScript is single-threaded, it can handle asynchronous operations by delegating time-consuming tasks to Web APIs and using the event loop to manage when their callbacks should run."

---

## Q2: What does it mean that JavaScript is single-threaded?

### How to Answer:

> "When we say JavaScript is single-threaded, it means it has only one call stack and can execute only one piece of code at a time. Think of it like having only one worker who can do only one task at a time.
>
> For example:"

```javascript
console.log('First');
console.log('Second');
console.log('Third');
```

> "These will always execute in order - First, Second, Third - because JavaScript processes them one by one on a single thread.
>
> However, this doesn't mean JavaScript can't handle multiple operations. The browser provides Web APIs that run outside of JavaScript's single thread - things like setTimeout, fetch, DOM events. These operations happen in the background, and when they're done, their callbacks are queued up to be executed on the main thread.
>
> So JavaScript itself is single-threaded, but it works in an environment (the browser or Node.js) that provides multi-threading capabilities for certain operations. The event loop coordinates everything to make it seem like multiple things are happening at once, even though JavaScript code itself only runs one thing at a time."


# Call Stack & Synchronous Execution

## Q3: What is the Call Stack and how does it work?

### How to Answer:

> "The call stack is a data structure that keeps track of function calls in your program. It works on a Last In, First Out (LIFO) principle - like a stack of plates where you can only add or remove from the top.
>
> When a function is called, it gets pushed onto the call stack. When the function returns, it gets popped off the stack.
>
> Let me show you with an example:"

```javascript
function third() {
  console.log('Third function');
}

function second() {
  third();
  console.log('Second function');
}

function first() {
  second();
  console.log('First function');
}

first();
```

> "Here's what happens step by step:
>
> 1. first() is called → pushed onto stack
> 2. Inside first(), second() is called → pushed onto stack
> 3. Inside second(), third() is called → pushed onto stack
> 4. third() executes and returns → popped off stack
> 5. Back in second(), console.log runs, then it returns → popped off stack
> 6. Back in first(), console.log runs, then it returns → popped off stack
> 7. Stack is empty
>
> The output would be:"

```text
Third function
Second function
First function
```

### Important point

> "JavaScript can only execute code when the call stack is not empty. When the stack is empty, it checks the task queue for any pending callbacks. This is how the event loop works."

---

## Q4: What happens when the call stack overflows?

### How to Answer:

> "A stack overflow happens when you have too many function calls on the stack, usually from infinite recursion - when a function keeps calling itself without a proper exit condition.
>
> For example:"

```javascript
function recurse() {
  recurse(); // Calls itself forever
}

recurse(); // Error: Maximum call stack size exceeded
```

> "Each call to recurse() adds a new frame to the call stack, but nothing ever gets popped off because the function never returns. Eventually, the stack runs out of space and JavaScript throws a 'Maximum call stack size exceeded' error.
>
> This is different from an infinite loop:"

```javascript
while (true) {
  console.log('Loop');
}
```

> "An infinite loop doesn't cause a stack overflow because it doesn't keep adding function calls to the stack. It just keeps executing the same code. But it will freeze your program because the call stack never becomes empty, so the event loop can't process other tasks.
>
> The way to avoid stack overflow is to always have a base case in recursive functions:"

```javascript
function countdown(n) {
  if (n <= 0) return; // Base case
  console.log(n);
  countdown(n - 1);
}

countdown(5); // Works fine
```

> "Now the recursion has an exit condition, so the stack doesn't grow infinitely."


# setTimeout & setInterval

## Q5: How does setTimeout work?

### How to Answer:

> "setTimeout is a function that executes a callback after a specified delay. The syntax is:"

```javascript
setTimeout(callback, delay);
```

> "For example:"

```javascript
console.log('Start');

setTimeout(() => {
  console.log('This runs after 2 seconds');
}, 2000);

console.log('End');
```

> "The output will be:"

```text
Start
End
This runs after 2 seconds
```

> "Notice that 'End' prints before the setTimeout callback, even though the setTimeout comes first in the code. This is because setTimeout is asynchronous.
>
> Here's what happens:
>
> 1. 'Start' is logged
> 2. setTimeout is called - it registers the callback with the Web API and sets a timer for 2000ms
> 3. JavaScript continues immediately to the next line (doesn't wait)
> 4. 'End' is logged
> 5. After 2 seconds, the callback is moved to the task queue
> 6. When the call stack is empty, the event loop picks up the callback and executes it
>
> Important points:
>
> The delay is not guaranteed to be exact. It's the minimum time before the callback can run. If the call stack is busy, the callback will have to wait:"

```javascript
setTimeout(() => {
  console.log('Should run after 0ms');
}, 0);

// Some heavy operation
for (let i = 0; i < 1000000000; i++) {}

console.log('Heavy operation done');
```

> "Even with 0ms delay, the setTimeout callback runs after the loop finishes because the loop blocks the call stack."

---

## Q6: What's the difference between setTimeout and setInterval?

### How to Answer:

> "Both are timer functions, but they work differently:
>
> setTimeout - Executes the callback once after the specified delay:"

```javascript
setTimeout(() => {
  console.log('Runs once after 2 seconds');
}, 2000);
```

> "setInterval - Executes the callback repeatedly at the specified interval:"

```javascript
setInterval(() => {
  console.log('Runs every 2 seconds');
}, 2000);
```

> "setInterval keeps running until you explicitly stop it using clearInterval:"

```javascript
const intervalId = setInterval(() => {
  console.log('Tick');
}, 1000);

// Stop after 5 seconds
setTimeout(() => {
  clearInterval(intervalId);
  console.log('Interval stopped');
}, 5000);
```

> "Important consideration: setInterval doesn't wait for the callback to finish before scheduling the next one. If your callback takes longer than the interval, you might have issues:"

```javascript
setInterval(() => {
  // If this takes 3 seconds but interval is 2 seconds,
  // calls can pile up
  someSlowOperation();
}, 2000);
```

> "A safer pattern is using setTimeout recursively:"

```javascript
function repeatTask() {
  someSlowOperation();
  setTimeout(repeatTask, 2000); // Schedule next call after this one finishes
}

repeatTask();
```

> "This ensures there's always a 2-second gap between the end of one call and the start of the next."

---

## Q7: What is setTimeout 0 and why would you use it?

### How to Answer:

> "setTimeout with 0 delay doesn't execute immediately - it defers the callback until after the current call stack is clear."

```javascript
console.log('1');

setTimeout(() => {
  console.log('2');
}, 0);

console.log('3');

// Output: 1, 3, 2
```

> "Even though the delay is 0, the callback still goes through the event loop. The synchronous code runs first, then the callback executes.
>
> Why use it?
>
> Use case 1: Breaking up long-running tasks"

```javascript
function processLargeArray(array) {
  // Process in chunks to avoid blocking
  const chunk = array.splice(0, 100);

  // Process this chunk
  chunk.forEach(item => processItem(item));

  // If more items, schedule next chunk
  if (array.length > 0) {
    setTimeout(() => processLargeArray(array), 0);
  }
}
```

> "This allows the browser to handle other tasks between chunks, keeping the UI responsive.
>
> Use case 2: Deferring code until DOM is ready"

```javascript
// Sometimes you need to ensure DOM has updated
element.style.display = 'block';

setTimeout(() => {
  // This runs after the browser has painted the change
  element.style.opacity = '1';
}, 0);
```

> "Use case 3: Changing execution order"

```javascript
function doSomething() {
  console.log('Inside function');
}

console.log('Before');
setTimeout(doSomething, 0);
console.log('After');

// Output: Before, After, Inside function
```

> "So setTimeout(0) is a way to say 'do this, but let everything else in the current stack finish first.'"

# Callbacks & Callback Hell

## Q8: What is a callback function?

### How to Answer:

> "A callback is simply a function that you pass as an argument to another function, and it gets called later - usually after some operation completes.
>
> The simplest example:"

```javascript
function greet(name, callback) {
  console.log('Hello ' + name);
  callback();
}

function sayGoodbye() {
  console.log('Goodbye!');
}

greet('John', sayGoodbye);

// Output:
// Hello John
// Goodbye!
```

> "Callbacks are heavily used in asynchronous operations because they allow you to specify what should happen after an async operation completes:"

```javascript
// Reading a file (async operation)
readFile('data.txt', function(error, data) {
  if (error) {
    console.log('Error reading file');
  } else {
    console.log('File content:', data);
  }
});

console.log('This runs before the file is read');
```

> "Here, the callback function is called once the file reading is complete. Meanwhile, JavaScript continues executing other code.
>
> Types of callbacks:
>
> Synchronous callbacks - Execute immediately:"

```javascript
[1, 2, 3].forEach(function(num) {
  console.log(num); // Runs immediately for each item
});
```

> "Asynchronous callbacks - Execute later:"

```javascript
setTimeout(function() {
  console.log('Runs after 1 second'); // Runs later
}, 1000);
```

> "The key concept is that callbacks let you say 'when this is done, do that' - which is essential for handling async operations."

---

## Q9: What is Callback Hell and how do you avoid it?

### How to Answer:

> "Callback hell, also called 'pyramid of doom,' happens when you have multiple nested callbacks, making the code hard to read and maintain.
>
> Here's an example:"

```javascript
getData(function(a) {
  getMoreData(a, function(b) {
    getMoreData(b, function(c) {
      getMoreData(c, function(d) {
        getMoreData(d, function(e) {
          console.log('Finally done!');
        });
      });
    });
  });
});
```

> "Look at how the code keeps indenting to the right - that's the 'pyramid.' It's hard to read, hard to debug, and hard to handle errors.
>
> Problems with callback hell:
>
> - Hard to read - The logic is buried in nested functions
> - Error handling nightmare - You need to handle errors at each level
> - Hard to maintain - Adding or removing steps is difficult
> - Difficult to debug - Stack traces become confusing
>
> Solutions:
>
> Solution 1: Named functions"

```javascript
function step1(data, callback) {
  // Process data
  callback(null, processedData);
}

function step2(data, callback) {
  // Process data
  callback(null, processedData);
}

step1(initialData, function(err, result1) {
  if (err) return handleError(err);
  step2(result1, function(err, result2) {
    if (err) return handleError(err);
    // Continue...
  });
});
```

> "Solution 2: Promises (better)"

```javascript
getData()
  .then(getMoreData)
  .then(getMoreData)
  .then(getMoreData)
  .then(result => {
    console.log('Done!', result);
  })
  .catch(error => {
    console.log('Error:', error);
  });
```

> "Solution 3: async/await (best)"

```javascript
async function fetchData() {
  try {
    const a = await getData();
    const b = await getMoreData(a);
    const c = await getMoreData(b);
    const d = await getMoreData(c);
    console.log('Done!', d);
  } catch (error) {
    console.log('Error:', error);
  }
}
```

> "Modern JavaScript strongly favors promises and async/await over nested callbacks because they make async code look and behave more like synchronous code."


# Promises

## Q10: What is a Promise in JavaScript?

### How to Answer:

> "A Promise is an object that represents the eventual completion or failure of an asynchronous operation. Think of it as a placeholder for a value that we don't have yet but will have in the future.
>
> A promise can be in one of three states:
>
> - Pending - Initial state, the operation hasn't completed yet
> - Fulfilled - The operation completed successfully
> - Rejected - The operation failed
>
> Here's how you create a promise:" :contentReference[oaicite:0]{index=0}

```javascript
const myPromise = new Promise((resolve, reject) => {
  // Do some async operation
  setTimeout(() => {
    const success = true;

    if (success) {
      resolve('Operation successful!'); // Fulfilled
    } else {
      reject('Operation failed!'); // Rejected
    }
  }, 1000);
});
```

> "And here's how you use it:" :contentReference[oaicite:1]{index=1}

```javascript
myPromise
  .then(result => {
    console.log(result); // Runs if fulfilled
  })
  .catch(error => {
    console.log(error); // Runs if rejected
  });
```

> "Why promises are better than callbacks:
>
> - Cleaner syntax - No deep nesting
> - Better error handling - One .catch() can handle errors from any step
> - Chainable - You can chain multiple async operations easily
> - Built-in state management - The promise keeps track of whether it's pending, fulfilled, or rejected
>
> Important points:
>
> - A promise can only be resolved or rejected once - it can't change after that
> - You can't 'cancel' a promise once it's created
> - Promises always resolve or reject asynchronously, even if the operation is synchronous" :contentReference[oaicite:2]{index=2}

---

## Q11: How do you create and use Promises?

### How to Answer:

> "Creating a promise involves using the Promise constructor with an executor function that takes two parameters: resolve and reject.
>
> Creating a Promise:" :contentReference[oaicite:3]{index=3}

```javascript
function fetchUserData(userId) {
  return new Promise((resolve, reject) => {
    // Simulate API call
    setTimeout(() => {
      if (userId > 0) {
        resolve({ id: userId, name: 'John' });
      } else {
        reject('Invalid user ID');
      }
    }, 1000);
  });
}
```

> "Using a Promise with .then() and .catch():" :contentReference[oaicite:4]{index=4}

```javascript
fetchUserData(1)
  .then(user => {
    console.log('User:', user);
    return user.name; // Can return value for next .then()
  })
  .then(name => {
    console.log('User name:', name);
  })
  .catch(error => {
    console.log('Error:', error);
  });
```

> "Using .finally():" :contentReference[oaicite:5]{index=5}

```javascript
fetchUserData(1)
  .then(user => console.log(user))
  .catch(error => console.log(error))
  .finally(() => {
    console.log('Cleanup - runs whether success or failure');
  });
```

> "Real-world example - wrapping an async operation:" :contentReference[oaicite:6]{index=6}

```javascript
function delay(ms) {
  return new Promise(resolve => {
    setTimeout(resolve, ms);
  });
}

// Usage
console.log('Start');
delay(2000)
  .then(() => {
    console.log('2 seconds later');
  });
```

> "Key patterns:
>
> - Always return promises from functions so they can be chained
> - Always handle errors with .catch()
> - Return values from .then() to pass data to the next .then()
> - Use .finally() for cleanup code that should always run
>
> The beauty of promises is that they make async code more readable and manageable compared to nested callbacks." :contentReference[oaicite:7]{index=7}

---

## Q12: What's the difference between Promise.resolve() and Promise.reject()?

### How to Answer:

> "These are convenience methods for creating promises that are already fulfilled or rejected.
>
> Promise.resolve() - Creates a promise that's immediately fulfilled with a value:" :contentReference[oaicite:8]{index=8}

```javascript
const promise = Promise.resolve('Hello');

promise.then(value => {
  console.log(value); // 'Hello'
});

// Equivalent to:
const promise = new Promise(resolve => {
  resolve('Hello');
});
```

> "Promise.reject() - Creates a promise that's immediately rejected with a reason:" :contentReference[oaicite:9]{index=9}

```javascript
const promise = Promise.reject('Something went wrong');

promise.catch(error => {
  console.log(error); // 'Something went wrong'
});

// Equivalent to:
const promise = new Promise((resolve, reject) => {
  reject('Something went wrong');
});
```

> "When to use them:
>
> Use Promise.resolve() when:" :contentReference[oaicite:10]{index=10}

```javascript
function getData(value) {
  // If value is already a promise, return it
  // If it's a regular value, wrap it in a promise
  return Promise.resolve(value);
}

getData(42).then(console.log); // 42
getData(Promise.resolve(42)).then(console.log); // 42
```

> "Use Promise.reject() when:" :contentReference[oaicite:11]{index=11}

```javascript
function validateUser(user) {
  if (!user.email) {
    return Promise.reject('Email is required');
  }
  return Promise.resolve(user);
}
```

> "These are particularly useful in functions where you sometimes need to return a promise and sometimes have the value immediately. They ensure consistent return types." :contentReference[oaicite:12]{index=12}

---

# Promise Methods

## Q13: Explain Promise.all() and when to use it.

### How to Answer:

> "Promise.all() takes an array of promises and returns a single promise that resolves when all the input promises have resolved, or rejects if any one of them rejects.
>
> Basic usage:" :contentReference[oaicite:13]{index=13}

```javascript
const promise1 = fetch('/api/users');
const promise2 = fetch('/api/posts');
const promise3 = fetch('/api/comments');

Promise.all([promise1, promise2, promise3])
  .then(([users, posts, comments]) => {
    console.log('All data loaded:', users, posts, comments);
  })
  .catch(error => {
    console.log('One of the requests failed:', error);
  });
```

> "Key characteristics:
>
> - All or nothing - If any promise rejects, the entire Promise.all() rejects immediately
> - Maintains order - Results are in the same order as the input promises
> - Runs in parallel - All promises execute concurrently, not sequentially
>
> When to use Promise.all():
>
> Use it when you have multiple independent async operations and you need all of them to complete before proceeding:" :contentReference[oaicite:14]{index=14}

```javascript
async function loadDashboard() {
  try {
    const [user, settings, notifications] = await Promise.all([
      fetchUser(),
      fetchSettings(),
      fetchNotifications()
    ]);

    renderDashboard({ user, settings, notifications });
  } catch (error) {
    showError('Failed to load dashboard');
  }
}
```

> "Important gotcha:" :contentReference[oaicite:15]{index=15}

```javascript
const promises = [
  Promise.resolve(1),
  Promise.reject('Error'),
  Promise.resolve(3)
];

Promise.all(promises)
  .then(results => console.log(results))
  .catch(error => console.log(error)); // 'Error'

// We never get results for promise 1 and 3!
```

> "If you need to handle some failures gracefully, use Promise.allSettled() instead.
>
> Performance benefit:
>
> Instead of this (sequential):" :contentReference[oaicite:16]{index=16}

```javascript
const user = await fetchUser();      // 1 second
const posts = await fetchPosts();    // 1 second
const comments = await fetchComments(); // 1 second
// Total: 3 seconds
```

> "Use this (parallel):" :contentReference[oaicite:17]{index=17}

```javascript
const [user, posts, comments] = await Promise.all([
  fetchUser(),
  fetchPosts(),
  fetchComments()
]);
// Total: 1 second (all run at once)
```

> "So Promise.all() is great for performance when you have multiple independent operations." :contentReference[oaicite:18]{index=18}

---

## Q14: What's the difference between Promise.all, Promise.race, Promise.allSettled, and Promise.any?

### How to Answer:

> "These are four different ways to handle multiple promises, each with different behavior:
>
> Promise.all() - Waits for ALL promises to fulfill, or rejects if ANY rejects:" :contentReference[oaicite:19]{index=19}

```javascript
Promise.all([promise1, promise2, promise3])
  .then(results => {
    // All succeeded - results is array of all values
  })
  .catch(error => {
    // Any one failed - error is the first rejection
  });
```

> "Use when: You need all results and any failure should stop everything.
>
> Promise.race() - Returns as soon as ANY promise settles (fulfills or rejects):" :contentReference[oaicite:20]{index=20}

```javascript
Promise.race([promise1, promise2, promise3])
  .then(result => {
    // First one to finish - result is from the fastest
  })
  .catch(error => {
    // First one to fail - error from the fastest
  });
```

> "Use when: You want the fastest result, like implementing timeouts:" :contentReference[oaicite:21]{index=21}

```javascript
Promise.race([
  fetch('/api/data'),
  new Promise((_, reject) =>
    setTimeout(() => reject('Timeout'), 5000)
  )
]);
```

> "Promise.allSettled() - Waits for ALL promises to settle, never rejects:" :contentReference[oaicite:22]{index=22}

```javascript
Promise.allSettled([promise1, promise2, promise3])
  .then(results => {
    // Always succeeds - results is array of objects:
    // { status: 'fulfilled', value: ... }
    // { status: 'rejected', reason: ... }

    results.forEach(result => {
      if (result.status === 'fulfilled') {
        console.log('Success:', result.value);
      } else {
        console.log('Failed:', result.reason);
      }
    });
  });
```

> "Use when: You want to try all operations and handle successes/failures individually.
>
> Promise.any() - Returns as soon as ANY promise fulfills, rejects only if ALL reject:" :contentReference[oaicite:23]{index=23}

```javascript
Promise.any([promise1, promise2, promise3])
  .then(result => {
    // First one to succeed - result is from first fulfillment
  })
  .catch(error => {
    // All failed - error is AggregateError with all rejections
  });
```

> "Use when: You have multiple sources for the same data (like multiple CDNs) and want the first successful one:" :contentReference[oaicite:24]{index=24}

```javascript
Promise.any([
  fetch('https://cdn1.example.com/image.jpg'),
  fetch('https://cdn2.example.com/image.jpg'),
  fetch('https://cdn3.example.com/image.jpg')
]).then(response => {
  // Use the first successful response
});
```

> "Quick comparison:" :contentReference[oaicite:25]{index=25}

| Method | Resolves when | Rejects when |
| --- | --- | --- |
| Promise.all | All fulfill | Any rejects |
| Promise.race | Any settles | First to reject |
| Promise.allSettled | All settle | Never |
| Promise.any | Any fulfills | All reject |

> "Most common in interviews: Promise.all() and Promise.race() are asked most often, but knowing allSettled() and any() shows deeper understanding." :contentReference[oaicite:26]{index=26}

---

# Promise Chaining

## Q15: How does Promise chaining work?

### How to Answer:

> "Promise chaining is when you chain multiple .then() calls together. Each .then() returns a new promise, so you can keep chaining.
>
> Basic chaining:" :contentReference[oaicite:27]{index=27}

```javascript
fetch('/api/user')
  .then(response => response.json())
  .then(user => {
    console.log('User:', user);
    return fetch(`/api/posts/${user.id}`);
  })
  .then(response => response.json())
  .then(posts => {
    console.log('Posts:', posts);
  })
  .catch(error => {
    console.log('Error at any step:', error);
  });
```

> "Key concepts:
>
> 1. Each .then() returns a new promise:" :contentReference[oaicite:28]{index=28}

```javascript
const promise1 = fetch('/api/data');
const promise2 = promise1.then(response => response.json());
const promise3 = promise2.then(data => processData(data));
// Each step is a new promise
```

> "2. Return values become the next promise's value:" :contentReference[oaicite:29]{index=29}

```javascript
Promise.resolve(5)
  .then(value => {
    console.log(value); // 5
    return value * 2;
  })
  .then(value => {
    console.log(value); // 10
    return value + 3;
  })
  .then(value => {
    console.log(value); // 13
  });
```

> "3. If you return a promise, it's automatically unwrapped:" :contentReference[oaicite:30]{index=30}

```javascript
getUserId()
  .then(id => {
    return fetch(`/api/users/${id}`); // Returns a promise
  })
  .then(response => {
    // response is the fetch result, not a promise
    return response.json();
  })
  .then(user => {
    console.log(user);
  });
```

> "4. Error propagates down the chain:" :contentReference[oaicite:31]{index=31}

```javascript
doStep1()
  .then(doStep2)
  .then(doStep3)
  .then(doStep4)
  .catch(error => {
    // Catches error from any step above
    console.log('Error:', error);
  });
```

> "5. You can recover from errors:" :contentReference[oaicite:32]{index=32}

```javascript
fetch('/api/data')
  .then(response => response.json())
  .catch(error => {
    console.log('Error, using fallback data');
    return { fallback: true }; // Recovery
  })
  .then(data => {
    // This runs whether we got real data or fallback
    console.log(data);
  });
```

> "Important gotcha - not returning the promise:" :contentReference[oaicite:33]{index=33}

```javascript
// Wrong - promise not returned
fetch('/api/user')
  .then(response => {
    fetch('/api/posts'); // Missing return!
  })
  .then(posts => {
    console.log(posts); // undefined!
  });

// Correct
fetch('/api/user')
  .then(response => {
    return fetch('/api/posts'); // Return the promise
  })
  .then(response => response.json())
  .then(posts => {
    console.log(posts); // Works!
  });
```

> "Promise chaining is much cleaner than callback hell, but async/await makes it even cleaner." :contentReference[oaicite:34]{index=34}


