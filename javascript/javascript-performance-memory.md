[[javascript-roadmap.md]]

# Advanced Performance & Memory Management

## Introduction

This section delves into advanced performance optimization and memory management techniques, including garbage collection, code splitting, web workers, and functional programming principles.

### Knowledge Points

#### Memory Management and Garbage Collection

JavaScript manages memory automatically through garbage collection:

```javascript
// Memory lifecycle
function processData() {
	// 1. Allocate memory
	const data = {
		array: new Array(10000),
		text: "Large string".repeat(1000),
	};

	// 2. Use memory
	const result = doSomethingWith(data);

	// 3. Memory is automatically released when no longer referenced
	// When this function exits, 'data' becomes unreachable
	return result;
}

// The Mark and Sweep Algorithm
// 1. The GC builds a list of "root" objects (global variables, active function calls)
// 2. It marks all objects reachable from these roots as "in use"
// 3. It reclaims memory from all unmarked objects
```

#### Memory Leaks

Common patterns that cause memory leaks:

```javascript
// 1. Forgotten event listeners
function setupButton() {
	const button = document.getElementById("myButton");
	const data = loadLargeData();

	button.addEventListener("click", function () {
		// This closure holds a reference to 'data'
		console.log(data.length);
	});

	// If we don't remove the listener when done, 'data' can't be garbage collected
}

// Fix: Remove event listeners when no longer needed
function setupButtonFixed() {
	const button = document.getElementById("myButton");
	const data = loadLargeData();

	const handleClick = function () {
		console.log(data.length);
	};

	button.addEventListener("click", handleClick);

	// Later, when done:
	// button.removeEventListener("click", handleClick);
}

// 2. Detached DOM elements
function updateUI() {
	// Old approach - causes memory leak
	const oldElement = document.getElementById("container");
	const reference = oldElement; // Keeping a reference

	// Replace with new element
	const newElement = document.createElement("div");
	newElement.id = "container";
	oldElement.parentNode.replaceChild(newElement, oldElement);

	// 'oldElement' is removed from DOM but still referenced by 'reference'
	// so it and all its children can't be garbage collected
}

// 3. Closures holding references
function createLeak() {
	let largeData = new Array(1000000).fill("some data");

	return function leakingClosure() {
		// This inner function holds a reference to largeData
		console.log(largeData[0]);
	};
}

// The returned function keeps largeData in memory even if it's rarely used
const leak = createLeak();
```

#### Advanced Performance Optimization Techniques

Strategies for optimizing JavaScript performance:

```javascript
// Debouncing - limit function calls for events that fire rapidly
function debounce(func, wait) {
	let timeout;

	return function executedFunction(...args) {
		const later = () => {
			clearTimeout(timeout);
			func(...args);
		};

		clearTimeout(timeout);
		timeout = setTimeout(later, wait);
	};
}

// Usage
const handleSearch = debounce(function (query) {
	// Expensive operation like API call
	fetchSearchResults(query);
}, 300);

// Throttling - limit function execution to once per time period
function throttle(func, limit) {
	let inThrottle;

	return function (...args) {
		if (!inThrottle) {
			func(...args);
			inThrottle = true;
			setTimeout(() => (inThrottle = false), limit);
		}
	};
}

// Usage
const handleScroll = throttle(function () {
	// Expensive calculation on scroll
	updateScrollIndicator();
}, 100);
```

#### Code Splitting and Lazy Loading

Techniques to reduce initial load time:

```javascript
// Modern bundlers support dynamic imports
// Before: import everything upfront
import LargeComponent from "./LargeComponent";

// After: dynamic import with code splitting
const loadLargeComponent = async () => {
	const module = await import("./LargeComponent");
	return module.default;
};

// In React, using lazy and Suspense
import React, { lazy, Suspense } from "react";

const LargeComponent = lazy(() => import("./LargeComponent"));

function App() {
	return (
		<Suspense fallback={<div>Loading...</div>}>
			<LargeComponent />
		</Suspense>
	);
}

// Lazy loading images
function lazyLoadImages() {
	const images = document.querySelectorAll("img[data-src]");

	const observer = new IntersectionObserver((entries) => {
		entries.forEach((entry) => {
			if (entry.isIntersecting) {
				const img = entry.target;
				img.src = img.dataset.src;
				observer.unobserve(img);
			}
		});
	});

	images.forEach((img) => observer.observe(img));
}
```

#### Web Workers

Offloading heavy computation to background threads:

```javascript
// main.js
function startHeavyComputation() {
	const worker = new Worker("worker.js");

	worker.onmessage = function (event) {
		console.log("Result:", event.data.result);
		// Update UI with result
		document.getElementById("result").textContent = event.data.result;
	};

	worker.onerror = function (error) {
		console.error("Worker error:", error);
	};

	// Send data to worker
	worker.postMessage({
		numbers: Array.from({ length: 10000000 }, (_, i) => i),
	});
}

// worker.js
self.onmessage = function (event) {
	const numbers = event.data.numbers;

	// Perform heavy computation
	const result = numbers.reduce((sum, num) => sum + num, 0);

	// Send result back to main thread
	self.postMessage({ result });
};
```

#### Functional Programming Principles

Functional approaches for more predictable and optimizable code:

```javascript
// Pure functions - same input always produces same output, no side effects
function impureAddToCart(cart, item) {
	cart.push(item); // Modifies the input directly
	return cart;
}

function pureAddToCart(cart, item) {
	return [...cart, item]; // Returns new array, doesn't modify input
}

// Immutability - don't change data, create new copies
// Impure (mutable) approach
function updateUser(user, props) {
	Object.assign(user, props); // Modifies original object
	return user;
}

// Pure (immutable) approach
function updateUser(user, props) {
	return { ...user, ...props }; // Creates new object
}

// Function composition - building complex functions from simple ones
const add = (x) => (y) => x + y;
const multiply = (x) => (y) => x * y;
const formatPrice = (price) => `$${price.toFixed(2)}`;

// Compose functions right to left
const compose =
	(...fns) =>
	(x) =>
		fns.reduceRight((acc, fn) => fn(acc), x);

// Calculate price with tax and format it
const addTax = multiply(1.1); // 10% tax
const calculateTotalPrice = compose(formatPrice, addTax);

console.log(calculateTotalPrice(19.99)); // "$21.99"
```

## Memory Management in JavaScript

**Problem:** How does JavaScript handle memory allocation and deallocation, and how can we prevent memory leaks in our applications?

**Theory:** Unlike languages with manual memory management, JavaScript uses automatic garbage collection to reclaim memory that's no longer needed. Understanding this process is crucial for writing efficient applications, especially for long-running web apps.

### The Garbage Collection Process

**Problem:** How does JavaScript determine when memory can be freed?

**Theory:** Modern JavaScript engines use a garbage collection algorithm called "Mark and Sweep" that identifies and collects memory that's no longer reachable from the application's "roots" (global objects, currently executing functions, etc.).

```javascript
// Memory lifecycle demonstration
function processLargeData() {
	// 1. Memory allocation
	const largeArray = new Array(1000000).fill("data");
	const processedData = largeArray.map((item) => item.toUpperCase());

	// 2. Return only what's needed
	return processedData.length;

	// 3. After this function completes, largeArray and processedData
	// are no longer reachable and become eligible for garbage collection
}

// Call the function
const result = processLargeData();

// At this point, the large arrays created inside the function
// can be garbage collected since they're no longer reachable
```

**How garbage collection works:**

1. The collector starts from "roots" (global object, currently executing functions)
2. It traverses all references from these roots, marking everything it finds as "in use"
3. Any memory not marked as "in use" is considered unreachable and can be reclaimed

### Common Memory Leaks

**Problem:** Why do some JavaScript applications consume more and more memory over time?

**Theory:** Memory leaks occur when memory that's no longer needed is still being referenced, preventing the garbage collector from reclaiming it. Several common patterns lead to memory leaks in JavaScript applications.

#### 1. Forgotten Event Listeners

```javascript
// Memory leak: Event listener keeps reference to data
function setupLeakyButton() {
	const button = document.getElementById("myButton");
	const largeData = new Array(100000).fill("data"); // Large data

	button.addEventListener("click", function () {
		// This closure holds a reference to largeData
		console.log("Processing", largeData.length, "items");
	});

	// If the button is removed from the DOM but the event listener
	// isn't removed, largeData can't be garbage collected
}

// Solution: Clean up event listeners
function setupCleanButton() {
	const button = document.getElementById("myButton");
	const largeData = new Array(100000).fill("data");

	const handleClick = function () {
		console.log("Processing", largeData.length, "items");
	};

	button.addEventListener("click", handleClick);

	// Return a cleanup function
	return function cleanup() {
		button.removeEventListener("click", handleClick);
		// Now largeData can be garbage collected when no longer needed
	};
}

// Usage
const cleanup = setupCleanButton();
// Later, when the button is no longer needed:
cleanup();
```

#### 2. Detached DOM Elements

```javascript
// Memory leak: Keeping references to removed DOM elements
let detachedNodes = []; // Global array

function createAndStoreElements() {
	// Create a DOM element
	const container = document.createElement("div");
	for (let i = 0; i < 100; i++) {
		const child = document.createElement("p");
		child.textContent = "Item " + i;
		container.appendChild(child);
	}

	document.body.appendChild(container);

	// Store reference to the element
	detachedNodes.push(container);

	// Later, remove from DOM but keep the reference
	document.body.removeChild(container);

	// container is now "detached" - removed from DOM but still in memory
	// because it's referenced by detachedNodes array
}

// Solution: Remove references when no longer needed
function cleanupDetachedElements() {
	detachedNodes = []; // Allow GC to reclaim the detached elements
}
```

#### 3. Closure-Related Leaks

```javascript
// Memory leak: Long-lived closure capturing large data
function createLeakyClosure() {
	const largeData = new Array(1000000).fill("data");

	return function processSomeData(index) {
		// This function keeps largeData in memory
		// even if it only uses a tiny part of it
		return largeData[index];
	};
}

// Solution: Only keep what you need
function createEfficientClosure() {
	const largeData = new Array(1000000).fill("data");

	// Extract only what's needed for the closure
	const firstItem = largeData[0];
	const lastItem = largeData[largeData.length - 1];

	return function processImportantData() {
		// This closure only keeps two items, not the entire array
		return { first: firstItem, last: lastItem };
	};
}
```

#### 4. Circular References

```javascript
// Memory leak: Objects referencing each other
function createCircularReference() {
	let objectA = {};
	let objectB = {};

	// Create circular reference
	objectA.ref = objectB;
	objectB.ref = objectA;

	// Return references to both objects
	return { objectA, objectB };
}

// Modern garbage collectors can handle simple circular references,
// but they become problematic when combined with other patterns
// or when involving DOM elements
```

### Tools for Memory Analysis

**Problem:** How can we identify memory leaks in our applications?

**Theory:** Browser developer tools provide powerful memory profiling capabilities that help identify memory issues.

```javascript
// Common approaches to memory profiling:

// 1. Take heap snapshots and compare them
// In Chrome DevTools: Memory tab > Heap Snapshot

// 2. Record allocation timeline
// In Chrome DevTools: Memory tab > Allocation instrumentation on timeline

// 3. Use Performance monitor
// In Chrome DevTools: Performance tab > Record

// 4. Programmatic memory usage check
function checkMemoryUsage() {
	if (window.performance && window.performance.memory) {
		console.log(
			"Total JS heap size:",
			Math.round(window.performance.memory.totalJSHeapSize / 1048576),
			"MB"
		);
		console.log(
			"Used JS heap size:",
			Math.round(window.performance.memory.usedJSHeapSize / 1048576),
			"MB"
		);
	}
}
```

## Performance Optimization Techniques

**Problem:** How can we make JavaScript applications run faster and more efficiently?

**Theory:** Performance optimization involves identifying bottlenecks and applying targeted solutions to improve execution speed, responsiveness, and resource usage.

### Throttling and Debouncing

**Problem:** How can we limit the frequency of function execution for performance-intensive operations?

**Theory:** Throttling and debouncing are techniques to control how many times a function is called, especially for events that can fire rapidly like scrolling, resizing, or typing.

```javascript
// Debouncing: Execute function only after a specified delay since the last call
function debounce(func, wait) {
	let timeout;

	return function executedFunction(...args) {
		// Reset the timer each time the function is called
		clearTimeout(timeout);

		timeout = setTimeout(() => {
			func(...args);
		}, wait);
	};
}

// Usage: Only search after user stops typing for 300ms
const searchInput = document.getElementById("search");
const debouncedSearch = debounce((query) => {
	fetchSearchResults(query);
}, 300);

searchInput.addEventListener("input", (e) => debouncedSearch(e.target.value));

// Throttling: Execute function at most once per specified time period
function throttle(func, limit) {
	let inThrottle = false;

	return function executedFunction(...args) {
		if (!inThrottle) {
			func(...args);
			inThrottle = true;

			setTimeout(() => {
				inThrottle = false;
			}, limit);
		}
	};
}

// Usage: Update UI at most once every 100ms while scrolling
const throttledScroll = throttle(() => {
	updateScrollIndicator();
}, 100);

window.addEventListener("scroll", throttledScroll);
```

**When to use each:**

-   **Debounce**: When you want to execute after activity stops (search inputs, window resizing)
-   **Throttle**: When you want regular execution during continuous activity (scrolling, game loops)

### Code Splitting and Lazy Loading

**Problem:** How can we reduce initial load time and improve performance for large applications?

**Theory:** Code splitting divides your application into smaller chunks that can be loaded on demand, rather than loading everything upfront.

```javascript
// Dynamic imports for code splitting
// Before: Import everything at load time
import { heavyFeature } from "./heavyFeature";

// After: Import only when needed
async function loadFeatureWhenNeeded() {
	try {
		// This creates a separate chunk that's only loaded on demand
		const module = await import("./heavyFeature");
		module.heavyFeature();
	} catch (error) {
		console.error("Failed to load feature:", error);
	}
}

// In a React application
import React, { lazy, Suspense } from "react";

// Instead of: import HeavyComponent from './HeavyComponent';
const HeavyComponent = lazy(() => import("./HeavyComponent"));

function App() {
	return (
		<Suspense fallback={<div>Loading...</div>}>
			<HeavyComponent />
		</Suspense>
	);
}
```

**Lazy loading images:**

```javascript
// Use Intersection Observer to load images only when they're visible
function lazyLoadImages() {
	// Select all images with data-src attribute
	const images = document.querySelectorAll("img[data-src]");

	const observer = new IntersectionObserver((entries) => {
		entries.forEach((entry) => {
			// When image is visible
			if (entry.isIntersecting) {
				const img = entry.target;
				// Replace src with data-src
				img.src = img.dataset.src;
				// Remove data-src attribute
				img.removeAttribute("data-src");
				// Stop observing this image
				observer.unobserve(img);
			}
		});
	});

	// Start observing each image
	images.forEach((img) => observer.observe(img));
}
```

**When to use code splitting:**

-   For large applications with features not needed immediately
-   For routes in single-page applications
-   For resource-intensive components or libraries
-   For features used by only a subset of users

### Web Workers

**Problem:** How can we run CPU-intensive tasks without blocking the main thread?

**Theory:** Web Workers allow JavaScript to run in background threads, keeping the main thread responsive for user interactions.

```javascript
// main.js
function processDataWithoutBlocking() {
	const worker = new Worker("worker.js");

	// Send data to worker
	worker.postMessage({
		data: getLargeDataset(),
		config: { iterations: 1000 },
	});

	// Handle results from worker
	worker.onmessage = function (event) {
		const result = event.data;
		updateUI(result);
	};

	// Handle errors
	worker.onerror = function (error) {
		console.error("Worker error:", error);
	};
}

// worker.js
self.onmessage = function (event) {
	const { data, config } = event.data;

	// Perform CPU-intensive work
	const result = performHeavyCalculation(data, config);

	// Send result back to main thread
	self.postMessage(result);
};
```

**When to use Web Workers:**

-   For CPU-intensive calculations (data processing, parsing)
-   For tasks that would otherwise freeze the UI
-   For polling or background synchronization
-   For complex operations on large datasets

**Limitations:**

-   Workers don't have access to the DOM
-   Limited communication with main thread (via messages)
-   Can't directly access parent variables (must pass via messages)

### Functional Programming for Performance

**Problem:** How can we write more predictable and optimizable code?

**Theory:** Functional programming principles can lead to code that's easier to optimize, reason about, and parallelize.

```javascript
// Pure functions - same input always gives same output, no side effects
// Easier for engines to optimize
function add(a, b) {
	return a + b; // Pure function
}

// Impure function (has side effects)
let total = 0;
function addToTotal(value) {
	total += value; // Side effect: modifies external state
	return total;
}

// Immutability - don't modify data, create new copies
// Original array remains unchanged
const numbers = [1, 2, 3, 4, 5];

// Bad: Mutating the original array
function doubleInPlace(arr) {
	for (let i = 0; i < arr.length; i++) {
		arr[i] = arr[i] * 2; // Mutates original array
	}
	return arr;
}

// Good: Creating a new array
function double(arr) {
	return arr.map((x) => x * 2); // Returns new array
}

const doubled = double(numbers);
console.log(numbers); // Still [1, 2, 3, 4, 5]
console.log(doubled); // [2, 4, 6, 8, 10]

// Memoization - cache results of expensive function calls
function memoize(fn) {
	const cache = new Map();

	return function (...args) {
		const key = JSON.stringify(args);

		if (cache.has(key)) {
			return cache.get(key);
		}

		const result = fn(...args);
		cache.set(key, result);
		return result;
	};
}

// Usage
const expensiveOperation = (a, b) => {
	console.log("Computing...");
	// Simulate expensive calculation
	return a * b;
};

const memoizedOperation = memoize(expensiveOperation);

memoizedOperation(4, 2); // Logs: "Computing..." and returns 8
memoizedOperation(4, 2); // Returns 8 without computing again
```

**Benefits of functional programming for performance:**

-   Pure functions are easier for engines to optimize
-   Immutability helps prevent bugs and makes code more predictable
-   Memoization can dramatically speed up repeated calculations
-   Functional code is often more amenable to parallelization

By understanding and applying these advanced performance and memory management techniques, you can build JavaScript applications that are not only faster but also more stable and resource-efficient.
