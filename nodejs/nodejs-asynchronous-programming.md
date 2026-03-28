# Asynchronous Programming In Node.js

This file covers the async patterns interviewers expect you to know well in Node.js.

## TL;DR

Node.js is built around asynchronous, non-blocking programming. Strong answers here require understanding both language-level patterns and runtime-level behavior.

## Callbacks

### What They Are

A callback is a function passed to run later after an operation completes.

### What To Know

- Error-first callback style is common in older Node APIs.
- Nested callbacks can hurt readability and error handling.

### Example

```javascript
fs.readFile("data.txt", "utf8", (error, data) => {
	if (error) {
		console.error(error);
		return;
	}

	console.log(data);
});
```

## Promises

### Why They Matter

Promises make async composition cleaner than raw callbacks.

### What To Know

- `then`, `catch`, `finally`
- Better chaining
- Easier centralized error handling

## async And await

### TL;DR

`async/await` is the cleanest default for promise-based Node code.

### Common Traps

- Forgetting `await`
- Running independent async work sequentially instead of in parallel
- Swallowing errors with bad `try/catch` structure

### Example

```javascript
async function loadConfig() {
	try {
		const raw = await fs.promises.readFile("config.json", "utf8");
		return JSON.parse(raw);
	} catch (error) {
		console.error(error);
		throw error;
	}
}
```

## Promise.all And Concurrency

Use `Promise.all` when tasks are independent and can run together.

```javascript
const [user, posts] = await Promise.all([getUser(), getPosts()]);
```

## Streams

### TL;DR

Streams process data incrementally instead of loading everything into memory at once.

### Why They Matter

- Better memory efficiency
- Better for large files and network data
- Core Node strength

### Stream Types

- Readable
- Writable
- Duplex
- Transform

## Event Emitters

### What To Know

Event emitters use the publish-subscribe style inside Node's ecosystem.

### Example

```javascript
const EventEmitter = require("events");
const emitter = new EventEmitter();

emitter.on("ready", () => console.log("ready"));
emitter.emit("ready");
```

## Async Error Handling

### Good Rules

- Handle errors deliberately at boundaries.
- Do not ignore rejected promises.
- Use centralized logging where appropriate.
- Be careful mixing callback and promise styles.

## Rapid-Fire Questions

### Why move from callbacks to promises?

Better composition, readability, and error handling.

### Why are streams important in Node?

They reduce memory pressure and fit Node's I/O-focused design well.

### What is an error-first callback?

A callback whose first argument is the error and second is the result.
