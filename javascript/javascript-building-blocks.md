# Values, Types, and Grammar

## Introduction

This section covers the fundamental building blocks of JavaScript: its values, type system, coercion rules, equality, and grammar. Mastery of these concepts is essential for writing robust and predictable code.

### Knowledge Points

#### The JavaScript Type System

JavaScript is dynamically typed - variables don't have types, but values do. Understanding this distinction is crucial for writing predictable code.

```javascript
let a = 42; // 'a' holds a number
a = "hello"; // Now 'a' holds a string - perfectly valid
```

#### The Seven Primitive Types

JavaScript has seven primitive types:

```javascript
// The primitive types
const num = 42; // number
const text = "hello"; // string
const isTrue = true; // boolean
const nothing = null; // null
let notDefined; // undefined
const uniqueId = Symbol("id"); // symbol (ES6)
const bigNumber = 9007199254740991n; // bigint (ES2020)
```

#### The object Type

Objects are collections of key-value pairs and the foundation of complex data structures:

```javascript
// Object literal
const person = {
	name: "Alice",
	age: 30,
	greet() {
		return `Hello, I'm ${this.name}`;
	},
};

// Arrays are specialized objects
const numbers = [1, 2, 3, 4, 5];

// Functions are also objects
function sayHello() {
	return "Hello";
}
```

#### Type System Anomalies and Clarifications

JavaScript has some type system quirks that can be confusing:

```javascript
// The infamous typeof null bug
console.log(typeof null); // "object" (should be "null")

// undefined vs undeclared
let declared;
console.log(typeof declared); // "undefined"
console.log(typeof undeclared); // "undefined" (safety mechanism)
```

#### Coercion: The Misunderstood Power Tool

Type coercion is the automatic or explicit conversion between types:

```javascript
// Explicit coercion
const num = Number("42"); // 42
const str = String(42); // "42"
const bool = Boolean(1); // true

// Implicit coercion
const sum = "42" + 1; // "421" (string concatenation)
const diff = "42" - 1; // 41 (numeric subtraction)
```

#### Explicit Coercion

Explicit type conversion makes your intentions clear:

```javascript
// String conversion
String(42); // "42"
(42).toString(); // "42"

// Number conversion
Number("42"); // 42
+"42"; // 42 (unary + operator)
parseInt("42px", 10); // 42 (parses until non-digit)

// Boolean conversion
Boolean("hello"); // true
!!0; // false (double negation)
```

#### Implicit Coercion

JavaScript automatically converts types in certain operations:

```javascript
// String coercion
"hello" + 42; // "hello42"

// Number coercion
"42" - 0; // 42
"42" * 1; // 42

// Boolean coercion
if ("hello") {
	// Converted to true
	console.log("Truthy value");
}
```

#### Abstract Operations and Falsy Values

JavaScript has specific rules for converting values to booleans:

```javascript
// Falsy values - everything else is truthy
console.log(!!0); // false
console.log(!!""); // false
console.log(!!null); // false
console.log(!!undefined); // false
console.log(!!NaN); // false
console.log(!!false); // false
```

#### Equality, Anomalies, and Gotchas

JavaScript has two equality operators with different behaviors:

```javascript
// Strict equality (===) - no type conversion
console.log(42 === "42"); // false
console.log(null === undefined); // false

// Loose equality (==) - with type conversion
console.log(42 == "42"); // true
console.log(null == undefined); // true
console.log(0 == false); // true
console.log("" == false); // true
```

#### Grammar and Syntax

Understanding JavaScript's grammar helps avoid common pitfalls:

```javascript
// Statements vs. Expressions
let a = 3; // Statement
a = 3 + 4; // Expression within a statement

// Automatic Semicolon Insertion pitfall
function returnObject() {
	return; // Semicolon inserted here!
	{
		// This block is unreachable
		value: 42;
	}
}
console.log(returnObject()); // undefined, not the object
```
