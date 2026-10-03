# Node.js Interview Notes

## Q1. What is Node.js, and how does its runtime architecture differ from running JavaScript 
in a browser ? 
Node.js is a JavaScript runtime environment that allows us to execute JavaScript outside the 
browser, mainly for backend and server-side development. It uses Google's V8 JavaScript 
engine to execute JavaScript code  
Node.js uses a single-threaded event-driven architecture with a non-blocking I/O model. It uses 
the Event Loop and underlying system mechanisms to handle multiple I/O operations efficiently 
without blocking the main JavaScript thread. 
The main difference from a browser is the environment and the available APIs. In a browser, 
JavaScript runs inside the browser and has access to APIs like DOM, window, and document. 
In Node.js, there is no DOM or window; instead, Node provides server-side APIs for things like 
file systems, networking, HTTP servers, streams, and processes. 
So, the V8 engine executes JavaScript in both environments, but the runtime environment 
around V8 is different. 
JavaScript execution in Node.js primarily happens on a single main thread, but Node.js can 
handle many concurrent I/O operations using its Event Loop and underlying system mechanisms.   
Node.js = V8 JavaScript engine + Node.js runtime APIs + Event-driven, non-blocking I/O 
architecture.  

## Q2. Explain the Node.js Event Loop in detail — name and describe each of its phases 
(timers, pending callbacks, idle/prepare, poll, check, close callbacks). 
The Node.js Event Loop is responsible for handling asynchronous operations without blocking 
the main JavaScript thread. It continuously checks for callbacks or tasks that are ready to 
execute and processes them through different phases of the Event Loop at the appropriate time.. 
JavaScript runs mainly on one thread. 
Some operations, like file I/O, network requests, and timers, can take time. 
Instead of waiting for them and blocking JavaScript, Node.js handles them asynchronously. 
When those operations are ready, their callbacks are placed in the appropriate queues. 
The Event Loop continuously checks these queues and executes the callbacks according to 
its phases. 
(a) Timers Phase   
The Timers phase executes callbacks for timers such as: setTimeout(), setInterval() 
After the timer's delay has elapsed, its callback becomes eligible to run during the timers phase. 
setTimeout(1000) does not mean:- "Execute exactly after 1000 ms. 
It means: "Do not execute before approximately 1000 ms; execute when the Event Loop gets 
the opportunity." 
The timers phase executes callbacks scheduled by setTimeout and setInterval when their 
specified time has elapsed. 
(b) Pending Callbacks Phase  
Pending callbacks phase handles certain I/O callbacks that were deferred from the previous 
iteration of the Event Loop. These callbacks are executed during the pending callbacks phase of 
the next iteration  
Kuch I/O callbacks agar current round mein execute nahi ho paaye, toh unhe next round mein 
Pending Callbacks phase handle kar sakta hai  
© Idle / Prepare Phase  
This phase is mainly used internally by Node.js.It is not something developers normally interact 
with directly. Node.js performs internal preparation work before entering the poll phase. 
(d) Poll Phase  
The poll phase is responsible for retrieving and executing I/O-related callbacks. If there is no 
immediate work, the Event Loop can wait in the poll phase for new I/O events. 
For example: 
● File system operations 
● Network operations 
● Incoming connections 
● Database/network-related I/O callbacks 
The Poll phase also determines whether it should: 
1. Execute available I/O callbacks. 
2. Wait for new I/O events. 
3. Move to the next phase when appropriate. 
(e) Check Phase  
The check phase executes callbacks scheduled by setImmediate()  
(f) Close Callbacks Phase  
"The close callbacks phase handles callbacks for closed resources, such as sockets."  
socket.on("close", () => { 
console.log("Socket closed"); 
}); 
Easy memory trick: 
T → P → I → P → C → C 
Timers → Pending → Idle → Poll → Check → Close 

## Q3. What is the difference between blocking and non-blocking I/O? How does Node.js 
achieve non-blocking behavior on a single thread?  
Blocking I/O means the program waits for an I/O operation to finish before doing the next task. 
Non-blocking I/O means the program does not wait. It starts the I/O operation and continues 
doing other work. 
Node.js provides non-blocking behavior using the Event Loop and libuv. When Node.js gets an 
I/O task, it sends the task to the operating system or libuv's thread pool. The main JavaScript 
thread does not wait for the task to finish. 
When the task is completed, its callback is given to the Event Loop, and the Event Loop runs 
that callback. 
So, even though Node.js runs JavaScript on a single main thread, it can handle many I/O 
operations without waiting for each one to finish. 

## Q4. Explain the difference between process.nextTick(), setImmediate(), and 
setTimeout(fn, 0) — and their relative execution order ? 
"process.nextTick(), setImmediate(), and setTimeout(fn, 0) all schedule 
callbacks, but they work differently. 
process.nextTick() runs very soon after the current code finishes, before the Event Loop 
continues to the next phase. 
setTimeout(fn, 0) schedules the callback for the Timers phase, while setImmediate() 
schedules the callback for the Check phase. 
process.nextTick() generally runs before both of them. The order between 
setTimeout(fn, 0) and setImmediate() is not always fixed when they are called from 
the main script. However, inside an I/O callback, setImmediate() normally runs before 
setTimeout(fn, 0)." 

## Q5. What is libuv, and what role does it play in Node.js's concurrency model ? 
libuv is a library used by Node.js to handle asynchronous operations. It helps Node.js perform 
I/O operations without blocking the main JavaScript thread. 
libuv provides the Event Loop and also has a thread pool for certain operations, such as some 
file system and DNS tasks. When an asynchronous task is completed, the Event Loop helps run 
its callback. 
Because of this, Node.js can handle many I/O operations while JavaScript continues running on 
the main thread." 

## Q6. What are Streams in Node.js? Explain the four stream types (Readable, Writable, 
Duplex, Transform) with a real use case for each.  
Streams in Node.js are used to handle data piece by piece instead of loading the complete data 
into memory. They are useful when working with large files, videos, network data, and uploads 
or downloads. 
There are four main types of streams. 
Readable Stream is used to read data, for example, reading a large video file. 
Writable Stream is used to write data, for example, writing data to a file. 
Duplex Stream can both read and write data, for example, a TCP socket. 
Transform Stream can read data, change or process it, and then produce new data. For 
example, gzip compression. 
The main benefit of streams is that they save memory because we don't need to load the 
complete data at once." 

## Q7. What is the Buffer class in Node.js, and why is it needed when JavaScript already has 
strings ? 
Buffer is a class in Node.js used to work with binary data. It stores data as bytes. It is commonly 
used when working with files, images, videos, audio, and network data. 
JavaScript strings are mainly used for text, but Node.js also needs to work with binary data. 
That's why Node.js provides Buffer. 
For example, when we read an image or a file without specifying an encoding, Node.js can 
return the data as a Buffer.


## Q8. Explain the CommonJS module system (require/module.exports) vs ES Modules 
(import/export) in Node.js — differences and interop issues ? 
"Node.js supports two main module systems: CommonJS and ES Modules. 
Package.json: – { "type": "module" } – This tells Node.js to treat .js files as ES Modules. 
Without "type": "module", .js files are traditionally treated as CommonJS. 
CommonJS uses require() to import modules and module.exports to export them. ES 
Modules use import and export. 
CommonJS is the traditional module system used in Node.js, while ES Modules are the 
standard JavaScript module system. 
Node.js supports both, but when we mix them, there can be interop issues because they use 
different ways of importing and exporting modules. For example, a CommonJS file may need 
dynamic import() to load an ES Module. 
ESM provide Static structure  
● import and export are known before the code actually runs. 
● This helps tools analyze dependencies and perform optimizations such as tree shaking. 
● // math.js 
● export const add = () => {}; 
● export const subtract = () => {}; 
● export const multiply = () => {}; 
import { add } from "./math.js"; - use kar rahe ho, bundler unused subtract aur multiply ko 
final production bundle se remove kar sakta hai.  

## Q9. What is the purpose of package.json vs package-lock.json? What problem does the 
lock file solve?  
package.json contains information about the project, including its dependencies, scripts, and 
configuration. package-lock.json records the exact versions of the installed packages and 
their dependencies. The main purpose of package-lock.json is to make installations 
consistent. It ensures that developers, CI/CD, and production environments install the same 
dependency versions, which helps avoid 'works on my machine' problems." 

## Q10. How does error handling differ across callbacks, Promises, and async/await? What 
happens to an unhandled Promise rejection in Node.js ? 
Callbacks 
In the callback approach, we usually use an error-first callback. 
Example: 
fs.readFile("data.txt", "utf8", (err, data) => { 
if (err) { 
console.log("Error:", err); 
return; 
} 
console.log(data); 
}); 
Here: 
● err contains the error if something goes wrong. 
● data contains the result if successful. 
Error handling is different for callbacks, Promises, and async/await. In callbacks, Node.js 
commonly uses an error-first callback, where we check the err parameter. With Promises, we 
handle errors using .catch(). With async/await, we normally use try...catch because a 
rejected Promise causes the await expression to throw an error.  
What happens to an unhandled Promise rejection? 
An unhandled Promise rejection happens when a Promise is rejected but there is no 
.catch() or other error handling for it. 
Example: 
Promise.reject(new Error("Something went wrong")); 
If we don't handle this rejection, Node.js emits an unhandledRejection event. In current 
Node.js behavior, an unhandled rejection is treated as an uncaught exception by default, so the 
process will generally terminate. 

## Q11. Explain the EventEmitter class. How would you build a custom class that emits and 
listens to events?  
EventEmitter is a class provided by Node.js that allows objects to: 
● emit (send/trigger) events - Event ko trigger/announce karna.  
● listen to events -  Is event ko suno. Jab ye event aaye, ye function chalao.  
● handle events when they occur 
const EventEmitter = require("events"); 
const emitter = new EventEmitter(); 
const EventEmitter = require("events"); 
const emitter = new EventEmitter(); 
emitter.on("registered", () => { 
console.log("Send welcome email"); 
}); 
emitter.emit("registered"); 
Building a Custom Class 
This is the important interview part. 
Suppose we want to create a custom User class that emits a "registered" event. 
const EventEmitter = require("events"); 
class User extends EventEmitter { 
register(name) { 
console.log(`${name} is registered`); 
this.emit("registered", name); }} 
EventEmitter is a Node.js class that allows us to create and handle events. It is available from 
the events module. We can use .on() to listen for an event and .emit() to trigger an event. 
To create a custom class, we can extend the EventEmitter class. Then inside our custom 
class, we can use this.emit() to trigger an event, and outside the class we can use .on() 
to listen for that event. For example, in a User class, when a user is registered, we can emit a 
registered event. A listener can then handle that event, such as sending a welcome email or 
creating a log. 

## Q12. What are child processes in Node.js? Differentiate between fork(), spawn(), exec(), 
and execFile().  
Child processes allow our Node.js or Express application to create another process to do some 
work separately. We can use them to run external commands, programs, or heavy tasks. 
spawn() is used when we want to receive output continuously. 
exec() is used to run a command and get the complete output. 
execFile() is used to run a specific executable directly.  
fork() is used to create another Node.js process, and the parent and child can communicate 
using messages. 

## Q13. What is clustering in Node.js, and how does the cluster module help utilize 
multi-core CPUs?  
"Node.js clustering means running multiple worker processes of the same Node.js application. 
Normally, Node.js uses one main process for JavaScript execution. The cluster module allows 
us to create multiple workers, so multiple CPU cores can be utilized. This is useful for handling 
high traffic because different workers can handle incoming requests."  
Key Points for Your Notes 
● Clustering = using multiple Node.js processes- ek machine ke multiple CPU cores use 
karne mein help karta hai.  
● Load Balancer → multiple machines/servers ke beech traffic distribute karta hai.  
● Node.js JavaScript execution is normally single-threaded. 
● cluster is a built-in Node.js module. 
● cluster.fork() creates worker processes. 
● Workers can listen on the same port. 
● Multiple workers can use multiple CPU cores. 
● Useful for high-traffic applications. 
● Cluster = multiple processes, not multiple JavaScript threads. 
● For modern Node.js applications, clustering is only one option; worker threads are 
another option for CPU-intensive work. 

## 14. What are Worker Threads, and how do they differ from clustering and from child 
processes ?  
Cluster ka main purpose: Multiple CPU cores ka use karke Node.js server ki request-handling 
capacity badhana. 
Worker Thread ka main use hai CPU-intensive JavaScript work ko main thread se alag karna.  
Worker Threads are used to run JavaScript code in separate threads inside a Node.js process. 
They are mainly useful for CPU-intensive tasks because they prevent the main thread from 
being blocked. Clustering creates multiple Node.js processes, usually to utilize multiple CPU 
cores and handle more server traffic. Child processes also create separate processes, but they 
are mainly used when we want to run another program, command, or separate Node.js 
process."  















## How to Handle 1 Million Requests in an Express.js Application 
If an application receives around 1 million requests and the server load is very high, how 
would you handle it? 
Answer 
If my Express.js application receives around 1 million requests, I would not depend on a single 
server. I would use scaling, load balancing, caching, database optimization, and 
background processing to handle the high traffic. 
1. Use a Load Balancer 
A Load Balancer distributes incoming requests across multiple servers. 
1 Million Requests 
↓ 
Load Balancer 
↙   ↓   ↘ 
Server1 Server2 Server3 
Instead of sending all requests to one server, the load balancer distributes them among multiple 
servers. 
Benefit: 
It prevents one server from becoming overloaded. 
2. Run Multiple Node.js / Express Instances 
Node.js normally runs JavaScript on a single main thread. 
If the machine has multiple CPU cores, we can run multiple Node.js processes so that the 
application can use more CPU resources. 
For example, with a 4-core CPU: 
4 CPU Cores 
↓ 
Process ,Process 2, Process 3, Process 4 
Node.js Cluster can be used to create multiple worker processes on the same machine. 
However, for very large traffic, we normally also use multiple servers, not only cluster. 
3. Use Redis for Caching 
If the same data is requested repeatedly, we don't need to query MongoDB every time. 
We can store frequently accessed data in Redis. 
Request 
↓ 
Redis Cache 
↓ 
Data found? 
↙       ↘ 
Yes       
↓         
No 
↓ 
Return   MongoDB 
↓ 
Store in Redis 
Benefit: 
It reduces database load and improves response time. 
4. Optimize the Database 
The database can become a bottleneck when traffic is very high. 
For MongoDB, I would: 
● Create proper indexes. 
● Optimize queries. 
● Avoid unnecessary database calls. 
● Use pagination for large datasets. 
● Use connection pooling. 
● Return only the required fields. 
Example: 
User.find({ email: email }) 
If email is frequently searched, creating an index on email can make the query much more 
efficient. 
5. Use Background Jobs for Heavy Tasks 
Some tasks should not be performed directly inside the request-response cycle. 
For example: 
● Sending emails 
● Generating large PDFs 
● Image/video processing 
● Report generation 
● Other long-running tasks 
We can use a queue and worker system. 
Express API 
     ↓ 
   Queue 
     ↓ 
   Worker 
     ↓ 
Heavy Task 
 
The API can respond quickly while the worker processes the task in the background. 
6. Horizontal Scaling 
If traffic keeps increasing, we can add more servers. 
                Load Balancer 
                /      |      \ 
               ↓       ↓       ↓ 
            Server 1 Server 2 Server 3 
               ↓       ↓       ↓ 
            Express  Express  Express 
Adding more servers is called horizontal scaling. 
This allows the application to handle more traffic. 
                                      Interview Answer 
“If my Express.js application receives around one million requests and the server 
load is high, I would not handle all requests on a single server. I would use a load 
balancer to distribute traffic across multiple servers or Node.js instances. I would 
use Redis for frequently accessed data to reduce database load. I would optimize 
MongoDB queries and indexes. For heavy or long-running tasks, I would use a 
queue and background workers. If traffic increases further, I would horizontally 
scale by adding more servers. I would also monitor CPU, memory, response time, 
errors, and database performance.” 