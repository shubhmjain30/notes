# Closures, this, And Modules

This file covers three of the most common JavaScript interview themes: closures, `this` binding, and modular structure.

## Closures

### TL;DR

A closure happens when a function remembers variables from its lexical scope even after the outer function has finished executing.

### What To Know

- Closures are created naturally in JavaScript.
- They are used in callbacks, factories, modules, and event handlers.
- The function keeps access to variables, not frozen copies of values.

### Example

```javascript
function makeCounter() {
	let count = 0;
	return function () {
		count += 1;
		return count;
	};
}

const counter = makeCounter();
counter(); // 1
counter(); // 2
```

### Common Traps

- Thinking closure copies values once and never updates.
- Forgetting loop variables can produce closure bugs with `var`.

### Interview Answer

A closure is a function bundled with access to the variables in the scope where it was created. That is why callbacks and factory functions can keep using outer variables after the outer function returns.

## this Binding

### TL;DR

`this` is determined by how a function is called, not where it is written.

### The Main Rules

1. Default binding
2. Implicit binding
3. Explicit binding with `call`, `apply`, or `bind`
4. `new` binding

### Example

```javascript
const user = {
	name: "Ada",
	showName() {
		console.log(this.name);
	},
};

user.showName(); // Ada
```

### Arrow Functions

Arrow functions do not have their own `this`. They capture `this` from the surrounding lexical scope.

### Common Traps

- Thinking `this` points to the function itself.
- Losing method context when passing a method as a callback.
- Using arrow functions where dynamic `this` is actually needed.

### Interview Answer

`this` is based on call-site rules. In regular functions it depends on how the function is invoked. In arrow functions it is lexically inherited from the surrounding scope.

## Modules

### TL;DR

Modules organize code, control visibility, and avoid global namespace pollution.

### What To Know

- CommonJS uses `require` and `module.exports`.
- ES Modules use `import` and `export`.
- Modules create boundaries that help maintainability and testing.

### Example

```javascript
// math.js
export function add(a, b) {
	return a + b;
}

// app.js
import { add } from "./math.js";
```

### Module Pattern

Before standard modules, closures were often used to simulate privacy.

```javascript
function createStore() {
	let value = 0;

	return {
		get() {
			return value;
		},
		increment() {
			value += 1;
		},
	};
}
```

## Rapid-Fire Questions

### What is closure?

A function retaining access to variables from the scope where it was created.

### How is `this` determined?

By call-site rules for regular functions and lexically for arrow functions.

### Why use modules?

To organize code, reduce globals, improve reuse, and make dependencies explicit.
