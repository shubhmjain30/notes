# Mastering Node.js Module Systems

Modules are the building blocks of Node.js applications. Understanding how to organize, encapsulate, and share code using modules is essential for maintainable, scalable projects.

## Conceptual Overview

**Why Modules?**
Modules solve the problem of global namespace pollution and enable code reuse, separation of concerns, and dependency management.

**Mental Model:**

-   Think of modules as self-contained toolboxes. Each toolbox (module) has its own tools (functions, classes) and only exposes what is necessary to the outside world.

## Key Concepts

-   **CommonJS:** Synchronous, used by default in Node.js (`require`, `module.exports`).
-   **ES Modules:** Asynchronous, modern standard (`import`, `export`).
-   **Module Resolution:** How Node.js finds and loads modules.
-   **Encapsulation:** Each module has its own scope.

## Best Practices

-   Use modules to separate concerns (e.g., routes, controllers, services).
-   Prefer ES Modules for new projects, but understand CommonJS for legacy code.
-   Avoid circular dependencies.

## Common Pitfalls

-   Mixing CommonJS and ES Modules incorrectly.
-   Relying on global variables.
-   Not understanding module caching and its effects.

## Interview Q&A

**Q: What is the difference between CommonJS and ES Modules?**
A: CommonJS is synchronous and used by default in Node.js; ES Modules are asynchronous and follow the modern JavaScript standard. Syntax and loading mechanisms differ.

**Q: How does Node.js resolve modules?**
A: Node.js looks for built-in modules, then node_modules, then relative/absolute paths. It uses caching to avoid reloading modules.

**Q: What happens if you modify a module after it is required?**
A: Changes to exported objects are reflected in all imports due to reference sharing, but re-requiring the module does not reload it (due to caching).

---

[[nodejs-roadmap|← Back to Node.js Roadmap]]

## The Module Paradigm

**Problem:** How do we organize code into reusable, maintainable components while avoiding global namespace pollution and managing dependencies effectively?

**Theory:** Node.js supports two module systems that serve different needs and use cases. Each provides encapsulation, dependency management, and code reuse capabilities, but with different syntax and loading mechanisms.

### **Module Systems Comparison**

| Feature             | CommonJS (CJS)                 | ES Modules (ESM)    |
| ------------------- | ------------------------------ | ------------------- |
| **Syntax**          | `require()` / `module.exports` | `import` / `export` |
| **Loading**         | Synchronous                    | Asynchronous        |
| **Browser Support** | No (requires bundling)         | Native support      |
| **Dynamic Imports** | `require()` anywhere           | `import()` function |
| **Tree Shaking**    | Limited                        | Full support        |
| **Top-level await** | Not supported                  | Supported           |

## CommonJS Modules

**How it works:** CommonJS modules are loaded synchronously and executed immediately when required. Each module has its own scope, preventing global namespace pollution.

### Module Patterns

```javascript
// math.js - Module export patterns

// Pattern 1: Export individual functions
exports.add = (a, b) => a + b;
exports.subtract = (a, b) => a - b;

// Pattern 2: Replace entire exports object
module.exports = {
	add: (a, b) => a + b,
	subtract: (a, b) => a - b,
	multiply: (a, b) => a * b,
};

// Pattern 3: Export a class or constructor
module.exports = class Calculator {
	add(a, b) {
		return a + b;
	}
	subtract(a, b) {
		return a - b;
	}
};
```

```javascript
// app.js - Import patterns

// Import entire module
const math = require("./math");
console.log(math.add(5, 3)); // 8

// Destructure on import
const { add, subtract } = require("./math");
console.log(add(5, 3)); // 8

// Import class
const Calculator = require("./math");
const calc = new Calculator();
console.log(calc.add(5, 3)); // 8
```

### Module Caching

**Problem:** How does Node.js handle multiple imports of the same module?

**Theory:** Node.js caches modules after the first load. The code in a module executes only once, and the same object instance is returned for all subsequent requires.

**Why it matters:** Understanding caching is crucial for managing state in modules and avoiding unexpected behavior.

```javascript
// counter.js - Demonstrating module caching
let count = 0;

module.exports = {
	increment: () => ++count,
	getCount: () => count,
};

// In another file:
const counter1 = require("./counter");
const counter2 = require("./counter");

counter1.increment(); // 1
counter2.increment(); // 2 (not 1, because both reference the same cached module)
```

### Module Resolution Algorithm

**Problem:** How does Node.js find modules when you import them?

**Theory:** Node.js follows a specific resolution algorithm to locate modules:

1. **Core modules**: Built-in modules like `fs`, `http`
2. **File modules**: Local files with relative paths (`./` or `../`)
3. **node_modules**: Third-party packages in the node_modules directory

```javascript
// Resolution examples
const fs = require("fs"); // Core module
const myUtil = require("./utils"); // Local file (tries .js, .json, etc.)
const express = require("express"); // node_modules package
```

**Best practices:**

-   Always use relative paths for local files
-   Avoid complex relative paths (../../..) by organizing your code better
-   Consider using path aliases for cleaner imports in large projects

## ES Modules

**Problem:** How can we use the standardized JavaScript module system in Node.js?

**Theory:** ES Modules are the official standard for JavaScript modules, providing static analysis, better dependency tracking, and improved error reporting.

### Enabling ES Modules

```javascript
// Option 1: Use .mjs extension for individual files

// Option 2: Set type in package.json for the entire project
// package.json
{
  "name": "my-app",
  "type": "module"  // Makes all .js files use ES modules
}
```

### Import and Export Patterns

```javascript
// math.js - ES Module export patterns

// Named exports
export function add(a, b) { return a + b; }
export function subtract(a, b) { return a - b; }

// Default export (one per module)
export default function multiply(a, b) { return a * b; }

// Combined approach
export const PI = 3.14159;
export default class Calculator {
  // Class implementation
}
```

```javascript
// app.js - ES Module import patterns

// Named imports
import { add, subtract } from "./math.js";

// Default import
import multiply from "./math.js";

// Rename imports
import { add as sum } from "./math.js";

// Import all as namespace
import * as math from "./math.js";
console.log(math.add(1, 2), math.PI);
```

### Dynamic Imports

**Problem:** How do we load modules conditionally or on demand?

**Theory:** ES Modules support dynamic imports using the `import()` function, which returns a Promise.

**When to use:** For code-splitting, conditional loading, or when the module path is determined at runtime.

```javascript
// Dynamic import example
async function loadFeature(featureName) {
	try {
		// Path is determined at runtime
		const module = await import(`./features/${featureName}.js`);
		return module.default;
	} catch (error) {
		console.error(`Feature ${featureName} not found`);
		return null;
	}
}

// Usage
const userSelectedFeature = "dashboard";
loadFeature(userSelectedFeature).then((feature) => {
	feature.initialize();
});
```

## CommonJS vs ES Modules: Key Differences

| Feature                   | CommonJS                      | ES Modules                                    |
| ------------------------- | ----------------------------- | --------------------------------------------- |
| **Syntax**                | `require()`, `module.exports` | `import`, `export`                            |
| **Loading**               | Synchronous                   | Asynchronous (parsed before execution)        |
| **Evaluation**            | On demand                     | Before code execution                         |
| **Import behavior**       | Can import anywhere           | Imports must be at top level (except dynamic) |
| **File extensions**       | Optional                      | Required in Node.js                           |
| **JSON imports**          | Native support                | Requires import assertion (newer Node.js)     |
| **Circular dependencies** | Returns partial exports       | Better handling with live bindings            |

## Interoperability Between Module Systems

**Problem:** How do we use CommonJS and ES Modules together in the same project?

**Theory:** Node.js provides ways to use both systems together, but there are limitations and caveats.

```javascript
// In CommonJS modules (.js)
// Importing an ES module
const esmModule = await import("./esm-module.js");
esmModule.default(); // Access default export
esmModule.namedExport(); // Access named export

// In ES modules (.mjs or with type:module)
// Importing a CommonJS module
import cjsModule from "./cjs-module.js";
// The CommonJS module.exports becomes the default export
```

**Best practices:**

-   Choose one primary module system for your project
-   Use package boundaries for different module systems
-   Consider using tools like esm or dual packages for libraries

Understanding module systems is fundamental to building well-structured Node.js applications. The choice between CommonJS and ES Modules affects not only your syntax but also how your application loads, executes, and optimizes code.
