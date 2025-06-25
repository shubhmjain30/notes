[[javascript-roadmap.md]]

# ES6+ and Performance

## Introduction

This section introduces the modern JavaScript toolkit, including new syntax, data structures, modules, and performance best practices. It is essential for writing efficient, maintainable, and modern JavaScript code.

### Knowledge Points

#### Modern Declarations and Syntax

ES6 introduced significant improvements to JavaScript's syntax:

```javascript
// Old way
var name = "Alice";
var greeting = "Hello, " + name + "!";

// Modern way
const name = "Alice";
const greeting = `Hello, ${name}!`;
```

#### let and const

Block-scoped declarations provide better variable control:

```javascript
// var has function scope
function oldWay() {
	var x = 1;
	if (true) {
		var x = 2; // Same variable!
		console.log(x); // 2
	}
	console.log(x); // 2
}

// let and const have block scope
function newWay() {
	let x = 1;
	if (true) {
		let x = 2; // Different variable
		console.log(x); // 2
	}
	console.log(x); // 1

	// const prevents reassignment
	const y = 3;
	// y = 4;  // TypeError: Assignment to constant variable

	// But objects and arrays can be modified
	const obj = { value: 5 };
	obj.value = 6; // This works
}
```

#### Arrow Functions (=>)

Arrow functions provide concise syntax and lexical this binding:

```javascript
// Traditional function expression
const double = function (x) {
	return x * 2;
};

// Arrow function
const triple = (x) => x * 3;

// Lexical this binding
const counter = {
	count: 0,

	// Traditional method loses 'this' in callback
	incrementBad: function () {
		setTimeout(function () {
			this.count++; // 'this' is window/global
			console.log(this.count); // NaN
		}, 1000);
	},

	// Arrow function preserves 'this'
	incrementGood: function () {
		setTimeout(() => {
			this.count++; // 'this' is counter object
			console.log(this.count); // 1
		}, 1000);
	},
};
```

#### Default Parameters, Rest/Spread (...), Destructuring, Template Literals

Modern JavaScript provides powerful syntax for working with data:

```javascript
// Default parameters
function greet(name = "Guest") {
	return `Hello, ${name}!`;
}

// Rest parameters
function sum(...numbers) {
	return numbers.reduce((total, n) => total + n, 0);
}

// Spread operator
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5]; // [1, 2, 3, 4, 5]

// Object spread
const defaults = { theme: "dark", fontSize: 12 };
const userPrefs = { fontSize: 14 };
const settings = { ...defaults, ...userPrefs }; // { theme: "dark", fontSize: 14 }

// Array destructuring
const [first, second, ...rest] = [1, 2, 3, 4, 5];

// Object destructuring
const { name, age, job = "Developer" } = { name: "Alice", age: 30 };

// Template literals
const message = `
  Hello, ${name}!
  You are ${age} years old.
  Your job is ${job}.
`;
```

#### Modules

ES6 modules provide a standardized way to organize code:

```javascript
// math.js
export const PI = 3.14159;
export function square(x) {
	return x * x;
}
export default function add(a, b) {
	return a + b;
}

// main.js
import add, { PI, square } from "./math.js";
console.log(add(2, 3)); // 5
console.log(square(4)); // 16
console.log(PI); // 3.14159

// Import everything as a namespace
import * as math from "./math.js";
console.log(math.default(5, 5)); // 10
```

#### Iterators and Generators

Iterators provide a standard way to traverse data structures:

```javascript
// Custom iterator
const range = {
	from: 1,
	to: 5,

	// Makes the object iterable
	[Symbol.iterator]() {
		return {
			current: this.from,
			last: this.to,

			next() {
				if (this.current <= this.last) {
					return { value: this.current++, done: false };
				} else {
					return { done: true };
				}
			},
		};
	},
};

// Iterate over the range
for (const num of range) {
	console.log(num); // 1, 2, 3, 4, 5
}

// Generator function - simpler way to create iterators
function* generateSequence(start, end) {
	for (let i = start; i <= end; i++) {
		yield i;
	}
}

for (const num of generateSequence(1, 5)) {
	console.log(num); // 1, 2, 3, 4, 5
}
```

#### Advanced Collections: Map, Set, WeakMap, WeakSet

Modern JavaScript provides specialized collections for different use cases:

```javascript
// Map - key-value pairs where keys can be any type
const userRoles = new Map();
const user1 = { name: "Alice" };
const user2 = { name: "Bob" };

userRoles.set(user1, "admin");
userRoles.set(user2, "editor");

console.log(userRoles.get(user1)); // "admin"

// Set - collection of unique values
const uniqueVisitors = new Set();
uniqueVisitors.add("user1");
uniqueVisitors.add("user2");
uniqueVisitors.add("user1"); // Duplicate, ignored

console.log(uniqueVisitors.size); // 2

// WeakMap - keys are objects, doesn't prevent garbage collection
const userData = new WeakMap();
let user = { name: "Charlie" };
userData.set(user, { visits: 10 });

// If all other references to 'user' are removed,
// both 'user' and its data in the WeakMap can be garbage collected
user = null;
```

#### Meta Programming: Symbol, Proxy, Reflect

ES6 introduced powerful meta-programming features:

```javascript
// Symbol - unique property keys
const id = Symbol("id");
const user = {
	name: "Dave",
	[id]: 123,
};

console.log(user[id]); // 123
console.log(Object.keys(user)); // ["name"] - symbols are not enumerable

// Proxy - intercept and customize operations
const handler = {
	get(target, prop) {
		if (prop in target) {
			return target[prop];
		} else {
			return `Property "${prop}" doesn't exist`;
		}
	},
};

const proxy = new Proxy({ name: "Eve" }, handler);
console.log(proxy.name); // "Eve"
console.log(proxy.age); // "Property "age" doesn't exist"

// Reflect - standardized methods for object operations
const obj = { x: 1, y: 2 };
console.log(Reflect.has(obj, "x")); // true
Reflect.set(obj, "z", 3);
console.log(obj.z); // 3
```

#### New API Additions

ES6+ added many useful methods to built-in objects:

```javascript
// Array methods
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((n) => n * 2);
const even = numbers.filter((n) => n % 2 === 0);
const sum = numbers.reduce((total, n) => total + n, 0);

// Object methods
const source = { a: 1, b: 2 };
const target = { c: 3 };
const merged = Object.assign(target, source); // { a: 1, b: 2, c: 3 }

// String methods
const text = "Hello, world!";
console.log(text.includes("world")); // true
console.log(text.startsWith("Hello")); // true
console.log(text.endsWith("!")); // true
console.log(text.repeat(2)); // "Hello, world!Hello, world!"
```

#### Writing Performant JavaScript

Best practices for writing efficient JavaScript:

```javascript
// Avoid excessive DOM operations
function badPerformance() {
	const list = document.getElementById("list");

	for (let i = 0; i < 1000; i++) {
		// Causes layout thrashing - forces browser to recalculate layout each time
		list.appendChild(document.createElement("li"));
	}
}

function goodPerformance() {
	const fragment = document.createDocumentFragment();

	for (let i = 0; i < 1000; i++) {
		fragment.appendChild(document.createElement("li"));
	}

	// Single DOM operation
	document.getElementById("list").appendChild(fragment);
}

// Use requestAnimationFrame for animations
function animate() {
	// Update animation state
	element.style.left = parseFloat(element.style.left) + 1 + "px";

	// Schedule next frame
	requestAnimationFrame(animate);
}

// Offload heavy computation to Web Workers
const worker = new Worker("heavy-calculation.js");
worker.onmessage = function (event) {
	console.log("Result:", event.data);
};
worker.postMessage({ data: complexData });
```

## Modern JavaScript Features

**Problem:** How can we write more expressive, maintainable, and error-resistant JavaScript code?

**Theory:** ES6 (ECMAScript 2015) and subsequent versions introduced significant improvements to JavaScript's syntax and functionality. These modern features solve many common problems in earlier versions of the language and provide more powerful tools for developers.

### Variable Declarations: let and const

**Problem:** How can we avoid issues with variable hoisting and create more predictable scoping?

**Theory:** Unlike `var`, which is function-scoped and hoisted, `let` and `const` are block-scoped declarations that provide better control over variables. `const` adds the additional constraint that the binding cannot be reassigned.

```javascript
// Problem with var
function varProblem() {
	var x = 1;
	if (true) {
		var x = 2; // Same variable as outer x
		console.log(x); // 2
	}
	console.log(x); // 2 - the value was overwritten
}

// Solution with let
function letSolution() {
	let x = 1;
	if (true) {
		let x = 2; // Different variable due to block scope
		console.log(x); // 2
	}
	console.log(x); // 1 - outer x is preserved
}

// Using const for values that shouldn't change
function constExample() {
	const API_KEY = "abc123"; // Convention: uppercase for true constants
	// API_KEY = "xyz"; // Error: Assignment to constant variable

	// Note: Objects and arrays can still be modified
	const user = { name: "Alice" };
	user.name = "Bob"; // This works - the binding is constant, not the value
}
```

**When to use each:**

-   Use `const` by default for all variables that won't be reassigned
-   Use `let` when you need to reassign a variable
-   Avoid `var` in modern code unless you specifically need its hoisting behavior

### Arrow Functions

**Problem:** How can we write more concise functions and avoid issues with the `this` keyword?

**Theory:** Arrow functions provide a shorter syntax for function expressions and don't have their own `this` binding. Instead, they inherit `this` from the surrounding lexical context.

```javascript
// Traditional function expression
const double = function (x) {
	return x * 2;
};

// Arrow function - concise syntax
const triple = (x) => x * 3;

// Multi-parameter arrow function
const add = (a, b) => a + b;

// Multi-line arrow function needs braces and return
const calculate = (a, b) => {
	const result = a * b;
	return result + 5;
};
```

**The `this` problem and solution:**

```javascript
// Problem: 'this' context lost in callback
const counter = {
	count: 0,
	start: function () {
		setInterval(function () {
			this.count++; // 'this' refers to global object, not counter
			console.log(this.count); // NaN
		}, 1000);
	},
};

// Solution 1: Traditional approach with saved reference
const counterFixed1 = {
	count: 0,
	start: function () {
		const self = this;
		setInterval(function () {
			self.count++; // Use saved reference
			console.log(self.count);
		}, 1000);
	},
};

// Solution 2: Arrow function preserves lexical this
const counterFixed2 = {
	count: 0,
	start: function () {
		setInterval(() => {
			this.count++; // 'this' refers to counterFixed2
			console.log(this.count);
		}, 1000);
	},
};
```

**When to use arrow functions:**

-   For short, simple functions, especially callbacks
-   When you need to preserve the surrounding `this` context
-   For functional programming patterns like map/filter/reduce

**When NOT to use arrow functions:**

-   For object methods (use shorthand method syntax instead)
-   For constructor functions (they can't be used with `new`)
-   When you need to access `arguments` object (use rest parameters instead)
-   When you need dynamic `this` binding

### Parameter Handling

**Problem:** How can we make function parameters more flexible and handle variable numbers of arguments?

**Theory:** Modern JavaScript provides several features for better parameter handling: default parameters, rest parameters, and the spread operator.

#### Default Parameters

```javascript
// Problem: Old way of handling default values
function greetOld(name) {
	name = name || "Guest"; // Falsy values like "" also become "Guest"
	return "Hello, " + name;
}

// Solution: Default parameters
function greet(name = "Guest") {
	return `Hello, ${name}`;
}

console.log(greet()); // "Hello, Guest"
console.log(greet("Alice")); // "Hello, Alice"

// Default parameters can reference previous parameters
function createUser(name, role = "user", id = generateId(name)) {
	return { name, role, id };
}
```

#### Rest Parameters

**Problem:** How can we handle an arbitrary number of function arguments?

**Theory:** Rest parameters collect all remaining arguments into a real array, providing a cleaner alternative to the `arguments` object.

```javascript
// Old way: arguments object (not a real array)
function sumOld() {
	let total = 0;
	for (let i = 0; i < arguments.length; i++) {
		total += arguments[i];
	}
	return total;
}

// Modern way: rest parameters
function sum(...numbers) {
	// numbers is a real array, so we can use array methods
	return numbers.reduce((total, n) => total + n, 0);
}

console.log(sum(1, 2, 3, 4)); // 10

// Rest parameters must be the last parameter
function logDetails(title, ...items) {
	console.log(title);
	items.forEach((item) => console.log(`- ${item}`));
}
```

#### Spread Operator

**Problem:** How can we expand elements from arrays or objects into other arrays, objects, or function calls?

**Theory:** The spread operator (`...`) is the opposite of rest parameters - it expands an array or object into individual elements.

```javascript
// Spread in function calls
const numbers = [1, 2, 3];
console.log(Math.max(...numbers)); // 3

// Spread in arrays
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5]; // [1, 2, 3, 4, 5]
const combined = [...arr1, ...arr2]; // [1, 2, 3, 1, 2, 3, 4, 5]

// Clone an array
const original = [1, 2, 3];
const copy = [...original];

// Spread in objects (ES2018)
const defaults = { theme: "dark", fontSize: 12 };
const userPrefs = { fontSize: 14, showSidebar: true };
const settings = { ...defaults, ...userPrefs }; // Later properties override earlier ones
// Result: { theme: "dark", fontSize: 14, showSidebar: true }
```

**When to use spread:**

-   To pass array elements as separate arguments
-   To combine arrays or objects
-   To create shallow copies of arrays or objects
-   As a cleaner alternative to methods like `concat` or `Object.assign`

### Destructuring

**Problem:** How can we extract values from arrays or objects more elegantly?

**Theory:** Destructuring allows us to unpack values from arrays or properties from objects into distinct variables with concise syntax.

#### Array Destructuring

```javascript
// Basic array destructuring
const rgb = [255, 100, 50];
const [red, green, blue] = rgb;
console.log(red, green, blue); // 255 100 50

// Skip elements
const [first, , third] = [1, 2, 3];
console.log(first, third); // 1 3

// Rest pattern in destructuring
const [head, ...tail] = [1, 2, 3, 4];
console.log(head, tail); // 1 [2, 3, 4]

// Default values
const [name = "Guest", age = 0] = ["Alice"];
console.log(name, age); // "Alice" 0

// Swap variables without a temporary variable
let a = 1,
	b = 2;
[a, b] = [b, a];
console.log(a, b); // 2 1
```

#### Object Destructuring

```javascript
// Basic object destructuring
const user = { name: "Alice", age: 30, role: "admin" };
const { name, age } = user;
console.log(name, age); // "Alice" 30

// Assign to different variable names
const { name: userName, age: userAge } = user;
console.log(userName, userAge); // "Alice" 30

// Default values
const { role, level = "beginner" } = user;
console.log(role, level); // "admin" "beginner"

// Rest pattern with objects
const { name: n, ...rest } = user;
console.log(n, rest); // "Alice" { age: 30, role: "admin" }

// Nested destructuring
const metadata = {
	title: "JavaScript",
	translations: {
		es: "JavaScript",
		fr: "JavaScript",
	},
	year: 2023,
};

const {
	title,
	translations: { es, fr },
} = metadata;
console.log(title, es, fr); // "JavaScript" "JavaScript" "JavaScript"
```

**When to use destructuring:**

-   Function parameters for options objects
-   Extracting specific values from API responses
-   Returning multiple values from functions
-   Importing specific exports from modules

### Template Literals

**Problem:** How can we create strings with embedded expressions and preserve formatting?

**Theory:** Template literals (using backticks) allow for string interpolation and multi-line strings without concatenation or escape characters.

```javascript
// String concatenation problems
const name = "Alice";
const age = 30;
const oldWay =
	"My name is " +
	name +
	" and I am " +
	age +
	" years old.\n" +
	"That's " +
	age * 365 +
	" days of experience.";

// Template literal solution
const newWay = `My name is ${name} and I am ${age} years old.
That's ${age * 365} days of experience.`;

// Expressions in template literals can be any valid JavaScript
const isAdult = `${name} is ${age >= 18 ? "an adult" : "a minor"}.`;

// Tagged templates - process template literals with a function
function highlight(strings, ...values) {
	return strings.reduce((result, str, i) => {
		const value = values[i] || "";
		return `${result}${str}<span class="highlight">${value}</span>`;
	}, "");
}

const highlighted = highlight`My name is ${name} and I am ${age} years old.`;
// "My name is <span class="highlight">Alice</span> and I am <span class="highlight">30</span> years old."
```

**When to use template literals:**

-   Any time you need to combine strings and expressions
-   When creating multi-line strings
-   For HTML templates or SQL queries in code
-   With tagged templates for advanced string processing

## Modules and Code Organization

**Problem:** How can we organize JavaScript code into reusable, maintainable modules?

**Theory:** ES6 introduced a standardized module system that allows for better code organization, dependency management, and encapsulation.

```javascript
// math.js - exporting functionality
// Named exports
export const PI = 3.14159;
export function square(x) {
	return x * x;
}

// Default export (only one per module)
export default function add(a, b) {
	return a + b;
}

// main.js - importing functionality
// Import default and named exports
import add, { PI, square } from "./math.js";
console.log(add(2, 3)); // 5
console.log(square(4)); // 16
console.log(PI); // 3.14159

// Import everything as a namespace
import * as math from "./math.js";
console.log(math.default(5, 5)); // 10 (default export)
console.log(math.square(3)); // 9

// Dynamic imports (ES2020)
async function loadModule() {
	const math = await import("./math.js");
	return math.square(4);
}
```

**Key benefits of ES modules:**

-   File-based scoping (each module has its own scope)
-   Explicit imports and exports
-   Static analysis and tree-shaking
-   Asynchronous loading with dynamic imports
-   Singleton pattern (modules are only executed once)

**When to use modules:**

-   For any non-trivial application
-   To share code between different parts of an application
-   To create reusable libraries
-   When working with modern build tools like webpack or Rollup

## Advanced Data Structures

**Problem:** How can we handle more complex data relationships beyond simple objects and arrays?

**Theory:** ES6 introduced several new collection types that solve specific problems not easily addressed with objects and arrays.

### Map

**Problem:** How can we create key-value collections where keys can be any type, not just strings?

**Theory:** The `Map` object holds key-value pairs where keys can be of any type, including objects and functions.

```javascript
// Creating a Map
const userRoles = new Map();

// Adding entries
const user1 = { name: "Alice" };
const user2 = { name: "Bob" };
userRoles.set(user1, "admin");
userRoles.set(user2, "editor");

// Checking size
console.log(userRoles.size); // 2

// Getting values
console.log(userRoles.get(user1)); // "admin"

// Checking for keys
console.log(userRoles.has(user1)); // true

// Deleting entries
userRoles.delete(user2);

// Iterating over a Map
for (const [user, role] of userRoles) {
	console.log(`${user.name}: ${role}`);
}

// Creating from an array of pairs
const settings = new Map([
	["theme", "dark"],
	["fontSize", 14],
]);
```

**When to use Map over Object:**

-   When keys are not strings or symbols
-   When you need to preserve insertion order
-   When you frequently add and remove properties
-   When you need to easily get the size of the collection

### Set

**Problem:** How can we create collections of unique values?

**Theory:** The `Set` object stores unique values of any type, making it easy to eliminate duplicates.

```javascript
// Creating a Set
const uniqueVisitors = new Set();

// Adding values
uniqueVisitors.add("user1");
uniqueVisitors.add("user2");
uniqueVisitors.add("user1"); // Duplicate, ignored

// Checking size
console.log(uniqueVisitors.size); // 2

// Checking for values
console.log(uniqueVisitors.has("user1")); // true

// Deleting values
uniqueVisitors.delete("user2");

// Creating from an array (removes duplicates)
const numbers = new Set([1, 2, 3, 2, 1]);
console.log([...numbers]); // [1, 2, 3]

// Common use case: Remove duplicates from array
const array = [1, 2, 3, 2, 1];
const uniqueArray = [...new Set(array)]; // [1, 2, 3]
```

**When to use Set:**

-   To store unique values
-   To quickly check if a value exists
-   To remove duplicates from arrays
-   For mathematical set operations (union, intersection, difference)

### WeakMap and WeakSet

**Problem:** How can we associate data with objects without preventing garbage collection?

**Theory:** `WeakMap` and `WeakSet` are "weak" versions of Map and Set where references to key objects are held weakly, allowing them to be garbage collected if no other references exist.

```javascript
// WeakMap example
const userData = new WeakMap();

// Store data associated with a DOM element
const button = document.getElementById("submit");
userData.set(button, { clicks: 0 });

// Update data when clicked
button.addEventListener("click", () => {
	const data = userData.get(button);
	data.clicks++;
	console.log(`Button clicked ${data.clicks} times`);
});

// If button is removed from DOM and no other references exist,
// both button and its data can be garbage collected

// WeakSet example - track objects without preventing GC
const trackedObjects = new WeakSet();
let object = { data: "important" };

trackedObjects.add(object);
console.log(trackedObjects.has(object)); // true

// Later, if all references to object are removed
object = null;
// The WeakSet reference doesn't prevent garbage collection
```

**When to use WeakMap/WeakSet:**

-   To associate metadata with DOM elements
-   To implement caches that don't cause memory leaks
-   When you need to track object instances without preventing GC
-   For private data in classes (pre-private fields)

## Meta Programming

**Problem:** How can we customize the fundamental behavior of JavaScript objects?

**Theory:** ES6 introduced several features for meta-programming - code that manipulates the behavior of other code.

### Symbol

**Problem:** How can we create truly unique property keys that won't collide with other properties?

**Theory:** Symbols are unique, immutable primitive values that can be used as object property keys to avoid name collisions.

```javascript
// Creating symbols
const id = Symbol("id");
const userId = Symbol("id"); // Different from id

console.log(id === userId); // false

// Using symbols as property keys
const user = {
	name: "Alice",
	[id]: 123, // Symbol as property key
};

console.log(user[id]); // 123

// Symbols are not enumerable in for...in or Object.keys
for (const key in user) {
	console.log(key); // Only "name"
}

// Well-known symbols customize object behavior
const collection = {
	items: ["a", "b", "c"],
	// Make object iterable
	[Symbol.iterator]: function* () {
		for (let item of this.items) {
			yield item;
		}
	},
};

for (const item of collection) {
	console.log(item); // "a", "b", "c"
}
```

**When to use Symbols:**

-   For truly private or special properties
-   To extend objects without risking name collisions
-   To define well-known behaviors like iteration
-   For creating unique identifiers

### Proxy and Reflect

**Problem:** How can we intercept and customize fundamental operations on JavaScript objects?

**Theory:** Proxies allow you to create custom behavior for basic operations like property access, assignment, and function invocation. The Reflect API provides methods that correspond to the operations that can be intercepted.

```javascript
// Basic proxy example
const handler = {
	get(target, prop) {
		if (prop in target) {
			return target[prop];
		} else {
			return `Property "${prop}" doesn't exist`;
		}
	},
};

const user = { name: "Alice" };
const userProxy = new Proxy(user, handler);

console.log(userProxy.name); // "Alice"
console.log(userProxy.age); // "Property "age" doesn't exist"

// Validation example
const validator = {
	set(target, prop, value) {
		if (prop === "age") {
			if (typeof value !== "number" || value < 0) {
				throw new TypeError("Age must be a positive number");
			}
		}
		// Use Reflect to perform the default behavior
		return Reflect.set(target, prop, value);
	},
};

const person = new Proxy({}, validator);
person.age = 30; // Works
// person.age = -5; // TypeError: Age must be a positive number
```

**When to use Proxy/Reflect:**

-   For data validation
-   To create "smart" objects with computed properties
-   For logging or debugging object access
-   To implement framework features like reactivity (Vue.js uses this)

## Performance Optimization

**Problem:** How can we write JavaScript that performs well, especially in demanding applications?

**Theory:** Performance optimization in JavaScript involves understanding how the engine works and applying patterns that avoid common bottlenecks.

### DOM Performance

**Problem:** How can we minimize the performance impact of DOM operations?

**Theory:** DOM operations are often the slowest part of web applications. Minimizing DOM access and batching changes can dramatically improve performance.

```javascript
// Poor performance: Causes layout thrashing
function addItemsBad(items) {
	const list = document.getElementById("list");

	for (let i = 0; i < items.length; i++) {
		list.appendChild(document.createElement("li")).textContent = items[i];
		// Each append forces layout recalculation
		console.log(list.offsetHeight); // Forces layout
	}
}

// Better performance: Batch DOM operations
function addItemsGood(items) {
	const fragment = document.createDocumentFragment();

	for (let i = 0; i < items.length; i++) {
		const li = document.createElement("li");
		li.textContent = items[i];
		fragment.appendChild(li);
	}

	// Single DOM operation
	document.getElementById("list").appendChild(fragment);
}
```

### Animation Performance

**Problem:** How can we create smooth animations without blocking the main thread?

**Theory:** `requestAnimationFrame` synchronizes animations with the browser's rendering cycle, providing smoother animations with better performance.

```javascript
// Poor animation technique
function animateBad() {
	const element = document.getElementById("box");
	let position = 0;

	setInterval(() => {
		position += 2;
		element.style.left = position + "px";
	}, 16); // Approximately 60fps
}

// Better animation with requestAnimationFrame
function animateGood() {
	const element = document.getElementById("box");
	let position = 0;

	function step() {
		position += 2;
		element.style.left = position + "px";

		if (position < 300) {
			requestAnimationFrame(step);
		}
	}

	requestAnimationFrame(step);
}
```

### Offloading Heavy Computation

**Problem:** How can we perform CPU-intensive tasks without freezing the UI?

**Theory:** Web Workers allow JavaScript to run in background threads, keeping the main thread responsive for user interactions.

```javascript
// In main.js
function processDataOnMainThread(data) {
	// Heavy computation that blocks the UI
	const result = heavyComputation(data);
	updateUI(result);
}

// Better approach: Use Web Worker
function processDataWithWorker(data) {
	const worker = new Worker("worker.js");

	worker.onmessage = function (event) {
		// Result received from worker
		updateUI(event.data);
	};

	// Send data to worker
	worker.postMessage(data);
}

// In worker.js
self.onmessage = function (event) {
	const data = event.data;
	const result = heavyComputation(data);
	self.postMessage(result);
};
```

**Key performance optimization techniques:**

-   Minimize DOM operations and batch changes
-   Use appropriate data structures for your use case
-   Avoid blocking the main thread with heavy computation
-   Leverage modern browser APIs like requestAnimationFrame and Web Workers
-   Use profiling tools to identify actual bottlenecks, not theoretical ones

Modern JavaScript provides powerful features that make code more readable, maintainable, and efficient. By understanding these tools and when to use them, you can write cleaner code that performs better and has fewer bugs.
