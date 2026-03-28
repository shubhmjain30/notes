# JavaScript Interview Questions And Model Answers

This is the rapid-fire revision layer for the JavaScript notes.

## Core Language

### What is lexical scope?

Lexical scope means scope is determined by where code is written, not where a function is called.

### What is hoisting?

Hoisting is the visible result of declarations being processed before execution.

### What is closure?

A closure is when a function retains access to variables from the scope where it was created.

### Difference between `var`, `let`, and `const`?

`var` is function-scoped. `let` and `const` are block-scoped. `const` prevents rebinding.

### Difference between `==` and `===`?

`===` compares without coercion. `==` allows coercion according to JavaScript rules.

## Async

### What is the event loop?

It is the mechanism that schedules queued async work onto the call stack when the stack is empty.

### Why do promise callbacks run before setTimeout?

Because promise handlers are microtasks and are processed before the next macrotask.

### Promises vs async/await?

`async/await` is syntax built on top of promises that makes async code easier to read.

## Object Model

### What is the prototype chain?

The lookup chain JavaScript follows when a property is missing on the current object.

### Are classes real classes in the Java sense?

No. They are syntax over JavaScript's prototype system.

## Performance

### What causes memory leaks in JavaScript?

Objects remain reachable when they should have been released.

### Debounce vs throttle?

Debounce waits for activity to stop. Throttle limits how often a function can run.
