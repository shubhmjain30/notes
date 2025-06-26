# JavaScript as a Compiled Language: The First Principle

Understanding JavaScript's true nature as a compiled language is fundamental to mastering its behavior. This foundational knowledge explains scope, hoisting, and execution patterns that might otherwise seem magical or unpredictable.

[[javascript-roadmap|← Back to JavaScript Roadmap]]

## The Compilation Paradigm Shift

**Problem:** A common misconception treats JavaScript as an interpreted language, processed line-by-line. This mental model leads to confusion about scope, hoisting, and execution order.

**Theory:** JavaScript is actually a compiled language, though compilation happens just-in-time (microseconds before execution) rather than in a separate build step. This compilation process creates an Abstract Syntax Tree (AST) and optimizes code for execution.

**Why it matters:** Understanding compilation explains seemingly mysterious behaviors like variable hoisting and why certain variables are accessible before they're declared.

### Knowledge Points

#### JavaScript as a Compiled Language

JavaScript is often misunderstood as an interpreted language, but it's actually compiled just-in-time before execution. Understanding this compilation process is key to mastering JavaScript's behavior.

```javascript
// This simple statement undergoes a two-phase process
var a = 2;
```

#### The Compiler Conversation: Deconstructing var a = 2;

When the engine encounters code, it goes through compilation and execution phases:

```javascript
// Compilation phase: Declaration is registered with scope
// Execution phase: Assignment happens
var a = 2;
```

#### LHS vs. RHS Look-ups

The engine performs two types of variable lookups:

-   Left-hand side (LHS): When a variable appears as the target of an assignment
-   Right-hand side (RHS): When a variable's value is being retrieved

```javascript
a = 2; // LHS lookup for 'a'
console.log(a); // RHS lookup for 'a'
```

#### Lexical Scope: The Author-Time Decision

Scope in JavaScript is lexical, meaning it's defined at author-time based on where variables and blocks are written in the code.

```javascript
function outer() {
	var a = 1;
	function inner() {
		var b = 2;
		console.log(a + b); // 'a' is accessible from the outer scope
	}
	inner();
	// console.log(b); // Error: 'b' is not accessible here
}
```

#### Scope Bubbles and Look-up Process

Scopes can be visualized as nested bubbles, with lookups starting from the innermost scope and working outward.

```javascript
function outer() {
	var x = "outer";
	function inner() {
		var y = "inner";
		console.log(x); // Finds 'x' in outer scope
		console.log(y); // Finds 'y' in current scope
	}
	inner();
}
```

#### Hoisting: The Result of Compilation

Hoisting is the result of JavaScript's compilation phase, where declarations are processed before code execution.

```javascript
console.log(a); // undefined (not ReferenceError)
var a = 2; // Declaration is hoisted, assignment is not
```

#### Function Declarations vs. Function Expressions

Function declarations are fully hoisted, while function expressions are not:

```javascript
// This works because of hoisting
hoisted();
function hoisted() {
	console.log("I am hoisted!");
}

// This fails with TypeError
notHoisted(); // TypeError: notHoisted is not a function
var notHoisted = function () {
	console.log("I am not hoisted!");
};
```

#### Hoisting Precedence

Function declarations take precedence over variable declarations during hoisting:

```javascript
var foo;
function foo() {
	console.log("I am a function");
}
console.log(typeof foo); // "function", not "undefined"
```

#### Cheating Lexical Scope: eval and with

JavaScript provides mechanisms to modify lexical scope at runtime, but they're discouraged:

```javascript
// eval can modify the existing lexical scope
function badPractice() {
	var a = 1;
	eval("var a = 2");
	console.log(a); // 2, not 1
}

// with creates a new lexical scope from an object
function alsoAvoid(obj) {
	with (obj) {
		a = 2; // Might create a global variable if 'a' doesn't exist on obj
	}
}
```

## The Compilation Reality

**Problem:** Why does JavaScript behave in ways that seem unpredictable if it's just interpreted line by line?

**Theory:** JavaScript is not an interpreted language as commonly believed, but a compiled one. This compilation happens just microseconds before execution (Just-In-Time compilation), which creates the illusion of interpretation while actually following compilation principles.

**Why it matters:** Understanding JavaScript's compilation process explains seemingly magical behaviors like hoisting, scope resolution, and closure formation. It provides the foundation for predictable mental models of how your code will behave.

```javascript
// This simple statement undergoes a two-phase process:
// 1. Compilation: Declaration is processed
// 2. Execution: Assignment happens
var a = 2;
```

## The Compiler Conversation

**Problem:** How does the JavaScript engine process our code, and why does this matter to developers?

**Theory:** When processing JavaScript, the engine involves several components that work together:

-   **Engine:** Orchestrates the entire process from start to finish
-   **Compiler:** Parses code into an Abstract Syntax Tree (AST) and generates executable code
-   **Scope Manager:** Maintains a registry of declared variables and enforces access rules

**The Two-Pass Process:**

```javascript
// When processing this code:
var a = 2;

// FIRST PASS (Compilation):
// Compiler: "Hey Scope Manager, have you heard of 'a' before in this scope?"
// Scope Manager: "No, I haven't."
// Compiler: "Well, I'd like to register it in the current scope."
// Scope Manager: "Done. 'a' is now registered."

// SECOND PASS (Execution):
// Engine: "Hey Scope Manager, can I assign 2 to 'a'?"
// Scope Manager: "Yes, 'a' exists in this scope. Go ahead."
// Engine: *assigns 2 to 'a'*
```

### LHS vs. RHS Look-ups

**Problem:** How does the engine handle different types of variable references?

**Theory:** The engine performs two distinct types of variable lookups:

-   **LHS (Left-Hand Side):** When a variable is the target of an assignment operation
-   **RHS (Right-Hand Side):** When a variable's value is being retrieved

**When to care:**
Understanding this distinction helps diagnose errors. An RHS failure (referencing a non-existent variable) results in a `ReferenceError`. An LHS failure in non-strict mode creates a global variable, while in strict mode it throws a `ReferenceError`.

```javascript
// LHS lookup (target of assignment)
a = 2;

// RHS lookup (source of value)
console.log(a);

// In non-strict mode, if 'a' doesn't exist:
b = 2; // Creates a global variable 'b'

// In strict mode:
("use strict");
c = 2; // ReferenceError: c is not defined
```

## Lexical Scope

**Problem:** How does JavaScript determine which variables are accessible from different parts of the code?

**Theory:** JavaScript uses lexical scope, meaning scope is defined at author-time based on where variables and blocks of scope are written in the source code. This creates a predictable, static structure that doesn't change at runtime (except with specific scope-modifying features).

**Scope Bubbles:**
Visualize scope as a series of nested bubbles:

-   Each function or block creates a new scope bubble
-   Inner bubbles can see variables from outer bubbles
-   Outer bubbles cannot see variables from inner bubbles

```javascript
function outer() {
	// outer scope bubble
	var a = 1;

	function inner() {
		// inner scope bubble
		var b = 2;

		// inner can access both 'a' and 'b'
		console.log(a + b); // 3
	}

	inner();

	// outer can only access 'a'
	console.log(a); // 1
	console.log(b); // ReferenceError: b is not defined
}
```

**The Look-up Process:**
When the engine needs to resolve a variable:

1. It starts in the current scope
2. If not found, it moves up to the next outer scope
3. This continues until it reaches global scope
4. If still not found, it results in a `ReferenceError`

## Hoisting

**Problem:** Why can some variables and functions be used before they're declared in the code?

**Theory:** Hoisting is the result of JavaScript's compilation phase. All declarations (not assignments) are processed during compilation, making them available throughout their scope. This creates the appearance that declarations are "moved" to the top of their scope.

**Function Declarations vs. Function Expressions:**

```javascript
// Function declaration - fully hoisted with its body
hoisted(); // "I am hoisted!"
function hoisted() {
	console.log("I am hoisted!");
}

// Function expression - only variable declaration is hoisted
notHoisted(); // TypeError: notHoisted is not a function
var notHoisted = function () {
	console.log("I am not hoisted!");
};

// What the engine actually sees:
function hoisted() {
	console.log("I am hoisted!");
}
var notHoisted;
hoisted();
notHoisted(); // notHoisted is undefined at this point
notHoisted = function () {
	/* ... */
};
```

**Variable Hoisting:**

```javascript
console.log(a); // undefined (not ReferenceError)
var a = 2;

// What the engine sees:
var a;
console.log(a);
a = 2;
```

**Hoisting Precedence:**
When both a variable and function share the same name, the function declaration takes precedence:

```javascript
console.log(typeof foo); // "function"

var foo = "bar";
function foo() {
	console.log("I am a function");
}

// What the engine sees:
function foo() {
	console.log("I am a function");
}
var foo; // Ignored as duplicate declaration
console.log(typeof foo);
foo = "bar"; // Reassignment happens here
```

## Scope Modification

**Problem:** Are there ways to modify lexical scope after it's been defined?

**Theory:** JavaScript provides two mechanisms that can modify lexical scope at runtime, but they're generally discouraged because they prevent optimization by the engine.

**eval():**
Evaluates a string as code in the current scope, potentially introducing new variables:

```javascript
function badIdea() {
	var a = 1;
	eval("var a = 2; console.log(a);"); // 2
	console.log(a); // 2 (the original 'a' was modified)
}

// In strict mode, eval creates its own scope:
function slightlyBetter() {
	"use strict";
	var a = 1;
	eval("var a = 2; console.log(a);"); // 2
	console.log(a); // 1 (original 'a' is untouched)
}
```

**with:**
Creates a new lexical scope derived from an object's properties:

```javascript
function alsoAvoid(obj) {
	with (obj) {
		a = 2; // Is this creating a property on obj or a global variable?
	}
}

var o1 = { a: 3 };
var o2 = { b: 3 };

alsoAvoid(o1);
console.log(o1.a); // 2 - Modified existing property

alsoAvoid(o2);
console.log(o2.a); // undefined - Property wasn't created
console.log(a); // 2 - Created a global variable instead!
```

**Why avoid them:**

-   They make code harder to understand and debug
-   They prevent the JavaScript engine from optimizing code during compilation
-   Both are restricted or behave differently in strict mode

Understanding JavaScript's compilation process and scope mechanics forms the foundation for mastering the language. These concepts explain many behaviors that might otherwise seem arbitrary or confusing.
