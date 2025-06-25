[[javascript-roadmap.md]]

# Asynchronous Programming

## The Concurrency Challenge

**Problem:** How can JavaScript, a single-threaded language, handle time-consuming operations without blocking the entire program?

**Theory:** JavaScript uses an event-driven, non-blocking I/O model to achieve concurrency. Rather than waiting for operations to complete, JavaScript registers callbacks that will run when operations finish, allowing the program to continue executing other code in the meantime.

### The Event Loop and Concurrency Model

**Problem:** How does JavaScript coordinate asynchronous operations when it can only execute one piece of code at a time?

**Theory:** JavaScript uses an event loop architecture with several key components:

1. **Call Stack**: Where function calls are tracked during execution
2. **Web APIs**: Browser/Node.js provided interfaces for time-consuming operations
3. **Callback Queue**: Where callbacks wait to be processed
4. **Event Loop**: Continuously checks if the call stack is empty and moves callbacks from the queue to the stack

```javascript
console.log("Start"); // 1. Added to call stack, executed, removed

setTimeout(() => {
	console.log("Timeout callback"); // 4. Eventually executed
}, 0); // 2. Timer started in Web API, callback sent to queue when done

Promise.resolve().then(() => {
	console.log("Promise resolved"); // 3. Executed before timeout
});

console.log("End"); // 2. Added to call stack, executed, removed

// Output:
// Start
// End
// Promise resolved
// Timeout callback
```

**Visual execution model:**

1. Call stack processes synchronous code (`Start` and `End`)
2. Async operations (setTimeout, Promise) are handed off to Web APIs
3. When completed, their callbacks are placed in the appropriate queue
4. Once call stack is empty, the event loop moves callbacks to the stack

### The Job Queue (Microtask Queue)

**Problem:** Why do some asynchronous operations (like Promises) execute before others (like setTimeout), even when they're ready at the same time?

**Theory:** JavaScript has multiple queues with different priorities. The microtask queue (used by Promises) has higher priority than the callback queue (used by setTimeout, DOM events, etc.). After each task, the event loop processes all microtasks before moving to the next task.

```javascript
// Macrotask (regular callback queue)
setTimeout(() => console.log("Timeout 1"), 0);

// Microtask (job queue)
Promise.resolve().then(() => {
	console.log("Promise 1");

	// New microtasks go to the back of the microtask queue
	// but still run before the next macrotask
	Promise.resolve().then(() => console.log("Promise 2"));
});

// Another macrotask
setTimeout(() => console.log("Timeout 2"), 0);

// Output:
// Promise 1
// Promise 2
// Timeout 1
// Timeout 2
```

**When to care about queue types:**

-   When coordinating the precise order of operations
-   When you need certain code to run before the next render (microtasks)
-   When debugging race conditions or timing issues

## Evolution of Asynchronous Patterns

### Callback Pattern

**Problem:** How were asynchronous operations handled before modern JavaScript features?

**Theory:** Traditionally, JavaScript used callbacks - functions passed as arguments to be executed later when an operation completes. While simple, this pattern leads to deeply nested code ("callback hell") and makes error handling difficult.

```javascript
// Callback-based asynchronous code
function getUserData(userId, callback) {
	// Simulate API call
	setTimeout(() => {
		// Get user data
		const user = { id: userId, name: "User " + userId };

		callback(null, user); // Success: error=null, data=user
	}, 1000);
}

function getUserPosts(userId, callback) {
	setTimeout(() => {
		const posts = [
			{ id: 1, title: "Post 1" },
			{ id: 2, title: "Post 2" },
		];

		callback(null, posts);
	}, 1000);
}

// Using callbacks leads to nested code
getUserData(123, (error, user) => {
	if (error) {
		console.error("Error fetching user:", error);
		return;
	}

	console.log("User:", user);

	getUserPosts(user.id, (error, posts) => {
		if (error) {
			console.error("Error fetching posts:", error);
			return;
		}

		console.log("Posts:", posts);

		// Imagine more nested callbacks here...
	});
});
```

**Problems with callbacks:**

-   Nesting leads to "callback hell" or "pyramid of doom"
-   Error handling is repetitive and easy to miss
-   No standard pattern for success/failure
-   Difficult to reason about the flow of asynchronous code

### Promises: A Better Way

**Problem:** How can we make asynchronous code more readable, composable, and easier to reason about?

**Theory:** Promises are objects representing the eventual completion (or failure) of an asynchronous operation and its resulting value. They provide a standardized way to handle async operations with chainable methods.

```javascript
// Converting callback-based functions to promises
function getUserData(userId) {
	return new Promise((resolve, reject) => {
		setTimeout(() => {
			const user = { id: userId, name: "User " + userId };
			resolve(user); // Success
			// In case of error: reject(new Error("Failed to fetch user"))
		}, 1000);
	});
}

function getUserPosts(userId) {
	return new Promise((resolve, reject) => {
		setTimeout(() => {
			const posts = [
				{ id: 1, title: "Post 1" },
				{ id: 2, title: "Post 2" },
			];
			resolve(posts);
		}, 1000);
	});
}

// Using promise chains
getUserData(123)
	.then((user) => {
		console.log("User:", user);
		return getUserPosts(user.id);
	})
	.then((posts) => {
		console.log("Posts:", posts);
	})
	.catch((error) => {
		console.error("Error:", error);
	});
```

**Key benefits of promises:**

-   Chainable with `.then()` for cleaner code flow
-   Centralized error handling with `.catch()`
-   Composable with `Promise.all()`, `Promise.race()`, etc.
-   Standardized states (pending, fulfilled, rejected)
-   Only resolve/reject once (no accidental double callbacks)

### Generators + Promises: Synchronous-Looking Async Code

**Problem:** How can we write asynchronous code that looks and feels more like synchronous code?

**Theory:** Generators are special functions that can be paused and resumed, yielding values at each pause point. When combined with promises, they allow for writing asynchronous code that looks synchronous, improving readability.

```javascript
function* fetchUserData() {
	try {
		// yield pauses execution until the promise resolves
		const user = yield getUserData(123);
		console.log("User:", user);

		const posts = yield getUserPosts(user.id);
		console.log("Posts:", posts);

		return { user, posts }; // Final value
	} catch (error) {
		console.error("Error in generator:", error);
	}
}

// Helper function to run generators with promises
function runGenerator(generator) {
	const iterator = generator();

	function iterate(result) {
		if (result.done) return Promise.resolve(result.value);

		return Promise.resolve(result.value)
			.then((data) => iterate(iterator.next(data)))
			.catch((error) => iterate(iterator.throw(error)));
	}

	return iterate(iterator.next());
}

// Running the generator
runGenerator(fetchUserData)
	.then((result) => console.log("Final result:", result))
	.catch((error) => console.error("Generator error:", error));
```

**Benefits of the generator approach:**

-   Linear code flow that's easier to read
-   Try/catch blocks work across asynchronous boundaries
-   Each step waits for the previous step to complete

### Modern Async with async/await

**Problem:** How can we get the benefits of generators and promises with cleaner syntax?

**Theory:** The async/await syntax, introduced in ES2017, is syntactic sugar over generators and promises. It provides the most readable way to write asynchronous code that looks synchronous.

```javascript
// async function declaration
async function fetchUserData() {
	try {
		// await pauses execution until the promise resolves
		const user = await getUserData(123);
		console.log("User:", user);

		const posts = await getUserPosts(user.id);
		console.log("Posts:", posts);

		return { user, posts };
	} catch (error) {
		console.error("Error:", error);
		throw error; // Re-throw for caller to handle
	}
}

// Using the async function (which returns a promise)
fetchUserData()
	.then((result) => console.log("Final result:", result))
	.catch((error) => console.error("Caught error:", error));
```

**Key features of async/await:**

-   `async` functions always return promises
-   `await` can only be used inside `async` functions
-   `await` pauses execution until the promise resolves
-   Errors in awaited promises are thrown as exceptions
-   Multiple async operations can be awaited in sequence or parallel

**When to use each pattern:**

| Pattern         | Best For                                            | Example Use Case                    |
| --------------- | --------------------------------------------------- | ----------------------------------- |
| **Callbacks**   | Simple one-off async operations, legacy APIs        | DOM event handlers                  |
| **Promises**    | Composing multiple async operations, error handling | API calls, animations               |
| **async/await** | Complex async workflows, readability                | Data fetching, processing pipelines |

**Advanced async patterns:**

```javascript
// Running promises in parallel with await
async function fetchAllData() {
	// Both requests start at the same time
	const [users, products] = await Promise.all([
		fetchUsers(),
		fetchProducts(),
	]);

	return { users, products };
}

// Error handling with async/await
async function tryOperation() {
	try {
		const result = await riskyOperation();
		return result;
	} catch (error) {
		console.error("Operation failed:", error);
		return fallbackOperation();
	} finally {
		// Always runs, even if there's an error
		await cleanup();
	}
}
```

Understanding JavaScript's asynchronous patterns is essential for building responsive, efficient applications. Each pattern has its place, but modern development typically favors async/await for its readability and error handling capabilities.
