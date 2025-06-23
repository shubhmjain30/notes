[[javascript-roadmap.md]]

# JavaScript as a Compiled Language

## Introduction

This section explains why JavaScript is fundamentally a compiled language, how its engine works, and the mental models required to understand its core mechanics. It covers compilation, scope, hoisting, and the nuances of how code is processed.

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
