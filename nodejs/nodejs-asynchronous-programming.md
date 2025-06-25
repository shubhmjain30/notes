# Asynchronous Programming in Node.js

Asynchronous programming is at the heart of Node.js, enabling non-blocking operations and efficient resource usage. Mastering these patterns is crucial for building responsive and scalable applications.

[[Node.js Express.js Roadmap|← Back to Node.js Roadmap]]

## Callbacks and Callback Hell

**Problem:** How do we handle operations that don't complete immediately without blocking the event loop?

**Theory:** Callbacks are functions passed as arguments to other functions, which are executed when an asynchronous operation completes. They're the most basic form of asynchronous programming in Node.js.

**When to use:** Best for simple, one-off asynchronous operations or when working with legacy APIs that don't support newer patterns.

```javascript
// Basic callback pattern
const fs = require("fs");

fs.readFile("example.txt", "utf8", (err, data) => {
	if (err) return console.error("Error:", err);
	console.log("File content:", data);
});
console.log("This runs before file is read");
```

**Problem:** When multiple dependent asynchronous operations are needed, callbacks lead to deeply nested code known as "Callback Hell":

```javascript
// Callback Hell - avoid this pattern for complex operations
fs.readFile("file1.txt", "utf8", (err1, data1) => {
	if (err1) return console.error("Error reading file1:", err1);

	fs.readFile("file2.txt", "utf8", (err2, data2) => {
		if (err2) return console.error("Error reading file2:", err2);

		fs.writeFile("combined.txt", data1 + data2, (err3) => {
			if (err3) return console.error("Error writing file:", err3);
			console.log("Files combined successfully!");
		});
	});
});
```

## Promises and Chaining

**Problem:** How can we make asynchronous code more readable and maintainable?

**Theory:** Promises represent operations that haven't completed yet but are expected to in the future. They exist in one of three states: pending, fulfilled, or rejected.

**When to use:** For most asynchronous operations, especially when:

-   You need to chain multiple operations
-   You want centralized error handling
-   You're working with modern APIs that return promises

**Benefits:**

-   Flattens nested callbacks
-   Provides clear error propagation
-   Enables powerful composition patterns

```javascript
// Promise-based approach
const fs = require("fs").promises;

fs.readFile("example.txt", "utf8")
	.then((data) => {
		console.log("File content:", data);
		return data.toUpperCase();
	})
	.then((uppercased) => fs.writeFile("example-uppercase.txt", uppercased))
	.then(() => console.log("File written successfully"))
	.catch((err) => console.error("Error:", err));
```

**Converting callback APIs to promises:**

```javascript
// Creating a Promise wrapper for callback-based APIs
function readFilePromise(path) {
	return new Promise((resolve, reject) => {
		fs.readFile(path, "utf8", (err, data) => {
			if (err) reject(err);
			else resolve(data);
		});
	});
}
```

## Async/Await Syntax

**Problem:** How can we write asynchronous code that looks and behaves like synchronous code?

**Theory:** Async/await is syntactic sugar built on top of Promises, making asynchronous code more readable and maintainable.

**When to use:**

-   When working with complex promise chains
-   When you need sequential asynchronous operations
-   When you want to use try/catch for error handling

**Benefits:**

-   Looks like synchronous code
-   Easier to debug (better stack traces)
-   Simpler error handling with try/catch

```javascript
// Async/await example
const fs = require("fs").promises;

async function processFiles() {
	try {
		// Sequential operations with clean syntax
		const data1 = await fs.readFile("file1.txt", "utf8");
		const data2 = await fs.readFile("file2.txt", "utf8");
		await fs.writeFile("combined.txt", data1 + data2);
		return "Operation completed";
	} catch (err) {
		console.error("Error:", err);
		throw err; // Re-throw for caller handling
	}
}

// Async functions always return promises
processFiles()
	.then((result) => console.log(result))
	.catch((err) => console.error("Caught error:", err));

console.log("This runs before processFiles completes");
```

## Parallel vs. Sequential Execution

**Problem:** How do we run multiple asynchronous operations efficiently?

**Theory:** Sequential execution waits for each operation to complete before starting the next, while parallel execution starts all operations at once.

**When to use each approach:**

-   **Sequential:** When operations depend on previous results or must happen in order
-   **Parallel:** When operations are independent and can run simultaneously

```javascript
// Sequential execution (one after another)
async function sequential() {
	const start = Date.now();

	const data1 = await fs.promises.readFile("file1.txt", "utf8");
	const data2 = await fs.promises.readFile("file2.txt", "utf8");

	console.log(`Sequential took ${Date.now() - start}ms`);
	return data1 + data2;
}

// Parallel execution (all at once)
async function parallel() {
	const start = Date.now();

	const [data1, data2] = await Promise.all([
		fs.promises.readFile("file1.txt", "utf8"),
		fs.promises.readFile("file2.txt", "utf8"),
	]);

	console.log(`Parallel took ${Date.now() - start}ms`);
	return data1 + data2;
}
```

## Error Handling in Async Code

**Problem:** How do we handle errors in different asynchronous patterns?

**Theory:** Each asynchronous pattern has its own approach to error handling. Proper error handling prevents unhandled rejections and application crashes.

**Best practices:**

-   Always handle errors in asynchronous code
-   Use centralized error handling when possible
-   Set up global handlers for uncaught exceptions

```javascript
// Error handling patterns

// 1. Callback pattern - error-first convention
fs.readFile("file.txt", (err, data) => {
	if (err) return handleError(err);
	processData(data);
});

// 2. Promise pattern - catch method
fs.promises
	.readFile("file.txt", "utf8")
	.then((data) => processData(data))
	.catch((err) => handleError(err));

// 3. Async/await pattern - try/catch blocks
async function readAndProcess() {
	try {
		const data = await fs.promises.readFile("file.txt", "utf8");
		return processData(data);
	} catch (err) {
		handleError(err);
	}
}

// 4. Global handlers for uncaught errors
process.on("unhandledRejection", (reason, promise) => {
	console.error("Unhandled Rejection:", reason);
	// Log to monitoring service or gracefully shut down
});
```

Understanding these asynchronous patterns and when to use each one is essential for writing robust, efficient Node.js applications that can handle failures gracefully while maintaining optimal performance.
