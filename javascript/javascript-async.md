# Asynchronous JavaScript

This file covers callbacks, promises, async/await, and the event loop. It is one of the most important JavaScript interview topics.

## TL;DR

JavaScript runs on a single call stack, but asynchronous behavior is possible because the runtime delegates work and schedules callbacks back into queues.

## Why Async Exists

JavaScript should not block the main thread while waiting for timers, network responses, or user events. Async patterns let it keep the application responsive.

## Event Loop Basics

### What To Know

- The call stack runs synchronous code.
- Web APIs or the runtime handle timers, network, and other async work.
- Completed callbacks go into queues.
- The event loop moves queued work onto the call stack when it is empty.

### Microtasks Vs Macrotasks

- Microtasks: promise callbacks, `queueMicrotask`
- Macrotasks: `setTimeout`, `setInterval`, many event callbacks

### Example

```javascript
console.log("start");

setTimeout(() => console.log("timeout"), 0);
Promise.resolve().then(() => console.log("promise"));

console.log("end");
```

Output:

```text
start
end
promise
timeout
```

### Interview Answer

Promises schedule microtasks, which run before the next macrotask such as `setTimeout`, once the current call stack is clear.

## Callbacks

### TL;DR

Callbacks are functions passed to run later after some work completes.

### Problem

They work, but deeply nested callbacks hurt readability and error handling.

### Common Trap

Callback hell is not only about nesting depth. It is also about loss of control flow clarity and scattered error handling.

## Promises

### TL;DR

Promises represent the eventual success or failure of an async operation and make composition easier than raw callbacks.

### What To Know

- States: pending, fulfilled, rejected
- `.then()` handles fulfillment
- `.catch()` handles rejection
- `.finally()` handles cleanup logic

### Example

```javascript
fetchData()
	.then((data) => transform(data))
	.then((result) => save(result))
	.catch((error) => console.error(error));
```

## async And await

### TL;DR

`async/await` gives promise-based code a more synchronous-looking control flow.

### Example

```javascript
async function loadUser() {
	try {
		const user = await fetchUser();
		return user;
	} catch (error) {
		console.error(error);
		throw error;
	}
}
```

### Common Traps

- Forgetting `await` and accidentally working with a promise instead of a value.
- Running independent async work sequentially when it could run in parallel.

### Good Interview Answer

`async/await` improves readability, but under the hood it still works on top of promises.

## Parallelism With Promise.all

### What To Know

Use `Promise.all` when multiple independent async tasks can run at the same time.

```javascript
const [user, posts] = await Promise.all([fetchUser(), fetchPosts()]);
```

## Error Handling

### What To Know

- Use `try/catch` with `async/await`.
- Rejections should be handled deliberately.
- Centralized async error handling matters in larger apps.

## Common Interview Questions

### What is the event loop?

The mechanism that checks whether the call stack is empty and then schedules queued work onto it.

### Promise vs async/await?

`async/await` is cleaner syntax built on top of promises.

### Why does promise callback run before setTimeout?

Because promise handlers go into the microtask queue, which is processed before the next macrotask.
