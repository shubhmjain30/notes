# JavaScript Interview Roadmap

Interview-focused notes for JavaScript fundamentals, async behavior, object model, performance, and frontend architecture.

This folder is designed for preparation, not passive reading. Use it to revise quickly, study in a sane order, and build interview answers around language behavior, browser runtime details, and tradeoffs.

## What This Folder Covers

- Scope, hoisting, closures, and `this`
- Types, coercion, equality, and object behavior
- Prototypes, inheritance, classes, and the object model
- Event loop, promises, async/await, and concurrency basics
- Memory, performance, DOM behavior, and frontend architecture

## How To Use This Folder

1. Start with this roadmap.
2. Read the canonical topic notes in order.
3. Prioritize the highest-yield topics if time is limited.
4. Finish with the cheat sheet before interviews.
5. Practice giving short spoken answers for each core concept.

## File Map

| File                                                                       | Purpose                                          | When to Read                  |
| -------------------------------------------------------------------------- | ------------------------------------------------ | ----------------------------- |
| [javascript-first-principle.md](javascript-first-principle.md)             | Compilation, scope, hoisting                     | Start here                    |
| [javascript-building-blocks.md](javascript-building-blocks.md)             | Types, coercion, equality                        | After foundations             |
| [javascript-structure.md](javascript-structure.md)                         | Closures, `this`, modules                        | Core language prep            |
| [javascript-object-model.md](javascript-object-model.md)                   | Prototypes, inheritance, classes                 | Mid-level prep                |
| [javascript-async.md](javascript-async.md)                                 | Event loop, promises, async patterns             | High-priority interview topic |
| [javascript-modern-toolkit.md](javascript-modern-toolkit.md)               | ES6+ syntax and modern APIs                      | Practical revision            |
| [javascript-performance-memory.md](javascript-performance-memory.md)       | Memory, GC, optimization                         | Advanced prep                 |
| [javascript-advanced-frontend.md](javascript-advanced-frontend.md)         | DOM, browser APIs, rendering performance         | Frontend-focused interviews   |
| [javascript-system-design.md](javascript-system-design.md)                 | Frontend architecture and system design          | Senior interviews             |
| [javascript-interview-cheat-sheet.md](javascript-interview-cheat-sheet.md) | Rapid-fire interview questions and model answers | Before interviews             |

## Reading Order

1. [javascript-first-principle.md](javascript-first-principle.md)
2. [javascript-building-blocks.md](javascript-building-blocks.md)
3. [javascript-structure.md](javascript-structure.md)
4. [javascript-object-model.md](javascript-object-model.md)
5. [javascript-async.md](javascript-async.md)
6. [javascript-modern-toolkit.md](javascript-modern-toolkit.md)
7. [javascript-performance-memory.md](javascript-performance-memory.md)
8. [javascript-advanced-frontend.md](javascript-advanced-frontend.md)
9. [javascript-system-design.md](javascript-system-design.md)
10. [javascript-interview-cheat-sheet.md](javascript-interview-cheat-sheet.md)

## Highest-Yield Topics

If time is limited, prioritize these in order:

1. Scope and hoisting
2. Closures
3. `this` binding rules
4. Types and coercion
5. Equality rules
6. Event loop and microtasks
7. Promises and async/await
8. Prototypes and inheritance
9. Memory leaks
10. DOM performance

## Interview Buckets

### Junior

- What is hoisting?
- Difference between `var`, `let`, and `const`?
- What is closure?
- What are truthy and falsy values?
- Difference between `==` and `===`?

### Mid-level

- Explain lexical scope.
- Explain the event loop.
- What is the prototype chain?
- How does `this` work?
- How do promises differ from callbacks?

### Senior

- How does JavaScript handle concurrency with one thread?
- When does a function close over variables?
- What causes memory leaks in frontend apps?
- How would you reason about rendering performance?
- How would you structure a large frontend codebase?

## Suggested Preparation Plans

### 2-Hour Refresh

- Read [javascript-first-principle.md](javascript-first-principle.md)
- Read [javascript-building-blocks.md](javascript-building-blocks.md)
- Read [javascript-structure.md](javascript-structure.md)
- Read [javascript-async.md](javascript-async.md)
- Finish with [javascript-interview-cheat-sheet.md](javascript-interview-cheat-sheet.md)

### 1-Day Revision

- Cover core language first: first principle, building blocks, structure
- Review runtime behavior with [javascript-async.md](javascript-async.md)
- Revisit modern syntax in [javascript-modern-toolkit.md](javascript-modern-toolkit.md)
- Finish advanced topics: object model, performance, frontend, and system design
- End with [javascript-interview-cheat-sheet.md](javascript-interview-cheat-sheet.md)

## Best Way To Get Interview Value From This Folder

- Do not memorize definitions without examples
- Explain why language behavior happens, not just what happens
- Use async and object model notes to connect language and runtime questions
- Re-read only the topics you miss in mock interviews

## Final Advice

Start from the roadmap, then use the topic files as canonical references instead of re-reading duplicated summaries.
