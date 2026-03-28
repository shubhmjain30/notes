# Prototypes, Inheritance, And Classes

This file covers how JavaScript objects relate to each other and how inheritance works under the hood.

## TL;DR

JavaScript uses prototype-based inheritance. Classes are syntax on top of prototypes, not a separate inheritance system.

## Objects And Property Access

### What To Know

- Objects hold properties and methods.
- Property lookup checks the object first, then walks the prototype chain.
- Methods are just function-valued properties.

## Prototype Chain

### TL;DR

If a property is not found on the current object, JavaScript looks up the chain through the object's prototype.

### Example

```javascript
const animal = {
	speak() {
		return "sound";
	},
};

const dog = Object.create(animal);
dog.bark = function () {
	return "woof";
};

dog.speak(); // inherited from animal
```

### Common Traps

- Thinking methods are copied into every instance automatically.
- Confusing prototype inheritance with class-based inheritance from other languages.

## Constructor Functions

### What To Know

- Before classes, constructor functions plus prototypes were the common pattern.
- `new` creates an object, links it to the function's prototype, and binds `this`.

### Example

```javascript
function User(name) {
	this.name = name;
}

User.prototype.sayHi = function () {
	return `Hi, ${this.name}`;
};
```

## ES6 Classes

### TL;DR

Classes provide cleaner syntax, but the runtime model is still prototype-based.

### Example

```javascript
class User {
	constructor(name) {
		this.name = name;
	}

	sayHi() {
		return `Hi, ${this.name}`;
	}
}
```

### Interview Answer

JavaScript classes are syntactic sugar over prototype-based inheritance. They improve readability, but the underlying lookup model is still the prototype chain.

## Object.create And OLOO

### What To Know

- `Object.create` creates an object linked directly to another object.
- OLOO means objects linked to other objects.
- It is a direct prototype-based style that avoids pretending JavaScript is class-based.

## Rapid-Fire Questions

### What is the prototype chain?

The chain JavaScript walks when a property is missing on the current object.

### Are classes true classes in the Java or C++ sense?

No. They are syntax over the prototype system.

### What does `new` do?

It creates an object, links it to the prototype, binds `this`, and returns the new object unless another object is returned explicitly.
