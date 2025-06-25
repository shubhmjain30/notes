[[javascript-roadmap.md]]

# Scope, Closures, and this

## Introduction

This section explores the core structural concepts of JavaScript: scope, closures, and the dynamic nature of the this keyword. These are foundational for understanding how data and behavior are organized in JavaScript programs.

### Knowledge Points

#### The Power of Closure

Closure is when a function remembers and accesses variables from its outer scope, even after that scope has finished executing:

```javascript
function createCounter() {
	let count = 0; // This variable is "closed over"

	return function increment() {
		count++; // Access to the outer variable
		return count;
	};
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```

#### The Classic Loop Problem and Its Modern Solution

A common closure pitfall and its ES6 solution:

```javascript
// Problem: All callbacks share the same 'i' variable
for (var i = 0; i < 5; i++) {
	setTimeout(function () {
		console.log(i); // Prints "5" five times
	}, 100);
}

// Solution: Each iteration gets its own 'i' with let
for (let j = 0; j < 5; j++) {
	setTimeout(function () {
		console.log(j); // Prints 0, 1, 2, 3, 4
	}, 100);
}
```

#### The Module Pattern: Closure in Action

The module pattern uses closure to create private state:

```javascript
function createUser() {
	// Private variables
	let username, password;

	// Public API
	return {
		login(user, pw) {
			username = user;
			password = pw;
			console.log(`User ${username} logged in`);
		},
		getUsername() {
			return username;
		},
	};
}

const user = createUser();
user.login("alice", "secret");
console.log(user.getUsername()); // "alice"
console.log(user.password); // undefined (private)
```

#### Demystifying this

The `this` keyword refers to the execution context of a function, determined by how the function is called:

```javascript
function logThis() {
	console.log(this);
}

// Different call contexts change what 'this' refers to
logThis(); // Window (in browser, non-strict mode)
const obj = { method: logThis };
obj.method(); // obj
logThis.call({ custom: true }); // {custom: true}
new logThis(); // a new object
```

#### The Four Rules of this Binding

JavaScript has four rules for determining the value of `this`:

```javascript
// 1. Default binding (standalone function call)
function showName() {
	console.log(this.name);
}
var name = "Global";
showName(); // "Global" (in non-strict mode)

// 2. Implicit binding (method call)
const person = {
	name: "Alice",
	showName() {
		console.log(this.name);
	},
};
person.showName(); // "Alice"

// 3. Explicit binding (call/apply/bind)
const bob = { name: "Bob" };
showName.call(bob); // "Bob"

// 4. new binding (constructor call)
function Person(name) {
	this.name = name;
}
const carol = new Person("Carol");
console.log(carol.name); // "Carol"
```

#### this Binding Precedence

When multiple rules could apply, they follow a precedence order:

```javascript
// new binding takes precedence over implicit binding
function Person(name) {
	this.name = name;
}
const obj = {
	Person: Person,
};
const dave = new obj.Person("Dave");
console.log(dave.name); // "Dave", not undefined

// Explicit binding takes precedence over implicit binding
const eve = {
	name: "Eve",
	greet() {
		console.log(`Hello, I'm ${this.name}`);
	},
};
const frank = { name: "Frank" };
eve.greet.call(frank); // "Hello, I'm Frank"
```

#### Lexical this with Arrow Functions

Arrow functions inherit `this` from their surrounding scope:

```javascript
const team = {
	members: ["Alice", "Bob"],
	leader: "Charlie",
	showMembers() {
		// Arrow function inherits 'this' from showMembers
		this.members.forEach((member) => {
			console.log(`${member} reports to ${this.leader}`);
		});
	},
};

team.showMembers();
// "Alice reports to Charlie"
// "Bob reports to Charlie"
```

## Closure: Functions with Memory

**Problem:** How can we create functions that maintain access to data even after their parent function has completed execution?

**Theory:** Closure is one of JavaScript's most powerful features. It occurs when a function "remembers" and continues to access variables from its outer lexical scope, even after that scope has finished executing. This creates a persistent link to the variables, not just a snapshot of their values.

```javascript
function createCounter() {
	// This variable is "closed over" by the inner function
	let count = 0;

	// The returned function maintains access to count
	// even after createCounter() has finished executing
	return function increment() {
		count++; // Still has access to the outer variable
		return count;
	};
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3

// Each call to createCounter creates a new closure
// with its own independent count variable
const counter2 = createCounter();
console.log(counter2()); // 1 (not 4)
```

**When to use closures:**

-   To create private variables and encapsulation
-   For function factories that generate specialized functions
-   To maintain state in asynchronous operations
-   For partial application and currying patterns

### The Classic Loop Problem

**Problem:** Why does a loop with asynchronous callbacks often not work as expected?

**Theory:** One of the most common closure-related issues occurs with loops and asynchronous callbacks. When using `var`, all callbacks share the same variable reference rather than capturing the value at each iteration.

```javascript
// Problem: All callbacks close over the same 'i' variable
for (var i = 0; i < 5; i++) {
	setTimeout(function () {
		console.log(i); // Prints "5" five times
	}, 100);
}

// What happens:
// 1. The loop runs quickly, setting i to 0, 1, 2, 3, 4, and finally 5
// 2. The setTimeout callbacks are scheduled but don't run yet
// 3. When the callbacks execute, they all reference the same 'i', which is now 5
```

**Solutions:**

```javascript
// Solution 1: ES6 block-scoped variables with 'let'
for (let j = 0; j < 5; j++) {
	setTimeout(function () {
		console.log(j); // Prints 0, 1, 2, 3, 4
	}, 100);
}
// Each iteration gets its own 'j' variable due to block scoping

// Solution 2: Create a new scope with an IIFE (pre-ES6)
for (var i = 0; i < 5; i++) {
	(function (index) {
		// Creates a new scope with its own copy of i
		setTimeout(function () {
			console.log(index); // Prints 0, 1, 2, 3, 4
		}, 100);
	})(i);
}
```

## The Module Pattern

**Problem:** How can we create encapsulated code with private state and a public API?

**Theory:** The module pattern is one of the most powerful applications of closure in JavaScript. It provides a way to create private variables and methods while exposing only a controlled public interface.

```javascript
function createUser() {
	// Private variables - not accessible from outside
	let username, password;

	// Private function - internal use only
	function validatePassword(pw) {
		return pw.length >= 8;
	}

	// Public API - the only things accessible to the outside world
	return {
		login(user, pw) {
			if (!validatePassword(pw)) {
				throw new Error("Password too short");
			}
			username = user;
			password = pw;
			console.log(`User ${username} logged in`);
		},

		getUsername() {
			return username;
		},
	};
}

const user = createUser();
user.login("alice", "securepassword");
console.log(user.getUsername()); // "alice"
console.log(user.password); // undefined (private)
user.validatePassword; // undefined (private)
```

**When to use the module pattern:**

-   When you need to hide implementation details
-   To prevent naming conflicts in large applications
-   To create a clean, well-defined API
-   When you want to enforce data validation internally

## The Dynamic `this` Keyword

**Problem:** Why does the value of `this` seem to change unpredictably in JavaScript functions?

**Theory:** Unlike most programming languages, JavaScript's `this` keyword is not determined by where a function is declared (lexical scope), but by how the function is called (its "call site"). This dynamic binding can be powerful but also confusing if you don't understand the rules.

```javascript
function logThis() {
	console.log(this);
}

// The same function can have different 'this' values
// depending on how it's called
logThis(); // Window (in browser, non-strict mode) or undefined (strict mode)
const obj = { method: logThis };
obj.method(); // obj
logThis.call({ custom: true }); // {custom: true}
new logThis(); // a new object created for the constructor
```

### The Four Rules of `this` Binding

**Problem:** How exactly does JavaScript determine what `this` refers to in any given function call?

**Theory:** There are four rules for determining the value of `this`, applied in a specific order of precedence.

#### 1. Default Binding

When a function is called as a standalone function, `this` defaults to the global object (or `undefined` in strict mode).

```javascript
function showName() {
	console.log(this.name);
}

var name = "Global"; // Creates a property on the global object
showName(); // "Global" (in non-strict mode)

// In strict mode
("use strict");
function strictFn() {
	console.log(this); // undefined
}
strictFn();
```

**When it applies:** For regular function calls without any of the other binding rules.

#### 2. Implicit Binding

When a function is called as a method of an object, `this` refers to the object that contains the method.

```javascript
const person = {
	name: "Alice",
	showName() {
		console.log(this.name);
	},
};

person.showName(); // "Alice"

// Beware of losing the implicit binding
const standalone = person.showName;
standalone(); // undefined or "Global" (not "Alice")
```

**When it applies:** For method calls on objects.

#### 3. Explicit Binding

When a function is called using `.call()`, `.apply()`, or `.bind()`, `this` is explicitly set to the provided value.

```javascript
function introduce(greeting) {
	console.log(`${greeting}, I'm ${this.name}`);
}

const bob = { name: "Bob" };

// Different ways to explicitly bind 'this'
introduce.call(bob, "Hello"); // "Hello, I'm Bob"
introduce.apply(bob, ["Hi"]); // "Hi, I'm Bob"

// bind creates a new function with 'this' permanently set
const bobIntroduce = introduce.bind(bob);
bobIntroduce("Hey"); // "Hey, I'm Bob"
```

**When it applies:** When you need to control what `this` refers to, regardless of how the function is called.

#### 4. `new` Binding

When a function is called with the `new` keyword as a constructor, `this` refers to the newly created object.

```javascript
function Person(name) {
	// 'this' refers to the new object being created
	this.name = name;
}

const carol = new Person("Carol");
console.log(carol.name); // "Carol"
```

**When it applies:** When creating new instances with constructor functions.

### Binding Precedence

**Problem:** What happens when multiple rules could apply to the same function call?

**Theory:** The four binding rules are applied in a specific order of precedence:

1. `new` binding (highest precedence)
2. Explicit binding (call, apply, bind)
3. Implicit binding (method call)
4. Default binding (lowest precedence)

```javascript
// Explicit binding takes precedence over implicit binding
const obj = {
	name: "Object",
	method() {
		console.log(this.name);
	},
};

const otherObj = { name: "Other" };
obj.method.call(otherObj); // "Other", not "Object"

// new binding takes precedence over explicit binding
function Person(name) {
	this.name = name;
}

const boundPerson = Person.bind({ name: "Ignored" });
const person = new boundPerson("Dave");
console.log(person.name); // "Dave", not "Ignored"
```

### Arrow Functions and Lexical `this`

**Problem:** How can we maintain the correct `this` context in callbacks and nested functions?

**Theory:** ES6 arrow functions don't have their own `this` binding. Instead, they inherit `this` from their surrounding lexical scope. This solves a common problem where inner functions lose the `this` context of their outer function.

```javascript
// Traditional approach with 'this' problem
const team = {
	members: ["Alice", "Bob"],
	leader: "Charlie",
	showMembers: function () {
		// 'this' refers to team here
		this.members.forEach(function (member) {
			// 'this' is undefined or window here, not team!
			console.log(`${member} reports to ${this.leader}`); // Error or undefined
		});
	},
};

// Solution with arrow function
const betterTeam = {
	members: ["Alice", "Bob"],
	leader: "Charlie",
	showMembers: function () {
		// Arrow function inherits 'this' from showMembers
		this.members.forEach((member) => {
			console.log(`${member} reports to ${this.leader}`); // Works correctly
		});
	},
};

betterTeam.showMembers();
// "Alice reports to Charlie"
// "Bob reports to Charlie"
```

**When to use arrow functions for `this`:**

-   For callbacks inside methods
-   For event handlers that need access to the surrounding context
-   Anytime you want to preserve the lexical `this` from the surrounding scope

**When NOT to use arrow functions:**

-   For object methods (where you want `this` to be the object)
-   For constructor functions (arrow functions can't be used with `new`)
-   For methods that use `this` to refer to the object they belong to

Understanding scope, closures, and the `this` keyword is essential for writing effective JavaScript code. These concepts form the foundation for many advanced patterns and techniques in the language.
