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
