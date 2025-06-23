[[javascript-roadmap.md]]

# Asynchronous Programming

## Introduction

This section covers JavaScript's concurrency model, the event loop, promises, generators, and the async/await syntax. Understanding these concepts is crucial for writing non-blocking, responsive applications.

### Knowledge Points

#### The Event Loop and Concurrency Model

JavaScript is single-threaded but uses an event loop to handle asynchronous operations:

```javascript
console.log("Start");

setTimeout(() => {
	console.log("Timeout callback");
}, 0);

Promise.resolve().then(() => {
	console.log("Promise resolved");
});

console.log("End");

// Output:
// Start
// End
// Promise resolved
// Timeout callback
```

#### The Job Queue (Microtask Queue)

Promises use a special queue that has higher priority than the callback queue:

```javascript
// Macrotask (regular callback queue)
setTimeout(() => console.log("Timeout 1"), 0);

// Microtask (job queue)
Promise.resolve().then(() => {
	console.log("Promise 1");
	// Another microtask queued inside a microtask
	Promise.resolve().then(() => console.log("Promise 2"));
});

// Macrotask
setTimeout(() => console.log("Timeout 2"), 0);

// Output:
// Promise 1
// Promise 2
// Timeout 1
// Timeout 2
```

#### From Callback Hell to Promise Trust

Promises solve the "callback hell" problem with a cleaner, chainable API:

```javascript
// Callback hell
fetchData(function (data) {
	processData(
		data,
		function (processed) {
			saveData(
				processed,
				function (result) {
					displayData(
						result,
						function () {
							console.log("Done!");
						},
						handleError
					);
				},
				handleError
			);
		},
		handleError
	);
}, handleError);

// Promise chain
fetchData()
	.then(processData)
	.then(saveData)
	.then(displayData)
	.then(() => console.log("Done!"))
	.catch(handleError);
```

#### The Apex of Asynchrony: Generators + Promises

Generators allow for pausing and resuming function execution, which can be combined with promises:

```javascript
function* fetchUserData() {
	try {
		const userResponse = yield fetch("/api/user");
		const user = yield userResponse.json();

		const postsResponse = yield fetch(`/api/posts?userId=${user.id}`);
		const posts = yield postsResponse.json();

		return { user, posts };
	} catch (error) {
		console.error("Error:", error);
	}
}

// Runner function to handle the generator
function run(generator) {
	const iterator = generator();

	function handle(yielded) {
		if (yielded.done) return yielded.value;

		return Promise.resolve(yielded.value)
			.then((data) => handle(iterator.next(data)))
			.catch((error) => handle(iterator.throw(error)));
	}

	return handle(iterator.next());
}

run(fetchUserData).then((data) => {
	console.log("User:", data.user);
	console.log("Posts:", data.posts);
});
```

#### Modern Async with async/await

ES2017 introduced async/await as syntactic sugar over generators and promises:

```javascript
async function fetchUserData() {
	try {
		const userResponse = await fetch("/api/user");
		const user = await userResponse.json();

		const postsResponse = await fetch(`/api/posts?userId=${user.id}`);
		const posts = await postsResponse.json();

		return { user, posts };
	} catch (error) {
		console.error("Error:", error);
		throw error; // Re-throw to propagate to caller
	}
}

// Using the async function
fetchUserData()
	.then((data) => {
		console.log("User:", data.user);
		console.log("Posts:", data.posts);
	})
	.catch((error) => {
		console.error("Failed to fetch data:", error);
	});
```
