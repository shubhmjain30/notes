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
