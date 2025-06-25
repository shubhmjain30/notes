[[javascript-roadmap.md]]

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

## The Type System Foundation

**Problem:** Why do JavaScript programs sometimes behave unpredictably when working with different types of data?

**Theory:** JavaScript is dynamically typed, meaning variables don't have fixed types - values do. This flexibility is both powerful and dangerous, as it allows for rapid development but can lead to unexpected behavior if you don't understand the underlying rules.

```javascript
// The same variable can hold different types
let flexible = 42; // number
flexible = "hello"; // string
flexible = { key: "value" }; // object
flexible = true; // boolean

// This flexibility is powerful but requires understanding
```

### The Seven Primitive Types

**Problem:** What are the basic building blocks of data in JavaScript?

**Theory:** JavaScript has seven primitive types - immutable values that are not objects and have no methods of their own. Understanding these types is essential for proper data handling.

```javascript
// The primitive types
const num = 42; // number - includes integers and floating point
const text = "hello"; // string - text data
const isTrue = true; // boolean - logical true/false
const nothing = null; // null - intentional absence of value
let notDefined; // undefined - uninitialized variable
const uniqueId = Symbol("id"); // symbol - unique identifier
const bigNumber = 9007199254740991n; // bigint - large integers
```

**When to use each primitive:**

-   **number**: For mathematical operations and representing quantities
-   **string**: For text processing and representation
-   **boolean**: For logical operations and control flow
-   **null**: To explicitly indicate "no value" by choice
-   **undefined**: Represents uninitialized state (avoid assigning this directly)
-   **symbol**: For creating truly unique property keys in objects
-   **bigint**: For integers larger than Number.MAX_SAFE_INTEGER (±9,007,199,254,740,991)

### The Object Type

**Problem:** How do we represent complex data structures in JavaScript?

**Theory:** Objects are collections of key-value pairs that serve as the foundation for more complex data structures. Unlike primitives, objects are mutable and passed by reference.

```javascript
// Basic object
const person = {
	name: "Alice",
	age: 30,
	greet() {
		return `Hello, I'm ${this.name}`;
	},
};

// Arrays - specialized objects with numeric indices
const numbers = [1, 2, 3, 4, 5];
console.log(numbers[0]); // 1
console.log(typeof numbers); // "object"

// Functions - callable objects
function sayHello(name) {
	return `Hello, ${name}`;
}
console.log(typeof sayHello); // "function" (but really an object)
```

**Key differences between primitives and objects:**

-   Primitives are immutable; objects are mutable
-   Primitives are passed by value; objects are passed by reference
-   Primitives are compared by value; objects are compared by reference

```javascript
// Primitive comparison (by value)
console.log("hello" === "hello"); // true

// Object comparison (by reference)
console.log({} === {}); // false (different objects in memory)
const obj = {};
console.log(obj === obj); // true (same reference)
```

## Type System Quirks

**Problem:** Why do some type checks in JavaScript produce unexpected results?

**Theory:** JavaScript's type system has several historical quirks and safety mechanisms that can be confusing if you don't understand their origins.

```javascript
// The infamous typeof null bug
console.log(typeof null); // "object" (should be "null")
// This is a historical bug that can't be fixed without breaking the web

// Safety mechanism for undeclared variables
console.log(typeof undeclaredVariable); // "undefined" (not an error)
// This allows for safe existence checks

// Proper way to check for null
console.log(value === null); // Direct comparison, not typeof

// Checking for undefined
console.log(typeof value === "undefined"); // Works for both undefined variables and undeclared ones
console.log(value === undefined); // Only safe if you know the variable exists
```

## Coercion: Type Conversion

**Problem:** How and why does JavaScript automatically convert between types, and how can we control this behavior?

**Theory:** Coercion is the process of converting a value from one type to another. It can happen explicitly (when you intentionally convert types) or implicitly (when JavaScript does it automatically). Understanding these rules is crucial for writing predictable code.

### Explicit Coercion

**Problem:** How can we intentionally convert values from one type to another?

**Theory:** Explicit coercion makes your intentions clear in the code and is generally preferred for readability and predictability.

```javascript
// String conversion
String(42); // "42"
(42).toString(); // "42"
42 + ""; // "42" (using implicit coercion intentionally)

// Number conversion
Number("42"); // 42
parseInt("42px", 10); // 42 (parses until non-digit)
parseFloat("42.5"); // 42.5
+"42"; // 42 (unary + operator)

// Boolean conversion
Boolean("hello"); // true
Boolean(0); // false
!!"hello"; // true (double negation idiom)
!!0; // false
```

**When to use each method:**

-   **String/Number/Boolean constructors**: When maximum clarity is needed
-   **toString/parseInt/parseFloat**: When working with specific formats
-   **Unary operators (+, !)**: For concise code when the intent is clear

### Implicit Coercion

**Problem:** When and how does JavaScript automatically convert types, and how can we work with this behavior?

**Theory:** JavaScript automatically converts types in certain operations. This can be convenient but also lead to bugs if not understood.

```javascript
// String coercion happens with the + operator when one operand is a string
"hello" + 42; // "hello42"
"hello" + true; // "hellotrue"

// Number coercion happens with most other operators
"42" - 0; // 42
"42" * 1; // 42
"42" / 2; // 21

// Boolean coercion happens in logical contexts
if ("hello") {
	// Converted to true
	console.log("Truthy value");
}

while (0) {
	// Converted to false, loop never runs
	console.log("This won't execute");
}
```

### The Falsy Value List

**Problem:** How does JavaScript determine if a value is "truthy" or "falsy" in boolean contexts?

**Theory:** JavaScript has a specific list of values that are considered "falsy" - everything else is "truthy". This is crucial for conditional logic.

| Falsy Value           | Type      | Notes                          |
| --------------------- | --------- | ------------------------------ |
| `false`               | boolean   | The boolean value false        |
| `0`, `-0`             | number    | Zero (positive or negative)    |
| `0n`                  | bigint    | BigInt zero                    |
| `""`, `''`, `` ` ` `` | string    | Empty string (any quote style) |
| `null`                | null      | Absence of any value           |
| `undefined`           | undefined | Uninitialized variable         |
| `NaN`                 | number    | Not a Number (invalid number)  |

```javascript
// Testing for truthiness
function isTruthy(value) {
	return !!value; // Convert to boolean
}

console.log(isTruthy("hello")); // true
console.log(isTruthy(42)); // true
console.log(isTruthy([])); // true (empty array is truthy!)
console.log(isTruthy({})); // true (empty object is truthy!)

console.log(isTruthy("")); // false
console.log(isTruthy(0)); // false
console.log(isTruthy(null)); // false
```

## Equality Comparisons

**Problem:** Why do equality comparisons sometimes produce unexpected results in JavaScript?

**Theory:** JavaScript has two equality operators with different behaviors. Understanding when to use each one is crucial for writing reliable code.

```javascript
// Strict equality (===) - no type conversion
console.log(42 === 42); // true
console.log(42 === "42"); // false
console.log(undefined === null); // false

// Loose equality (==) - with type conversion
console.log(42 == "42"); // true (string converted to number)
console.log(undefined == null); // true (special case)
console.log(0 == false); // true (both convert to 0)
console.log("" == false); // true (both convert to 0)
```

**When to use each:**

-   **Strict equality (===)**: Use by default for predictable behavior
-   **Loose equality (==)**: Use when you specifically want type coercion, such as:
    -   Checking for null OR undefined: `x == null`
    -   Comparing with known types where coercion is helpful

**Common gotchas:**

```javascript
// NaN is not equal to anything, including itself
console.log(NaN === NaN); // false
// Use Number.isNaN() instead
console.log(Number.isNaN(NaN)); // true

// +0 and -0 are considered equal
console.log(0 === -0); // true
// Use Object.is for strict equality including NaN and ±0
console.log(Object.is(0, -0)); // false
console.log(Object.is(NaN, NaN)); // true
```

## Grammar and Syntax

**Problem:** How do JavaScript's grammar rules affect code execution and what pitfalls should we avoid?

**Theory:** JavaScript's grammar contains nuances that can lead to unexpected behavior if not understood properly.

### Statements vs. Expressions

**Problem:** What's the difference between statements and expressions, and why does it matter?

**Theory:** An expression produces a value, while a statement performs an action. Understanding this distinction helps write clearer code and avoid syntax errors.

```javascript
// Expressions - produce values
40 + 2                  // Arithmetic expression
myFunction()            // Function call expression
a = b                   // Assignment expression

// Statements - perform actions
let a = 3;              // Variable declaration statement
if (condition) { ... }  // Conditional statement
for (let i=0; i<10; i++) { ... } // Loop statement

// Context determines interpretation
{} // Block statement on its own
const obj = {}; // Object literal expression in assignment
```

### Automatic Semicolon Insertion (ASI)

**Problem:** Why does JavaScript sometimes behave as if there are semicolons where I didn't write any?

**Theory:** JavaScript has an error-correction mechanism called Automatic Semicolon Insertion that tries to fix parsing errors by inserting semicolons. This can lead to unexpected behavior.

```javascript
// The most notorious ASI pitfall
function returnObject() {
	return; // Semicolon automatically inserted here!
	{
		// This block is now unreachable
		value: 42;
	}
}
console.log(returnObject()); // undefined, not the object

// Fixed version
function returnObjectFixed() {
	return {
		value: 42,
	};
}
```

**Best practices:**

-   Always use explicit semicolons to avoid ASI surprises
-   Keep opening braces on the same line as their statement
-   Be especially careful with `return`, `throw`, `yield`, `break`, and `continue`

Understanding JavaScript's type system and grammar rules is essential for writing robust code. These concepts form the foundation upon which all JavaScript programs are built.
