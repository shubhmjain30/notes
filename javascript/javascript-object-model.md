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
