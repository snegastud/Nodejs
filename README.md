# Nodejs

**What is Node.js?**

>Node.js is a JavaScript runtime that allows us to run JavaScript on the server side. In my project, I use Node.js with SAP CAP for backend development. CAP provides the service framework, and Node.js executes our service handlers and business logic.

>For example, when a request comes from the Fiori application, it reaches our CAP service. From the service handler, we can perform validations, database operations, call external APIs, and return the response.

>One important characteristic of Node.js is its non-blocking and asynchronous programming model. So when we're waiting for an I/O operation like a database or API call, Node.js doesn't have to block the entire application waiting for that operation to finish.

**Is Node.js single-threaded or multithreaded?**

>“Node.js is single-threaded from the JavaScript execution perspective, which means our JavaScript code normally runs on one main thread.

>But being single-threaded doesn't mean it can handle only one request at a time. Node.js uses the Event Loop and its asynchronous, non-blocking model to handle multiple requests concurrently.

>For example, imagine three requests come in at the same time in my onboarding application. One request may be waiting for a database operation, another may be waiting for an external API response, and another may be processing some employee onboarding logic.

>Node.js doesn't block the main thread while waiting for those I/O operations to complete. It can continue processing other work. Once an asynchronous operation is completed, its callback or promise continuation is picked up and the JavaScript execution continues.

>So, even though JavaScript runs on a single main thread, Node.js can handle multiple I/O operations concurrently without creating a separate JavaScript thread for every request.”

**“What is the Event Loop in Node.js?”**

>“The Event Loop is the mechanism Node.js uses to handle asynchronous operations without blocking the main JavaScript thread.

>In a typical backend application, we have operations like database calls, API calls, file operations, and timers. Node.js doesn't keep the main JavaScript thread blocked while waiting for these operations to finish.

>Instead, the asynchronous operation is initiated, and Node.js can continue handling other work. Once that operation is completed, its callback or promise continuation becomes ready to run, and the Event Loop makes sure that work gets executed on the JavaScript thread.

>So basically, the Event Loop allows Node.js to handle a large number of I/O-based operations efficiently even though JavaScript execution itself happens on the main thread.”

**“What is the difference between synchronous and asynchronous execution in Node.js?”**

>“The main difference is whether the execution has to wait for an operation to finish before moving to the next statement.

>In synchronous execution, the code runs step by step, so if one operation takes time, the next operation has to wait.

>In asynchronous execution, we can start an operation such as a database call or an API call and continue with other work instead of blocking the main thread. Once the operation completes, we handle the result through a callback, Promise, or async/await.

>In Node.js applications, we generally prefer asynchronous operations for I/O work because it helps the application handle multiple requests efficiently.”

**What is call stack ?**

>“The Call Stack is basically where Node.js keeps track of the JavaScript code that is currently executing. When I call a function, it gets added to the stack, and once that function finishes, it gets removed.

>For example, if one function calls another function, Node.js executes the inner function first and then comes back to the previous function.

>This becomes important with asynchronous code. The current synchronous code has to finish first, and only after that can the asynchronous callback or Promise continuation get executed. That's how the Call Stack works together with the Event Loop in Node.js.

**“What is the difference between setTimeout() and setImmediate()?”**

>“setTimeout() and setImmediate() are both Node.js mechanisms for scheduling asynchronous callbacks, but the purpose is different.

>setTimeout() is timer-based. I use it when I need a callback to execute after a minimum delay. For example, if I want to retry an operation after two seconds, I can use setTimeout().

>setImmediate() is Node.js-specific and is used to defer a callback until the check phase of the Event Loop. There is no delay value associated with it. It's especially useful when I'm working with I/O callbacks and want to schedule some follow-up work after the current I/O processing.

>One important point is that I don't assume setTimeout(..., 0) always runs before setImmediate(). Their order depends on where they are scheduled. In an I/O callback, setImmediate() generally runs before setTimeout(..., 0).

`Microtask vs macrotask`

**“What is the difference between a microtask and a macrotask in Node.js?”**

Then you explain:

`Microtask-related:`
• Promise.then()
• Promise.catch()
• Promise.finally()
• queueMicrotask()

`Node-specific:`
• process.nextTick(). `process.nextTick() has its own queue and is processed before the regular Promise microtask queue.`

And timer/event-loop work such as:

setTimeout()
setInterval()
setImmediate()
I/O callbacks

**“What is the difference between sequential and concurrent processing in Node.js?”**

>Sequential means: I finish the first operation, then I start the second operation.

>In concurrent processing, I start independent asynchronous operations together and wait for the results when I actually need them. In Node.js, I can commonly do that using Promise.all().

`example`

“Sequential means I complete one operation and then start the next one. For example, I first get the employee details and wait for the result, then I get the laptop details. If the two operations are independent, I can run them concurrently using Promise.all(). Then both operations are started together, so I don't unnecessarily wait for one to finish before starting the other.”

`concurrent processing example program`

   this.on('getOnboardingDashboard',async(req)=>{

        const [employeeData,assetData,onboardingRequestData]=await Promise.all([
            SELECT.from(Employee),
            SELECT.from(Assets).where({availabilityStatus:"AVAILABLE"}),
            SELECT.from(OnboardingRequest).where({status:"PENDING"})
        ])

        return {
            totalEmployees: employeeData.length,
            availableAssets: assetData.length,
            pendingRequests: onboardingRequestData.length
        }
    })

