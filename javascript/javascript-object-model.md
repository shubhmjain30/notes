[[javascript-roadmap.md]]

# Prototypes and "Classes"

## Introduction

This section explains JavaScript's true object model, focusing on prototypal delegation, the prototype chain, and the class syntax as syntactic sugar. It also covers patterns for object creation and inheritance.

### Knowledge Points

#### The [[Prototype]] Chain: Behavior Delegation

JavaScript objects have an internal link to another object called their prototype:

```javascript
// Creating an object with a specific prototype
const animal = {
	makeSound() {
		console.log("Some generic sound");
	},
};

const dog = Object.create(animal);
dog.makeSound(); // "Some generic sound"

// Adding a method to the dog object
dog.makeSound = function () {
	console.log("Woof!");
};
dog.makeSound(); // "Woof!"
```

#### Delegation, Not Inheritance

JavaScript uses delegation rather than classical inheritance - objects delegate property lookups to their prototype:

```javascript
const parent = {
	value: 42,
	getValue() {
		return this.value;
	},
};

const child = Object.create(parent);

// The child delegates to the parent
console.log(child.getValue()); // 42

// But 'this' still refers to the child
child.value = 100;
console.log(child.getValue()); // 100
console.log(parent.getValue()); // 42
```

#### The OLOO Pattern: Objects-Linking-to-Other-Objects

OLOO is a clean pattern for creating objects with shared behavior:

```javascript
// Task object as a prototype
const Task = {
	init(id) {
		this.id = id;
		return this;
	},
	setDescription(desc) {
		this.description = desc;
		return this;
	},
	complete() {
		console.log(`Completing task ${this.id}`);
		return this;
	},
};

// Create a new task that delegates to Task
const task1 = Object.create(Task).init(1).setDescription("Learn OLOO");
task1.complete(); // "Completing task 1"
```

#### Deconstructing "Constructor" Functions

Constructor functions are just regular functions used with the `new` keyword:

```javascript
function Person(name) {
	this.name = name;
}

Person.prototype.greet = function () {
	return `Hello, I'm ${this.name}`;
};

const alice = new Person("Alice");
console.log(alice.greet()); // "Hello, I'm Alice"

// What happens when using 'new':
// 1. A new object is created
// 2. The object is [[Prototype]]-linked to Person.prototype
// 3. The function is called with 'this' bound to the new object
// 4. The new object is returned (unless the function returns something else)
```

#### The class Keyword: Syntactic Sugar

ES6 classes are syntactic sugar over the prototype system:

```javascript
// ES6 class syntax
class Animal {
	constructor(name) {
		this.name = name;
	}

	speak() {
		console.log(`${this.name} makes a sound`);
	}
}

class Dog extends Animal {
	constructor(name, breed) {
		super(name);
		this.breed = breed;
	}

	speak() {
		console.log(`${this.name} barks`);
	}

	fetch() {
		console.log(`${this.name} fetches the ball`);
	}
}

const rex = new Dog("Rex", "German Shepherd");
rex.speak(); // "Rex barks"
rex.fetch(); // "Rex fetches the ball"
```

#### super and Relative Polymorphism

The `super` keyword provides access to parent methods:

```javascript
class Shape {
	constructor(color) {
		this.color = color;
	}

	getDescription() {
		return `A ${this.color} shape`;
	}
}

class Circle extends Shape {
	constructor(color, radius) {
		super(color);
		this.radius = radius;
	}

	getDescription() {
		// Access the parent's method with super
		return `${super.getDescription()} with radius ${this.radius}`;
	}
}

const circle = new Circle("red", 5);
console.log(circle.getDescription()); // "A red shape with radius 5"
```

## The Prototype System

**Problem:** How does JavaScript implement object-oriented programming without traditional classes?

**Theory:** JavaScript's object system is based on prototypes, not classes. This prototype-based inheritance is fundamentally different from class-based inheritance in languages like Java or C++. Understanding this distinction is crucial for writing idiomatic JavaScript.

### The [[Prototype]] Chain

**Problem:** How do objects in JavaScript share behavior?

**Theory:** Nearly every JavaScript object has an internal link (denoted as [[Prototype]] in the specification) to another object called its prototype. When you try to access a property or method that doesn't exist on an object, JavaScript automatically looks for it on the object's prototype, then that object's prototype, and so on, forming a "prototype chain."

```javascript
// Creating an object with a specific prototype
const animal = {
	makeSound() {
		console.log("Some generic sound");
	},
};

// Object.create() creates a new object with the specified prototype
const dog = Object.create(animal);

// Property lookup: dog doesn't have makeSound(), so it delegates to animal
dog.makeSound(); // "Some generic sound"

// Adding a property directly to dog
dog.makeSound = function () {
	console.log("Woof!");
};

// Now dog has its own makeSound property
dog.makeSound(); // "Woof!"

// Check an object's prototype
console.log(Object.getPrototypeOf(dog) === animal); // true
```

**When to use the prototype chain:**

-   To share methods across many objects
-   To implement behavior delegation
-   To save memory (methods defined once in the prototype, not on each instance)

### Delegation vs. Inheritance

**Problem:** How is JavaScript's prototype system different from classical inheritance?

**Theory:** In classical inheritance, child classes copy properties and methods from parent classes. In JavaScript's prototype system, objects don't copy anything - they simply have a link to another object and delegate property lookups to it. This is called behavior delegation.

```javascript
const parent = {
	value: 42,
	getValue() {
		return this.value;
	},
};

const child = Object.create(parent);

// The child delegates to the parent for the getValue method
console.log(child.getValue()); // 42

// But 'this' still refers to the calling object (child)
child.value = 100;
console.log(child.getValue()); // 100 (not 42)
console.log(parent.getValue()); // 42 (unchanged)

// The child doesn't have a copy of getValue
console.log(child.hasOwnProperty("getValue")); // false
```

**Key differences:**

-   In delegation, the link is live - changes to the prototype affect all objects that delegate to it
-   The `this` keyword always refers to the calling object, not the prototype
-   Properties are not copied, saving memory

## Object Creation Patterns

**Problem:** What are the most effective ways to create and organize objects in JavaScript?

### The OLOO Pattern (Objects-Linking-to-Other-Objects)

**Problem:** How can we create clean, simple object relationships without constructor functions?

**Theory:** OLOO is a pattern that embraces JavaScript's prototype nature directly, using Object.create() to establish prototype links between plain objects. It's simpler and often more readable than constructor functions.

```javascript
// Task object serves as a prototype
const Task = {
	// Initialize a new task instance
	init(id, description) {
		this.id = id;
		this.description = description;
		this.completed = false;
		return this; // Enable method chaining
	},

	complete() {
		this.completed = true;
		console.log(`Completed: ${this.description}`);
		return this;
	},

	getStatus() {
		return this.completed ? "Completed" : "Pending";
	},
};

// Create specific tasks that delegate to Task
const study = Object.create(Task).init(1, "Study JavaScript prototypes");
const exercise = Object.create(Task).init(2, "Go for a run");

study.complete(); // "Completed: Study JavaScript prototypes"
console.log(exercise.getStatus()); // "Pending"
```

**When to use OLOO:**

-   When you want simple, clear object relationships
-   When you prefer delegation over inheritance
-   When you don't need the complexity of constructor functions or classes

### Constructor Functions

**Problem:** How did JavaScript implement object creation before ES6 classes?

**Theory:** Constructor functions are regular functions that are called with the `new` keyword. This creates a special execution context where `this` refers to a new object that is automatically returned.

```javascript
function Person(name, age) {
	// 'this' refers to the new object being created
	this.name = name;
	this.age = age;
}

// Methods are typically added to the prototype
Person.prototype.greet = function () {
	return `Hello, I'm ${this.name}`;
};

Person.prototype.haveBirthday = function () {
	this.age++;
	return `Happy birthday! Now ${this.age} years old.`;
};

// Create instances with 'new'
const alice = new Person("Alice", 28);
const bob = new Person("Bob", 32);

console.log(alice.greet()); // "Hello, I'm Alice"
console.log(bob.haveBirthday()); // "Happy birthday! Now 33 years old."

// What happens when using 'new':
// 1. A new empty object is created
// 2. The object's [[Prototype]] is set to Person.prototype
// 3. The function is called with 'this' bound to the new object
// 4. The new object is returned (unless the function returns something else)
```

**When to use constructor functions:**

-   In older codebases without ES6 support
-   When you need compatibility with code that expects constructor functions
-   When you prefer this syntax over ES6 classes

### The ES6 `class` Keyword

**Problem:** How can we use more familiar class-based syntax in JavaScript?

**Theory:** ES6 introduced the `class` keyword, which provides a cleaner, more familiar syntax for creating constructor functions and prototypes. It's important to understand that this is purely syntactic sugar - underneath, JavaScript still uses the same prototype-based system.

```javascript
// ES6 class syntax
class Animal {
	constructor(name) {
		this.name = name;
	}

	speak() {
		console.log(`${this.name} makes a sound`);
	}
}

// Inheritance using 'extends'
class Dog extends Animal {
	constructor(name, breed) {
		super(name); // Call the parent constructor
		this.breed = breed;
	}

	speak() {
		console.log(`${this.name} barks`);
	}

	fetch() {
		console.log(`${this.name} fetches the ball`);
	}
}

const rex = new Dog("Rex", "German Shepherd");
rex.speak(); // "Rex barks"
rex.fetch(); // "Rex fetches the ball"
```

**What happens under the hood:**

-   `class Animal` creates a constructor function named `Animal`
-   Methods like `speak()` are added to `Animal.prototype`
-   `extends` sets up the prototype chain between `Dog.prototype` and `Animal.prototype`
-   `super()` calls the parent constructor with the current `this`

**When to use ES6 classes:**

-   In modern JavaScript applications
-   When working with developers familiar with class-based languages
-   When you want cleaner syntax for inheritance
-   When using frameworks that expect class-based components (like React)

### The `super` Keyword and Relative Polymorphism

**Problem:** How can child classes access and extend parent class functionality?

**Theory:** The `super` keyword provides a way to call methods on a parent prototype, enabling cleaner relative polymorphism (where a method can extend its parent's behavior).

```javascript
class Shape {
	constructor(color) {
		this.color = color;
	}

	getArea() {
		return 0; // Base implementation
	}

	getDescription() {
		return `A ${this.color} shape with area ${this.getArea()}`;
	}
}

class Circle extends Shape {
	constructor(color, radius) {
		super(color); // Call parent constructor
		this.radius = radius;
	}

	getArea() {
		return Math.PI * this.radius * this.radius;
	}

	getDescription() {
		// Extend parent method using super
		return `${super.getDescription()} (a circle with radius ${
			this.radius
		})`;
	}
}

const circle = new Circle("blue", 5);
console.log(circle.getDescription());
// "A blue shape with area 78.54... (a circle with radius 5)"
```

**When to use super:**

-   To call a parent class constructor from a child class constructor
-   To access parent methods that have been overridden
-   To extend parent functionality rather than completely replacing it

## Choosing the Right Pattern

**Problem:** With multiple ways to create and relate objects, how do you choose the right approach?

**Theory:** Each pattern has its strengths and appropriate use cases. The best choice depends on your specific needs and the context of your application.

| Pattern                   | Pros                                              | Cons                                                                | Best For                                                                |
| ------------------------- | ------------------------------------------------- | ------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| **OLOO**                  | Simple, direct, no `new` or `this` binding issues | Less familiar to developers from class-based languages              | Small projects, when you want minimal abstraction                       |
| **Constructor Functions** | Traditional, widely understood                    | Requires careful handling of `new`, prototype property is confusing | Legacy code, compatibility with older patterns                          |
| **ES6 Classes**           | Clean syntax, familiar to most developers         | Obscures JavaScript's prototype nature                              | Modern applications, working with teams, frameworks that expect classes |

Remember that regardless of which pattern you choose, they all use JavaScript's prototype system under the hood. Understanding the fundamentals of the prototype chain will help you work effectively with any of these patterns.

JavaScript's object model is unique among mainstream programming languages. By embracing its prototypal nature rather than fighting against it, you can write more idiomatic and efficient code.
