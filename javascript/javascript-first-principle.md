# JavaScript First Principle

This file covers the foundation that explains most JavaScript interview questions: compilation, scope, and hoisting.

## TL;DR

JavaScript is compiled just in time, scope is lexical, and hoisting is the result of declarations being processed before execution.

## JavaScript Is A Compiled Language

### What Matters

- JavaScript is not simply executed line by line with no preparation.
- The engine parses and compiles code before execution.
- This explains scope registration and hoisting behavior.

### Good Interview Answer

JavaScript is compiled just in time. The engine first parses and registers declarations, then executes the code. That is why variables and functions can appear to exist before the line where they are written.

## Lexical Scope

### TL;DR

Scope is determined by where code is written, not where it is called from.

### What To Know

- Functions and blocks create scope.
- Lookups start in the current scope and move outward.
- Inner scope can read outer scope, but outer scope cannot read inner scope.

### Example

```javascript
function outer() {
	const a = 1;

	function inner() {
		const b = 2;
		console.log(a + b);
	}

	inner();
}
```

### Common Traps

- Confusing lexical scope with dynamic scope.
- Thinking scope depends on who called the function.

## LHS Vs RHS Lookups

### TL;DR

LHS means assignment target. RHS means value retrieval.

### Example

```javascript
let a;
a = 2; // LHS lookup for a
console.log(a); // RHS lookup for a
```

### Why It Matters

This helps explain why some failures become `ReferenceError` and why assignment and reading are treated differently by the engine.

## Hoisting

### TL;DR

Hoisting is not code movement. It is the visible result of declaration processing during compilation.

### What To Know

- Function declarations are hoisted with their definitions.
- `var` declarations are hoisted and initialized to `undefined`.
- `let` and `const` are hoisted too, but stay in the temporal dead zone until initialized.

### Example

```javascript
console.log(a); // undefined
var a = 2;
```

```javascript
sayHi();
function sayHi() {
	console.log("hi");
}
```

### Common Traps

- Saying `let` and `const` are not hoisted at all.
- Confusing hoisting with initialization.

## Temporal Dead Zone

### TL;DR

A `let` or `const` binding exists from the start of scope but cannot be accessed before initialization.

### Example

```javascript
console.log(count); // ReferenceError
let count = 1;
```

## eval And with

### Why Interviewers Mention Them

They test whether you understand that JavaScript prefers static lexical analysis.

### What To Say

- `eval` and `with` make scope reasoning harder.
- They hurt optimization and readability.
- They should generally be avoided.

## Rapid-Fire Questions

### What is lexical scope?

Scope determined by where code is written.

### What is hoisting?

The observable result of declarations being processed before execution.

### Difference between `var` and `let`?

`var` is function-scoped and initialized to `undefined`; `let` is block-scoped and has a temporal dead zone.

### Why does hoisting matter?

Because it explains execution order, access rules, and many common bugs.
