# Node.js Runtime Architecture

Node.js is built on a unique architecture that enables high performance and scalability for server-side applications. Understanding its core components and how they interact is essential for mastering Node.js development.

[[Node.js Express.js Roadmap|← Back to Node.js Roadmap]]

## Event-Driven Non-Blocking I/O

Node.js operates on a single-threaded event loop model, handling concurrent operations without blocking the main thread. This architecture is optimized for I/O-intensive operations.

```javascript
// Example of non-blocking I/O
const fs = require("fs");

console.log("Start reading file...");

// This operation doesn't block the execution
fs.readFile("example.txt", "utf8", (err, data) => {
	if (err) {
		console.error("Error reading file:", err);
		return;
	}
	console.log("File content:", data);
});

console.log("Continue execution while file is being read...");
```

## V8 Engine and JIT Compilation

Node.js uses Google's V8 JavaScript engine, which compiles JavaScript directly to native machine code before execution using Just-In-Time (JIT) compilation.

```javascript
// V8 optimizes frequently executed code paths
function calculateSum(n) {
	let sum = 0;
	for (let i = 0; i < n; i++) {
		sum += i;
	}
	return sum;
}

// After multiple executions, V8 will optimize this function
console.log(calculateSum(1000000));
```

## Libuv and the Event Loop

Libuv is a C library that provides the event loop, thread pool, and asynchronous I/O operations. The event loop processes events in phases:

1. **Timers**: Executes callbacks scheduled by `setTimeout()` and `setInterval()`
2. **Pending callbacks**: Executes I/O callbacks deferred to the next loop iteration
3. **Idle, prepare**: Used internally
4. **Poll**: Retrieves new I/O events
5. **Check**: Executes `setImmediate()` callbacks
6. **Close callbacks**: Executes close event callbacks

```javascript
const fs = require("fs");

// Different phases of the event loop
console.log("Start");

// Timer phase
setTimeout(() => {
	console.log("Timer callback");
}, 0);

// Check phase
setImmediate(() => {
	console.log("Immediate callback");
});

// Poll phase (I/O)
fs.readFile(__filename, () => {
	console.log("File read complete");

	// Nested timer and immediate
	setTimeout(() => {
		console.log("Nested timer");
	}, 0);

	setImmediate(() => {
		console.log("Nested immediate");
	});
});

console.log("End");
```

## C++ Bindings and Node.js Core

Node.js core functionality is implemented in C++ and exposed to JavaScript through bindings. This architecture allows JavaScript to access system-level functionality.

```javascript
// Node.js core modules are implemented in C++ but exposed as JavaScript APIs
const os = require("os");
const crypto = require("crypto");

// These calls invoke C++ functions under the hood
console.log(`Total memory: ${os.totalmem() / 1024 / 1024 / 1024} GB`);
console.log(`Free memory: ${os.freemem() / 1024 / 1024 / 1024} GB`);

// Cryptographic operations are CPU-intensive and handled by C++ code
const hash = crypto.createHash("sha256");
hash.update("Some data to hash");
console.log(hash.digest("hex"));
```

## Microtasks vs Macrotasks

The event loop processes two types of tasks with different priorities:

-   **Microtasks**: `process.nextTick()` and Promise callbacks
-   **Macrotasks**: Timer callbacks, I/O operations, setImmediate

```javascript
console.log("Script start");

// Macrotask (timer)
setTimeout(() => {
	console.log("setTimeout");
}, 0);

// Microtask (promise)
Promise.resolve().then(() => {
	console.log("Promise resolved");
});

// Microtask (highest priority)
process.nextTick(() => {
	console.log("nextTick");
});

console.log("Script end");

// Output order:
// Script start
// Script end
// nextTick
// Promise resolved
// setTimeout
```

## Node.js vs Browser Runtime

While both use JavaScript, Node.js and browser environments have significant differences:

```javascript
// In a browser:
// window is the global object
// console.log(typeof window); // 'object'
// console.log(typeof document); // 'object'
// console.log(typeof global); // 'undefined'

// In Node.js:
console.log(typeof window); // 'undefined'
console.log(typeof document); // 'undefined'
console.log(typeof global); // 'object'
console.log(typeof process); // 'object'

// Node.js has modules not available in browsers
const fs = require("fs");
const http = require("http");
const path = require("path");

// Browsers have APIs not available in Node.js
// document.getElementById('element');
// window.localStorage.setItem('key', 'value');
```

Understanding these architectural components is crucial for building efficient Node.js applications and debugging complex issues.
