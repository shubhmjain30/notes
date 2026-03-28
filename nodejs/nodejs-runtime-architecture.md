# Node.js Runtime Architecture

This file covers the Node.js runtime model: the event loop, libuv, thread pool, and why Node behaves differently from a thread-per-request server.

## TL;DR

Node.js runs JavaScript on a single main thread, delegates many I/O operations to the system or libuv, and uses the event loop to schedule callbacks back into execution.

## What Node.js Is

- A JavaScript runtime
- Built on the V8 engine
- Uses libuv for async I/O and runtime infrastructure

## Why Node.js Scales For I/O Work

### Core Idea

Node.js does not create a dedicated JavaScript thread for every request. Instead, it keeps JavaScript execution on one main thread and lets the runtime handle I/O efficiently.

### Good Interview Answer

Node.js is strong for I/O-bound workloads because it avoids thread-per-request overhead and uses non-blocking I/O plus an event loop to handle many concurrent operations.

## Event Loop

### TL;DR

The event loop checks whether the call stack is free and schedules queued callbacks for execution.

### What To Know

- Synchronous JavaScript still runs on one call stack.
- Timers, network events, and completed async work are scheduled back through the runtime.
- The event loop does not make CPU-heavy code free.

### Common Trap

Do not say Node is multithreaded just because it handles many requests at once. JavaScript execution is still single-threaded unless you explicitly use worker threads.

## libuv

### Why It Matters

libuv provides:

- The event loop
- Cross-platform async I/O abstractions
- A thread pool for specific operations

### Thread Pool Use Cases

The thread pool is commonly used for:

- File system operations
- DNS work in some cases
- Crypto operations
- Compression

## V8

### What To Know

- V8 compiles JavaScript to machine code.
- It performs runtime optimizations.
- Node.js runtime behavior is not only about V8; libuv matters too.

## When Node.js Is A Bad Fit

Node.js is usually a weaker choice when:

- The workload is CPU-heavy
- Long-running synchronous computation blocks the event loop
- You need thread-heavy parallel compute more than async I/O handling

## Avoid Blocking The Event Loop

### Practical Rules

- Avoid sync APIs in request paths.
- Push CPU-intensive work to worker threads or external services.
- Stream large data instead of buffering everything in memory.

## Rapid-Fire Questions

### Why is Node.js called single-threaded?

Because JavaScript execution runs on one main thread.

### How does Node.js handle many requests then?

By delegating I/O and scheduling callbacks through the event loop.

### What is libuv?

The library that powers Node's event loop, async I/O abstractions, and thread pool.

### When would Node.js be a poor choice?

When the application is dominated by CPU-bound work that blocks the main thread.
