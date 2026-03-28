# Node.js Interview Roadmap

Interview-focused notes for Node.js runtime behavior, async programming, modules, core APIs, and data modeling tradeoffs.

This folder is designed for preparation, not passive reading. Use it to revise quickly, study in a sane order, and build strong answers around runtime constraints, concurrency, and backend architecture tradeoffs.

## What This Folder Covers

- Event loop, libuv, thread pool, and runtime architecture
- Callbacks, promises, async/await, streams, and async control flow
- CommonJS, ES Modules, resolution, caching, and core APIs
- Data modeling, SQL vs NoSQL, and persistence tradeoffs
- Operational thinking around performance, blocking work, and resilience

## How To Use This Folder

1. Start with this roadmap.
2. Read the canonical topic notes in order.
3. Prioritize the highest-yield topics if time is limited.
4. Finish with the cheat sheet before interviews.
5. Practice giving short spoken answers for each core concept.

## File Map

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

If time is limited, prioritize these in order:

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

## Suggested Preparation Plans

### 2-Hour Refresh

- Read [nodejs-runtime-architecture.md](nodejs-runtime-architecture.md)
- Read [nodejs-asynchronous-programming.md](nodejs-asynchronous-programming.md)
- Review [nodejs-modules.md](nodejs-modules.md)
- Review [nodejs-core-modules.md](nodejs-core-modules.md)
- Finish with [nodejs-interview-cheat-sheet.md](nodejs-interview-cheat-sheet.md)

### 1-Day Revision

- Cover runtime architecture, async programming, and modules first
- Review core modules and common operational patterns
- Revisit persistence tradeoffs in [nodejs-data-modeling.md](nodejs-data-modeling.md)
- Read the database implementation notes most relevant to your target stack
- End with [nodejs-interview-cheat-sheet.md](nodejs-interview-cheat-sheet.md)

## Best Way To Get Interview Value From This Folder

- Explain Node.js as a runtime model, not just a package ecosystem
- Focus on why the event loop works well for I/O-heavy systems and where it breaks down
- Use data-modeling notes to support backend architecture answers
- Re-read only the topics you miss in mock interviews

## Final Advice

Start from the roadmap, then use the topic files as canonical references instead of collecting overlapping summaries from multiple notes.
