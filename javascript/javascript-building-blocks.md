# Values, Types, And Coercion

This file covers the JavaScript type system, coercion rules, and equality behavior. These topics show up constantly in interviews because they expose whether someone understands how the language actually behaves.

## TL;DR

Values have types, variables do not. Coercion is rule-based, not random. Equality rules matter because JavaScript performs implicit conversions in specific cases.

## JavaScript Types

### Primitive Types

- `string`
- `number`
- `boolean`
- `null`
- `undefined`
- `symbol`
- `bigint`

### Non-Primitive Type

- `object`

### Important Clarifications

- Arrays, functions, and dates are all objects.
- `typeof null` is `"object"`, which is a long-standing language bug.
- Variables can hold values of different types over time.

## Truthy And Falsy Values

### Falsy Values

- `false`
- `0`
- `-0`
- `0n`
- `""`
- `null`
- `undefined`
- `NaN`

Everything else is truthy.

### Common Trap

An empty array `[]` and empty object `{}` are both truthy.

## Coercion

### TL;DR

Coercion is automatic type conversion. It can be explicit or implicit.

### Explicit Coercion

```javascript
String(42); // "42"
Number("42"); // 42
Boolean("hello"); // true
```

### Implicit Coercion

```javascript
"5" + 1; // "51"
"5" - 1; // 4
if ("hello") {
	// truthy
}
```

### Common Traps

- Assuming `+` always means numeric addition.
- Forgetting that `-`, `*`, and `/` coerce toward numbers.
- Treating coercion as unpredictable instead of rule-based.

## Equality

### `==` Vs `===`

- `===` checks value and type without coercion.
- `==` allows coercion before comparison.

### Example

```javascript
42 === "42"; // false
42 == "42"; // true
null == undefined; // true
null === undefined; // false
```

### Interview Answer

I prefer `===` by default because it avoids implicit conversion surprises. I still understand `==` because it follows defined coercion rules and appears in real code.

## Common Interview Gotchas

### Why is `[] == false` true?

Because the array is coerced to an empty string, then to `0`, and `false` also becomes `0`.

### Why is `NaN !== NaN`?

Because `NaN` is defined as a value that is not equal to anything, including itself.

### How do you check for `NaN` correctly?

Use `Number.isNaN(value)`.

## Objects And References

### TL;DR

Objects are compared by reference, not by structural equality.

### Example

```javascript
{} === {}; // false
const a = { x: 1 };
const b = a;
a === b; // true
```

### Common Trap

Thinking two objects with the same shape and values are equal with `===`.

## Rapid-Fire Questions

### How many types are in JavaScript?

Seven primitive types and the object type.

### What are falsy values?

The small set of values that coerce to false in boolean contexts.

### Why prefer `===`?

Because it avoids implicit coercion and is easier to reason about.

### Why is `typeof null` weird?

Because it returns `"object"` due to a long-standing bug in the language.
