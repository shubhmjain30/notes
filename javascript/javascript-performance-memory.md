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
