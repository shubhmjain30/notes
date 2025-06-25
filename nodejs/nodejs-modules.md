# Node.js Modules

Modules are the building blocks of Node.js applications, allowing code to be organized into reusable, self-contained units. Understanding the different module systems and how to use them effectively is essential for building maintainable applications.

[[Node.js Express.js Roadmap|← Back to Node.js Roadmap]]

## CommonJS Modules

CommonJS is the original module system built into Node.js. It uses `require()` for importing and `module.exports` or `exports` for exporting.

### Basic Module Export and Import

```javascript
// math.js - Exporting a module
function add(a, b) {
	return a + b;
}

function subtract(a, b) {
	return a - b;
}

// Method 1: Exporting individual functions
exports.add = add;
exports.subtract = subtract;

// Method 2: Replacing the entire exports object
// module.exports = {
//   add,
//   subtract
// };
```

```javascript
// app.js - Importing a module
const math = require("./math");
// Or destructure specific functions
// const { add, subtract } = require('./math');

console.log(math.add(5, 3)); // 8
console.log(math.subtract(5, 3)); // 2
```

### Module Caching

Node.js caches modules after the first time they are loaded, which means the code in a module only executes once, even if it's required multiple times.

```javascript
// counter.js
let count = 0;

module.exports = {
	increment: function () {
		return ++count;
	},
	getCount: function () {
		return count;
	},
};
```

```javascript
// app.js
const counter1 = require("./counter");
const counter2 = require("./counter");

console.log(counter1.increment()); // 1
console.log(counter2.increment()); // 2 (not 1, because the module is cached)
console.log(counter1.getCount()); // 2
console.log(counter2.getCount()); // 2
```

### Module Resolution Algorithm

Node.js follows a specific algorithm to resolve module paths:

```javascript
// Importing a core module
const fs = require("fs");

// Importing a local module (with explicit extension)
const myModule = require("./my-module.js");

// Importing a local module (without extension)
const utils = require("./utils"); // Node.js will look for utils.js, utils.json, etc.

// Importing from node_modules
const express = require("express");
// Node.js will search in ./node_modules, ../node_modules, ../../node_modules, etc.
```

## ES Modules

ES Modules (ESM) is the official standardized module system for JavaScript. Node.js has added support for ES modules alongside CommonJS.

### Using ES Modules in Node.js

To use ES Modules in Node.js, you can either:

1. Use the `.mjs` file extension
2. Set `"type": "module"` in your package.json

```json
// package.json
{
	"name": "my-es-module-app",
	"version": "1.0.0",
	"type": "module"
}
```

### Importing and Exporting with ES Modules

```javascript
// math.mjs or math.js (with "type": "module" in package.json)

// Named exports
export function add(a, b) {
	return a + b;
}

export function subtract(a, b) {
	return a - b;
}

// Default export
export default function multiply(a, b) {
	return a * b;
}
```

```javascript
// app.mjs or app.js (with "type": "module" in package.json)

// Importing named exports
import { add, subtract } from "./math.js";

// Importing default export
import multiply from "./math.js";

// Importing both named and default exports
// import multiply, { add, subtract } from './math.js';

// Renaming imports
// import { add as addition } from './math.js';

console.log(add(5, 3)); // 8
console.log(subtract(5, 3)); // 2
console.log(multiply(5, 3)); // 15
```

### Dynamic Imports

ES Modules support dynamic imports, allowing modules to be loaded conditionally:

```javascript
// app.mjs
async function loadModule() {
	if (process.env.NODE_ENV === "development") {
		const { default: devModule } = await import("./dev-module.js");
		return devModule;
	} else {
		const { default: prodModule } = await import("./prod-module.js");
		return prodModule;
	}
}

loadModule().then((module) => {
	module.doSomething();
});
```

## CommonJS vs ES Modules

Here's a comparison of the key differences between the two module systems:

| Feature         | CommonJS                       | ES Modules                             |
| --------------- | ------------------------------ | -------------------------------------- |
| Syntax          | `require()`, `module.exports`  | `import`, `export`                     |
| Loading         | Synchronous                    | Asynchronous (parsed before execution) |
| Evaluation      | On demand, when required       | Before code execution                  |
| Caching         | Cached after first load        | Same, but with different cache         |
| Named imports   | `const { x } = require('./y')` | `import { x } from './y'`              |
| Dynamic imports | `require(variable)`            | `import(variable)` (returns Promise)   |
| File extensions | Optional                       | Required (in Node.js)                  |

## Creating and Using a Package

A practical example of creating and publishing a Node.js package:

```javascript
// lib/index.js
// A simple utility package

// Helper function
function formatDate(date) {
	return date.toISOString().split("T")[0];
}

// Main functionality
function dateCalculator(startDate, days) {
	const result = new Date(startDate);
	result.setDate(result.getDate() + days);
	return formatDate(result);
}

module.exports = {
	dateCalculator,
	formatDate,
};
```

```json
// package.json
{
	"name": "date-calculator",
	"version": "1.0.0",
	"description": "A simple date calculation utility",
	"main": "lib/index.js",
	"scripts": {
		"test": "jest"
	},
	"keywords": ["date", "calculator", "utility"],
	"author": "Your Name",
	"license": "MIT"
}
```

Understanding both module systems is essential as you'll encounter both in the Node.js ecosystem. CommonJS is still prevalent in many existing packages, while ES Modules represent the future standard for JavaScript modules.
