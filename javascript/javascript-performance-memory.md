# Performance And Memory In JavaScript

This file covers runtime behavior that matters in advanced interviews: memory leaks, garbage collection, rendering cost, and optimization decisions.

## TL;DR

Performance work starts with measurement. Memory problems usually come from unintended object retention, not from the language randomly wasting memory.

## Garbage Collection

### What To Know

- JavaScript engines use automatic garbage collection.
- Objects that are no longer reachable can be collected.
- Reachability is the key idea, not manual freeing.

## Common Memory Leaks

### Typical Causes

- Forgotten event listeners
- Long-lived timers
- Closures retaining large objects unnecessarily
- Detached DOM nodes still referenced in code
- Unbounded caches

### Interview Answer

A memory leak in JavaScript usually means something is still reachable when it should have been released, so the garbage collector cannot clean it up.

## Debounce And Throttle

### Debounce

Use when a function should run after rapid activity stops.

### Throttle

Use when a function should run at most once per interval.

### Common Examples

- Debounce search input
- Throttle scroll or resize handlers

## Rendering Performance

### Good Rules

- Minimize unnecessary DOM updates.
- Batch work when possible.
- Avoid layout thrashing.
- Use virtualization for large lists.

## Algorithmic Performance Still Matters

### What Interviewers Expect

Even in frontend work, poor Big-O choices can make the UI slow before rendering even starts.

## Optimization Mindset

### Good One-Liner

I profile first, identify the real bottleneck, and then choose the narrowest fix instead of applying generic micro-optimizations everywhere.

## Rapid-Fire Questions

### What causes memory leaks in JavaScript?

Unintended object retention through references that never get released.

### Debounce vs throttle?

Debounce waits until activity stops. Throttle limits how often work can run.

### Why profile first?

Because otherwise you may optimize the wrong thing and add complexity without improving user experience.
