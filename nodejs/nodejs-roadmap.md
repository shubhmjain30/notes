# Node.js Interview Roadmap

This folder should help you revise Node.js efficiently. Use this file to find the right note fast instead of reading long repeated explanations.

## How To Use This Folder

| File                                                                     | Purpose                                                   | When to Read            |
| ------------------------------------------------------------------------ | --------------------------------------------------------- | ----------------------- |
| [nodejs-runtime-architecture.md](nodejs-runtime-architecture.md)         | Event loop, libuv, thread pool, Node runtime model        | Start here              |
| [nodejs-asynchronous-programming.md](nodejs-asynchronous-programming.md) | Callbacks, promises, async/await, streams, async patterns | High-priority topic     |
| [nodejs-modules.md](nodejs-modules.md)                                   | CommonJS, ES Modules, resolution, caching                 | Core platform knowledge |
| [nodejs-core-modules.md](nodejs-core-modules.md)                         | `fs`, `path`, `http`, streams, process utilities          | Practical Node revision |
| [nodejs-data-modeling.md](nodejs-data-modeling.md)                       | SQL vs NoSQL, relations, embedding vs referencing         | Persistence design      |
| [nodejs-database-mongoose.md](nodejs-database-mongoose.md)               | MongoDB and Mongoose basics                               | NoSQL implementation    |
| [nodejs-database-sequelize.md](nodejs-database-sequelize.md)             | SQL and Sequelize basics                                  | SQL implementation      |
| [nodejs-interview-cheat-sheet.md](nodejs-interview-cheat-sheet.md)       | Rapid-fire interview questions and model answers          | Before interviews       |

## Reading Order

1. [nodejs-runtime-architecture.md](nodejs-runtime-architecture.md)
2. [nodejs-asynchronous-programming.md](nodejs-asynchronous-programming.md)
3. [nodejs-modules.md](nodejs-modules.md)
4. [nodejs-core-modules.md](nodejs-core-modules.md)
5. [nodejs-data-modeling.md](nodejs-data-modeling.md)
6. [nodejs-database-mongoose.md](nodejs-database-mongoose.md)
7. [nodejs-database-sequelize.md](nodejs-database-sequelize.md)
8. [nodejs-interview-cheat-sheet.md](nodejs-interview-cheat-sheet.md)

## Highest-Yield Topics

1. Event loop
2. Single-threaded execution model
3. Thread pool and libuv
4. Callbacks, promises, async/await
5. Streams
6. CommonJS vs ES Modules
7. Core modules
8. SQL vs NoSQL tradeoffs
9. Mongoose vs Sequelize
10. Error handling in async code

## Interview Buckets

### Junior

- What is Node.js?
- Why is Node.js good for I/O-heavy apps?
- What is the event loop?
- What is the difference between synchronous and asynchronous file APIs?
- What is a module?

### Mid-level

- How does Node.js handle concurrency with one thread?
- Difference between CommonJS and ES Modules?
- How do streams help performance?
- How do you structure async error handling?
- How do you choose SQL vs NoSQL?

### Senior

- When is Node.js a bad fit?
- What role does the thread pool play?
- How would you avoid blocking the event loop?
- How would you design data boundaries for scale?
- How would you structure observability and operational resilience?

## What To Memorize Verbatim

- Node.js is a runtime, not a framework.
- JavaScript execution is single-threaded, but Node can still handle many concurrent I/O operations.
- The event loop schedules callbacks and async work.
- CPU-heavy code can block the event loop.
- Streams reduce memory pressure by processing data incrementally.

## Final Advice

Node.js interviews are usually less about memorizing APIs and more about understanding runtime behavior, async control flow, and architecture tradeoffs.
