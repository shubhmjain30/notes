# Node.js Interview Questions And Model Answers

This is the rapid-fire revision sheet for the Node.js notes.

## Runtime

### What is Node.js?

Node.js is a JavaScript runtime built on V8 and libuv, designed for event-driven, non-blocking I/O workloads.

### Why is Node.js good for I/O-heavy applications?

Because it avoids thread-per-request overhead and handles async I/O efficiently through the event loop and runtime support.

### What is the event loop?

It is the runtime mechanism that schedules queued callbacks and async completions onto the main execution flow.

### What is libuv?

The library that powers Node's event loop, async I/O abstractions, and thread pool.

## Async

### Callbacks vs promises vs async/await?

Callbacks are the older style, promises improve composition, and async/await makes promise-based code easier to read.

### Why are streams important?

They let Node process large data incrementally, reducing memory pressure.

### Why can CPU-heavy code hurt Node.js badly?

Because it blocks the main JavaScript thread and delays all other work.

## Modules

### CommonJS vs ES Modules?

CommonJS uses `require` and `module.exports`. ES Modules use `import` and `export` and are the modern standard syntax.

### Why does module caching matter?

Because repeated imports usually return the same module instance, which affects shared state.

## Data

### SQL vs NoSQL?

Choose based on relationships, transactions, schema control, and access patterns.

### Mongoose vs Sequelize?

Mongoose is for MongoDB document modeling. Sequelize is for relational databases and table-based associations.

## Operations

### When is Node.js a bad fit?

When the workload is dominated by CPU-bound computation on the main thread.

### How do you avoid blocking the event loop?

Avoid sync APIs, stream large data, and move CPU-heavy work to worker threads or other services.
