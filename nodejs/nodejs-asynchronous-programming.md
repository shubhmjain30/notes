# Asynchronous Programming in Node.js

Asynchronous programming is at the heart of Node.js, enabling non-blocking operations and efficient resource usage. Mastering these patterns is crucial for building responsive and scalable applications.

[[Node.js Express.js Roadmap|← Back to Node.js Roadmap]]

## Callbacks and Callback Hell

Callbacks are the most basic form of asynchronous programming in Node.js. A callback is a function passed as an argument to another function, which is then executed when an operation completes.

```javascript
// Basic callback example
const fs = require("fs");

fs.readFile("example.txt", "utf8", (err, data) => {
	if (err) {
		console.error("Error reading file:", err);
		return;
	}
	console.log("File content:", data);
});
```

However, when multiple dependent asynchronous operations are needed, callbacks can lead to deeply nested code known as "Callback Hell" or the "Pyramid of Doom":

```javascript
// Callback Hell example
const fs = require("fs");

fs.readFile("file1.txt", "utf8", (err1, data1) => {
	if (err1) {
		return console.error("Error reading file1:", err1);
	}
	console.log("Read file1");

	fs.readFile("file2.txt", "utf8", (err2, data2) => {
		if (err2) {
			return console.error("Error reading file2:", err2);
		}
		console.log("Read file2");

		fs.writeFile("combined.txt", data1 + data2, (err3) => {
			if (err3) {
				return console.error("Error writing file:", err3);
			}
			console.log("Files combined successfully!");
		});
	});
});
```

## Promises and Chaining

Promises provide a more structured approach to handling asynchronous operations. A Promise represents an operation that hasn't completed yet but is expected to in the future.

```javascript
// Promise example
const fs = require("fs").promises;

fs.readFile("example.txt", "utf8")
	.then((data) => {
		console.log("File content:", data);
		return data.toUpperCase();
	})
	.then((uppercased) => {
		return fs.writeFile("example-uppercase.txt", uppercased);
	})
	.then(() => {
		console.log("File written successfully");
	})
	.catch((err) => {
		console.error("Error:", err);
	});
```

Promises can also be created manually for wrapping callback-based APIs:

```javascript
// Creating a Promise from a callback-based function
function readFilePromise(path) {
	return new Promise((resolve, reject) => {
		fs.readFile(path, "utf8", (err, data) => {
			if (err) {
				reject(err);
			} else {
				resolve(data);
			}
		});
	});
}

// Using the Promise-based function
readFilePromise("example.txt")
	.then((data) => console.log("File content:", data))
	.catch((err) => console.error("Error:", err));
```

## Async/Await Syntax

Async/await is syntactic sugar built on top of Promises, making asynchronous code look and behave more like synchronous code.

```javascript
// Async/await example
const fs = require("fs").promises;

async function processFiles() {
	try {
		// These operations happen sequentially but don't block the event loop
		const data1 = await fs.readFile("file1.txt", "utf8");
		console.log("Read file1");

		const data2 = await fs.readFile("file2.txt", "utf8");
		console.log("Read file2");

		await fs.writeFile("combined.txt", data1 + data2);
		console.log("Files combined successfully!");

		return "Operation completed";
	} catch (err) {
		console.error("Error:", err);
		throw err; // Re-throw the error for further handling
	}
}

// Using the async function
processFiles()
	.then((result) => console.log(result))
	.catch((err) => console.error("Caught error:", err));

console.log("This runs before processFiles completes");
```

## Error Handling in Async Code

Proper error handling is crucial in asynchronous code to prevent unhandled rejections and ensure application stability.

### Callback Error Handling

```javascript
// Error-first callback pattern
fs.readFile("nonexistent.txt", "utf8", (err, data) => {
	if (err) {
		// Handle the error appropriately
		console.error("Could not read file:", err.message);
		// Maybe log to a service, return an error response, etc.
		return;
	}

	// Process data if no error occurred
	console.log(data);
});
```

### Promise Error Handling

```javascript
// Using catch for Promise error handling
fs.promises
	.readFile("nonexistent.txt", "utf8")
	.then((data) => {
		console.log(data);
	})
	.catch((err) => {
		console.error("Error caught in promise chain:", err.message);
	})
	.finally(() => {
		console.log("This runs regardless of success or failure");
	});
```

### Async/Await Error Handling

```javascript
// Try/catch with async/await
async function readFileExample() {
	try {
		const data = await fs.promises.readFile("nonexistent.txt", "utf8");
		console.log(data);
	} catch (err) {
		console.error("Error caught in try/catch:", err.message);
	} finally {
		console.log("This runs regardless of success or failure");
	}
}

// Global unhandled rejection handler
process.on("unhandledRejection", (reason, promise) => {
	console.error("Unhandled Rejection at:", promise, "reason:", reason);
	// Application specific logging, throwing an error, or other logic here
});
```

Understanding these asynchronous patterns and their error handling mechanisms is essential for writing robust Node.js applications that can handle failures gracefully.
