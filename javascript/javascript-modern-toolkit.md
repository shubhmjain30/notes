# Modern JavaScript Toolkit

This file is the practical ES6+ revision sheet: syntax, patterns, and APIs that modern JavaScript code uses every day.

## TL;DR

Modern JavaScript improves readability, safer scoping, and better composition. Interviewers expect you to be comfortable with these features, not just recognize them.

## let, const, And Scope

### What To Know

- `let` is block-scoped and reassignable.
- `const` is block-scoped and cannot be rebound.
- `var` is function-scoped and should be treated as legacy in modern code.

## Arrow Functions

### What To Know

- Shorter syntax
- Lexical `this`
- Good for callbacks
- Not suitable when you need dynamic `this`

## Destructuring

### Example

```javascript
const user = { name: "Ada", age: 28 };
const { name, age } = user;

const point = [10, 20];
const [x, y] = point;
```

## Rest And Spread

### Example

```javascript
const numbers = [1, 2, 3];
const copy = [...numbers];

function sum(...values) {
	return values.reduce((total, value) => total + value, 0);
}
```

## Template Literals

### Example

```javascript
const name = "Ada";
const message = `Hello, ${name}`;
```

## Default Parameters

```javascript
function greet(name = "Guest") {
	return `Hello, ${name}`;
}
```

## Modules

### What To Know

- `export` and `import` are standard module syntax.
- Named exports and default exports solve different needs.
- Modules improve dependency clarity.

## Array Helpers

Know when and why to use:

- `map`
- `filter`
- `reduce`
- `find`
- `some`
- `every`
- `flatMap`

## Map And Set

### Good Use Cases

- `Map` for key-value storage where keys are not limited to strings.
- `Set` for uniqueness.

## Optional Chaining And Nullish Coalescing

### Example

```javascript
const city = user?.address?.city ?? "Unknown";
```

### Common Trap

Do not confuse `??` with `||`. `??` only falls back on `null` or `undefined`.

## Symbols, Iterators, And Generators

### What To Know

- `Symbol` creates unique identifiers.
- Iterators define how values are produced one by one.
- Generators pause and resume execution with `yield`.

These appear less often in everyday frontend work, but still come up in deeper language interviews.

## Rapid-Fire Questions

### Why prefer `const`?

It prevents rebinding and makes intent clearer.

### When is an arrow function a bad fit?

When you need a dynamic `this`, such as certain object methods or constructor-like behavior.

### Why use `Map` over object?

When you need non-string keys, predictable iteration, or dedicated key-value semantics.
