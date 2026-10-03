
# Node.js Notes

## Q1. What is Node.js, and how does its runtime architecture differ from running JavaScript in a browser?

### Answer

Node.js is a JavaScript runtime environment that allows us to execute JavaScript outside the browser, mainly for backend and server-side development. It uses Google's **V8** JavaScript engine to execute JavaScript code.

**Node.js** uses a single-threaded event-driven architecture with a **non-blocking I/O** model. It uses the **Event Loop** and underlying system mechanisms to handle multiple I/O operations efficiently without blocking the main JavaScript thread.

The main difference from a browser is the environment and the available APIs. In a browser, JavaScript runs inside the browser and has access to APIs like `DOM`, `window`, and `document`. In **Node.js**, there is no `DOM` or `window`; instead, Node provides server-side APIs for things like file systems, networking, HTTP servers, streams, and processes.

So, the `V8` engine executes JavaScript in both environments, but the runtime environment around `V8` is different.

JavaScript execution in **Node.js** primarily happens on a single main thread, but **Node.js** can handle many concurrent I/O operations using its **Event Loop** and underlying system mechanisms.

`Node.js` = `V8` JavaScript engine + `Node.js` runtime APIs + Event-driven, non-blocking I/O architecture. 


## Q2. Explain the Node.js Event Loop in detail — name and describe each of its phases (timers, pending callbacks, idle/prepare, poll, check, close callbacks).

### Answer

The **Node.js Event Loop** is responsible for handling asynchronous operations without blocking the main JavaScript thread. It continuously checks for callbacks or tasks that are ready to execute and processes them through different phases of the **Event Loop** at the appropriate time.

- JavaScript runs mainly on one thread.
- Some operations, like file I/O, network requests, and timers, can take time.
- Instead of waiting for them and blocking JavaScript, **Node.js** handles them asynchronously.
- When those operations are ready, their callbacks are placed in the appropriate queues.
- The **Event Loop** continuously checks these queues and executes the callbacks according to its phases.

#### (a) Timers Phase

The Timers phase executes callbacks for timers such as: `setTimeout()`, `setInterval()`

After the timer's delay has elapsed, its callback becomes eligible to run during the timers phase.

`setTimeout(1000)` does not mean: "Execute exactly after 1000 ms."
It means: "Do not execute before approximately 1000 ms; execute when the **Event Loop** gets the opportunity."

The timers phase executes callbacks scheduled by `setTimeout` and `setInterval` when their specified time has elapsed.

#### (b) Pending Callbacks Phase

Pending callbacks phase handles certain I/O callbacks that were deferred from the previous iteration of the **Event Loop**. These callbacks are executed during the pending callbacks phase of the next iteration.

> **Note:** Kuch I/O callbacks agar current round mein execute nahi ho paaye, toh unhe next round mein Pending Callbacks phase handle kar sakta hai.

#### (c) Idle / Prepare Phase

This phase is mainly used internally by **Node.js**. It is not something developers normally interact with directly. **Node.js** performs internal preparation work before entering the poll phase.

#### (d) Poll Phase

The poll phase is responsible for retrieving and executing I/O-related callbacks. If there is no immediate work, the **Event Loop** can wait in the poll phase for new I/O events.

For example:
- File system operations
- Network operations
- Incoming connections
- Database/network-related I/O callbacks

The Poll phase also determines whether it should:
1. Execute available I/O callbacks.
2. Wait for new I/O events.
3. Move to the next phase when appropriate.

#### (e) Check Phase

The check phase executes callbacks scheduled by `setImmediate()`.

#### (f) Close Callbacks Phase

The close callbacks phase handles callbacks for closed resources, such as sockets.


socket.on("close", () => {
  console.log("Socket closed");
});


## Q3. What is the difference between blocking and non-blocking I/O? How does Node.js achieve non-blocking behavior on a single thread?

### Answer

Blocking I/O means the program waits for an I/O operation to finish before doing the next task.
Non-blocking I/O means the program does not wait. It starts the I/O operation and continues doing other work.

**Node.js** provides non-blocking behavior using the Event Loop and `libuv`. When **Node.js** gets an I/O task, it sends the task to the operating system or `libuv`'s thread pool. The main JavaScript thread does not wait for the task to finish.

When the task is completed, its callback is given to the Event Loop, and the Event Loop runs that callback.

So, even though **Node.js** runs JavaScript on a single main thread, it can handle many I/O operations without waiting for each one to finish.

---

## Q4. Explain the difference between process.nextTick(), setImmediate(), and setTimeout(fn, 0) — and their relative execution order?

### Answer

`process.nextTick()`, `setImmediate()`, and `setTimeout(fn, 0)` all schedule callbacks, but they work differently.

`process.nextTick()` runs very soon after the current code finishes, before the Event Loop continues to the next phase.

`setTimeout(fn, 0)` schedules the callback for the Timers phase, while `setImmediate()` schedules the callback for the Check phase.

`process.nextTick()` generally runs before both of them. The order between `setTimeout(fn, 0)` and `setImmediate()` is not always fixed when they are called from the main script. However, inside an I/O callback, `setImmediate()` normally runs before `setTimeout(fn, 0)`.


## Q5. What is libuv, and what role does it play in Node.js's concurrency model?

### Answer

`libuv` is a library used by **Node.js** to handle asynchronous operations. It helps **Node.js** perform I/O operations without blocking the main JavaScript thread.

`libuv` provides the Event Loop and also has a thread pool for certain operations, such as some file system and DNS tasks. When an asynchronous task is completed, the Event Loop helps run its callback.

Because of this, **Node.js** can handle many I/O operations while JavaScript continues running on the main thread.

---

## Q6. What are Streams in Node.js? Explain the four stream types (Readable, Writable, Duplex, Transform) with a real use case for each.

### Answer

Streams in **Node.js** are used to handle data piece by piece instead of loading the complete data into memory. They are useful when working with large files, videos, network data, and uploads or downloads.

There are four main types of streams:
- **Readable Stream** is used to read data, for example, reading a large video file.
- **Writable Stream** is used to write data, for example, writing data to a file.
- **Duplex Stream** can both read and write data, for example, a TCP socket.
- **Transform Stream** can read data, change or process it, and then produce new data. For example, gzip compression.

The main benefit of streams is that they save memory because we don't need to load the complete data at once.


## Q7. What is the Buffer class in Node.js, and why is it needed when JavaScript already has strings?

### Answer

`Buffer` is a class in **Node.js** used to work with binary data. It stores data as bytes. It is commonly used when working with files, images, videos, audio, and network data.

JavaScript strings are mainly used for text, but **Node.js** also needs to work with binary data. That's why **Node.js** provides `Buffer`.

For example, when we read an image or a file without specifying an encoding, **Node.js** can return the data as a `Buffer`.

---

## Q8. Explain the CommonJS module system (require/module.exports) vs ES Modules (import/export) in Node.js — differences and interop issues?

### Answer

**Node.js** supports two main module systems: `CommonJS` and `ES Modules`.

`package.json`: `{ "type": "module" }` — This tells **Node.js** to treat `.js` files as ES Modules. Without `"type": "module"`, `.js` files are traditionally treated as `CommonJS`.

`CommonJS` uses `require()` to import modules and `module.exports` to export them. `ES Modules` use `import` and `export`.

`CommonJS` is the traditional module system used in **Node.js**, while `ES Modules` are the standard JavaScript module system.

**Node.js** supports both, but when we mix them, there can be interop issues because they use different ways of importing and exporting modules. For example, a `CommonJS` file may need dynamic `import()` to load an ES Module.

`ESM` provide static structure:
- `import` and `export` are known before the code actually runs.
- This helps tools analyze dependencies and perform optimizations such as tree shaking.

Example:

// math.js
export const add = () => {};
export const subtract = () => {};
export const multiply = () => {};

## Q9. What is the purpose of package.json vs package-lock.json? What problem does the lock file solve?

### Answer

`package.json` contains information about the project, including its dependencies, scripts, and configuration. `package-lock.json` records the exact versions of the installed packages and their dependencies. The main purpose of `package-lock.json` is to make installations consistent. It ensures that developers, CI/CD, and production environments install the same dependency versions, which helps avoid 'works on my machine' problems.

---

## Q10. How does error handling differ across callbacks, Promises, and async/await? What happens to an unhandled Promise rejection in Node.js?

### Answer

### Callbacks

In the callback approach, we usually use an error-first callback.

Example:

fs.readFile("data.txt", "utf8", (err, data) => {
if (err) {
console.log("Error:", err);
return;
}
console.log(data);
});


## Q11. Explain the EventEmitter class. How would you build a custom class that emits and listens to events?

### Answer

`EventEmitter` is a class provided by **Node.js** that allows objects to:
- emit (send/trigger) events — Event ko trigger/announce karna.
- listen to events — Is event ko suno. Jab ye event aaye, ye function chalao.
- handle events when they occur.

Example:

const EventEmitter = require("events");
const emitter = new EventEmitter();

emitter.on("registered", () => {
  console.log("Send welcome email");
});

emitter.emit("registered");


## Q12. What are child processes in Node.js? Differentiate between fork(), spawn(), exec(), and execFile()?

### Answer

Child processes allow our **Node.js** or Express application to create another process to do some work separately. We can use them to run external commands, programs, or heavy tasks.

- `spawn()` is used when we want to receive output continuously.
- `exec()` is used to run a command and get the complete output.
- `execFile()` is used to run a specific executable directly.
- `fork()` is used to create another **Node.js** process, and the parent and child can communicate using messages.


## Q13. What is clustering in Node.js, and how does the cluster module help utilize multi-core CPUs?

### Answer

Node.js clustering means running multiple worker processes of the same **Node.js** application. Normally, **Node.js** uses one main process for JavaScript execution. The `cluster` module allows us to create multiple workers, so multiple CPU cores can be utilized. This is useful for handling high traffic because different workers can handle incoming requests.

### Key Points for Your Notes

- **Clustering** = using multiple **Node.js** processes — ek machine ke multiple CPU cores use karne mein help karta hai.
- **Load Balancer** → multiple machines/servers ke beech traffic distribute karta hai.
- **Node.js** JavaScript execution is normally single-threaded.
- `cluster` is a built-in **Node.js** module.
- `cluster.fork()` creates worker processes.
- Workers can listen on the same port.
- Multiple workers can use multiple CPU cores.
- Useful for high-traffic applications.
- Cluster = multiple processes, not multiple JavaScript threads.
- For modern **Node.js** applications, clustering is only one option; worker threads are another option for CPU-intensive work.

---

## Q14. What are Worker Threads, and how do they differ from clustering and from child processes?

### Answer

Cluster ka main purpose: Multiple CPU cores ka use karke **Node.js** server ki request-handling capacity badhana.
Worker Thread ka main use hai CPU-intensive JavaScript work ko main thread se alag karna.

Worker Threads are used to run JavaScript code in separate threads inside a **Node.js** process. They are mainly useful for CPU-intensive tasks because they prevent the main thread from being blocked. Clustering creates multiple **Node.js** processes, usually to utilize multiple CPU cores and handle more server traffic. Child processes also create separate processes, but they are mainly used when we want to run another program, command, or separate **Node.js** process.

## Q15. What is middleware in Express.js? Explain how the request-response cycle flows through next() and how error-handling middleware differs from regular middleware

### Answer

Middleware in Express.js is a function that runs between the client request and the final response.

It has access to the request (`req`), response (`res`), and the `next()` function.

Middleware is commonly used for things like:
* Authentication
* Logging
* Validation
* Request processing
* Error handling

---

## How does next() work?

When a request comes to the Express server, it passes through middleware one by one.

For example:

```javascript
app.use((req, res, next) => {
  console.log("Middleware 1");

  next();
});

app.use((req, res, next) => {
  console.log("Middleware 2");

  next();
});

app.get("/users", (req, res) => {
  res.json({ message: "Users data" });
});

`next()` tells Express: "I have finished my work, now move to the next middleware or route handler."

If middleware does not call `next()` and also does not send a response, the request can remain stuck.

## Error-Handling Middleware

Error-handling middleware is different from normal middleware.

Normal middleware has 3 parameters:
* `(req, res, next)`

Error-handling middleware has 4 parameters:
* `(err, req, res, next)`

The first parameter, `err`, tells Express that this middleware is specifically for handling errors.

app.use((err, req, res, next) => {
  console.error(err);

  res.status(500).json({
    message: "Something went wrong"
  });
});

If an error is passed using:

next(error);

Express skips normal middleware and moves to the error-handling middleware.


















# How to Handle 1 Million Requests in an Express.js Application

## Q1. If an application receives around 1 million requests and the server load is very high, how would you handle it?

### Answer

If my **Express.js** application receives around 1 million requests, I would not depend on a single server. I would use scaling, load balancing, caching, database optimization, and background processing to handle the high traffic.

1. Use a **Load Balancer**

A **Load Balancer** distributes incoming requests across multiple servers.

```text
1 Million Requests 
↓ 
Load Balancer 
↙   ↓   ↘ 
Server1 Server2 Server3