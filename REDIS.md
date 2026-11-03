# Redis – Definition, Use Cases & Practical Examples

## 1. What is Redis?

Redis is an in-memory key-value database used for super fast data access.

## Important Takeaway

- **MongoDB:** Persistent application data.
- **Redis:** Fast access, caching and temporary data.
- **BullMQ:** Background job processing.
- **TTL:** Automatic key expiration.

Redis improves application performance by reducing repeated database operations and providing fast data access.

## 2. Redis Use Cases & Examples

| Use Case | Example | Purpose |
|---|---|---|
| API Caching | Products / Users | Store frequently requested API responses |
| OTP Storage | Temporary OTP | Store OTP with automatic expiration |
| Sessions | Login Sessions | Maintain user login sessions |
| Rate Limiting | API Protection | Limit the number of API requests |
| Queues | Emails / Background Jobs | Manage asynchronous tasks |
| AI Memory | Chatbot History | Store recent conversation history |

## 3. Practical Examples in MERN Stack

### 3.1 API Caching

**Example:** get dataq.

cache user data in reedis

```javascript
const Redis = require("ioredis")
const redis = new Redis(redis://localhost:6379)
// get redis
app.get("/get-with-redis", async (req, res)=>{
  try{
    // get redis
  
   const cached = await redis.get("user:all")

   if(cached){
    const user = JSON.parse(cached)
    return res.status(200).json({success:true, message:"user:all get successfully", user})
   }

    const user = await User.find({})
  //  set redis
    await redis.set("user:all", JSON.stringify(user))

     res.status(200).json({success:true, message: "user found successfully", user})


  }catch(err){
    res.status(500).json({success: false, message: "not available data", error : err.message})
  }
})

// create
app.post("/create", async (req, res)=>{

   try{
    const { name , email, age } = req.body

    if(!email){
      return res.status(500).json({success:false, message: "user email is required"})
    }
    // delete previous cached data
    await redis.del("user:all")

    let user = await User.create({
      name,
      email,
      age
    })
    res.status(201).json({success:true,message: "user created successfuly", user})

  }catch(err){
    res.status(500).json({success:false, error : err.message})
  }

})
```

**Benefits:**
- Reduces repeated MongoDB queries.
- Improves API response time.
- Reduces database load.

### 3.2 OTP Storage

**Example:** User registration OTP.

```javascript
// generate otp
app.post("/send-otp", async (req, res)=>{
  const { email } = req.body
  const otp= Math.floor(100000 + Math.random()*900000).toString()
  // how will we know that otp created for which user
  //not storing it in db becoz redis provide us easy method to add,delet etc, if i store it in db then we will have to write logic manully. it's one of the  reason
  await redis.set(`otp: ${email}`, otp, "EX",30)
   return res.json({otp})
})
// verify otp
app.post("/verify-otp", async (req, res)=>{
  const { email, otp } = req.body

  const cachedOpt  = await redis.get(`otp: ${email}`)
  if(!cachedOpt){
    return res.status(400).json({message: "otp not found"})
  }

  if(cachedOpt !== otp){
    return res.status(400).json({message: "incorrect otp"})
  }

   return res.json({message: "verified otp"})
})
```

**Benefits:**
- Stores OTP temporarily.
- Automatically expires after 5 minutes.
- Avoids permanently storing temporary OTPs.
- Instead of stroing otp in db we we store it in redis for temoporary.

**Note:** In production, generate secure OTPs and avoid storing them as plain text.

### 3.3 Session Management

**Example:** User login sessions.

- Redis stores session information.
- Sessions can expire automatically.
- Helps manage active user sessions.
- Supports session sharing across multiple backend servers.

### 3.4 Rate Limiting
# Redis Rate Limiting

## 1. What is Rate Limiting?

Rate Limiting is a technique used to control how many requests a user or IP address can make within a specific time period.

**Example:** Allow only 5 API requests per minute from one IP address.

If the user exceeds the limit, the server blocks further requests temporarily.

## 2. Real Flow

```text
1. Request Received
        |
        v
2. Identify User / IP Address
        |
        v
3. Count Requests in Redis
        |
        v
4. Check Request Limit
        |
        v
   Limit Crossed?
      /      \
    No        Yes
    |          |
    v          v
  Allow      Block
  Request    Request
```

## 3. Redis Commands Used

| Redis Command | Work |
|---|---|
| `INCR` | Increase request count by 1 |
| `EXPIRE` | Automatically expire the counter after a specified time |
| `TTL` | Check remaining expiration time |
| `GET` | Retrieve the current request count |
| `DEL` | Delete a request counter |

### INCR

Increases the request counter by 1.

```redis
INCR rate_limit:1.2.3.4
```

### EXPIRE

Sets an expiration time for the counter.

```redis
EXPIRE rate_limit:1.2.3.4 60
```

**Example:** The counter automatically expires after 60 seconds.

## 4. Real-Time Example Using IP Address

Suppose the API allows a maximum of **3 requests per minute**.

**IP Address:** `1.2.3.4`

| Request | Redis Counter | Result |
|---|---|---|
| 1st Request | `1.2.3.4 → 1` | Allow |
| 2nd Request | `1.2.3.4 → 2` | Allow |
| 3rd Request | `1.2.3.4 → 3` | Allow |
| 4th Request | `1.2.3.4 → 4` | Block |

After 60 seconds, the counter expires and the user can make requests again.

## 5. Redis Working Flow

### First Request

```redis
INCR rate_limit:1.2.3.4
```

**Result:**

```text
1.2.3.4 → 1
```

### Set Expiration

```redis
EXPIRE rate_limit:1.2.3.4 60
```

### Next Requests

```text
Request 1 → Count: 1
Request 2 → Count: 2
Request 3 → Count: 3
Request 4 → Count: 4 → BLOCK
```

**Important:** Set the expiration only when the counter is first created. Otherwise, repeatedly calling `EXPIRE` will extend the time window with every request.

## 6. Why Do We Use Redis for Rate Limiting?

- Fast request counting.
- Prevents excessive API requests.
- Protects login and registration APIs.
- Helps prevent brute-force attacks.
- Supports multiple backend servers using shared counters.

### Example: Login API Protection

- Allow only 5 login attempts within a defined time window.
- Track requests using Redis counters.
- Block excessive requests.
- Helps protect against brute-force attacks.

## 7. Types of Attacks and Abuse

| Attack / Abuse | Meaning | Example |
|---|---|---|
| Brute Force Attack | Repeatedly guessing passwords | Trying multiple passwords to access an account |
| Spam | Sending excessive unwanted requests | Repeated API requests |
| DDoS Attack | Overloading a server with traffic from multiple sources | Thousands of requests sent simultaneously |
| Bot Abuse | Automated malicious or unwanted activity | Bots repeatedly calling APIs |

### What Happens Without Protection?

```text
Brute Force
     |
     v
API Spam
     |
     v
Bot Abuse / DDoS
     |
     v
Excessive Server Traffic
     |
     v
Server Slow / Crash
```

**Result:** High server load, slow API responses and possible service unavailability.

## 8. Important Takeaway

Redis Rate Limiting helps control API traffic by counting requests, checking limits and temporarily blocking excessive requests.

**HTTP Status Code:** `429 Too Many Requests` — returned when a client exceeds the allowed request limit.

**Note:** Rate limiting is one layer of protection. It does not independently prevent every DDoS attack.

# Redis Rate Limiting Middleware (Express.js)

## 1. Code Example

```javascript
const rateLimiter = async (req, res, next) => {

  // Gets the client's IP address
  const ip = req.ip
  // Creates a unique Redis key for each IP
  const key = `rate_limit:${ip}`
  // Increases the request counter by 1
  const requests = await redis.incr(key)

  if (requests === 1) {
    await redis.expire(key, 60)
  }
 // Gets the remaining expiration time
  const ttl = await redis.ttl(key)

  if (requests > 5) {
    return res.status(429).json({
      message: "Too Many Requests",
      retryAfter: ttl
    })
  }

  next()
}
```


### 3.5 Background Job Queues

## Queues

A queue is a waiting line where backend tasks are stored so they can be processed in the background without making the user wait.

### Example: Signup API

After a user successfully signs up, the backend may perform these tasks:

- Send a welcome email.
- Send a notification.
- Track signup analytics.

These tasks can be added to a queue and processed in the background.

### User Signup Workflow

1. Create a Signup API.
2. Accept the following user details:
   - Name
   - Email
   - Password
3. Create a new user in the database.
4. Send a signup confirmation email to the user.
5. Return a successful signup response.

## Problem Without Queues

### Example: Signup API

```javascript
app.post("/signup", async (req, res) => {

    await sendEmail();

    res.json({
        message: "Signup Success"
    });

});
```

### Problem
- Suppose:
  - Email sending = 5 seconds
- The API also takes 5 seconds to send the response.
User Experience 😵
- Loading...
- Loading...
- Loading...

## Signup API Using Queue

```javascript
app.post("/signup", async (req, res) => {

  await emailQueue.add(
    "send-email",
    {
      email: "ankush@gmail.com"
    }
  );

  res.json({
    message: "Signup Success"
  });

});

### How Queue Works

1. Email is not sent immediately.
2. The email task is added to the queue.
3. **API Immediately Responds:**
   - `Signup Success`
4. **Then Worker Processes the Task:**
   - Sends the email in the background.

## BullMQ:

## BullMQ

BullMQ is a Redis-based queue management library that runs tasks in the background.

### Components

- **Queue:** Stores jobs.
- **Job:** Represents a task to be processed.
- **Worker:** Processes jobs in the background.

### 3.6 AI Memory

**Example:** AI chatbot conversation history.

- Store recent chat messages.
- Retrieve previous conversation context.
- Provide conversation history to an AI model.
- Maintain temporary conversation memory.

## 4. Important Redis Commands

| Command | Purpose |
|---|---|
| `SET` | Store a value |
| `GET` | Retrieve a value |
| `DEL` | Delete a key |
| `EXPIRE` | Set expiration time |
| `TTL` | Check remaining expiration time |
| `INCR` | Increment a number |
| `HSET` | Store hash fields |
| `HGET` | Retrieve hash fields |
| `LPUSH` | Add an item to a list |
| `KEYS` | Find keys (avoid in production) |

## 5. Redis vs MongoDB

| Feature | Redis | MongoDB |
|---|---|---|
| Storage | Primarily RAM | Disk-based |
| Speed | Very fast | Depends on query and workload |
| Data Type | Key-value and data structures | Document-based |
| Main Purpose | Caching and temporary data | Persistent application data |
| Expiration | Built-in key expiration | TTL indexes |
| Example | OTP, cache, sessions | Users, products, orders |




