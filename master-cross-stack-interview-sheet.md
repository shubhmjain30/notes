# Master Cross-Stack Interview Sheet

Interview-focused cross-stack navigation for JavaScript, React.js, Node.js, Express.js, and system design.

This file is designed for preparation, not passive reading. Use it to revise by topic across the stack and build answers that connect language behavior, frontend architecture, backend runtime constraints, and API design tradeoffs.

## What This Sheet Covers

- A topic map that links related notes across JavaScript, React, Node.js, and Express
- Suggested preparation paths for frontend, backend, full-stack, and last-minute revision
- Cross-stack practice questions that force connected interview answers

## How To Use This Sheet

1. Start here when you want to revise by topic instead of by technology folder.
2. Pick one topic row from the map below.
3. Read the linked canonical notes across the stack.
4. Use the preparation paths if you want a role-based study sequence.
5. Practice the cross-stack questions aloud and answer them as one connected system.

## Topic Map

| Topic                   | JavaScript                                                                                                                                                                           | React                                                                                                                                                          | Node.js                                                                                                                                                                | Express                                                                            |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| Core language           | [javascript/javascript-first-principle.md](javascript/javascript-first-principle.md), [javascript/javascript-building-blocks.md](javascript/javascript-building-blocks.md)           | [reactjs/reactjs-foundations.md](reactjs/reactjs-foundations.md)                                                                                               | [nodejs/nodejs-runtime-architecture.md](nodejs/nodejs-runtime-architecture.md)                                                                                         | [expressjs/express-intro.md](expressjs/express-intro.md)                           |
| Scope, closures, `this` | [javascript/javascript-structure.md](javascript/javascript-structure.md)                                                                                                             | [reactjs/reactjs-core-proficiency.md](reactjs/reactjs-core-proficiency.md)                                                                                     | [nodejs/nodejs-modules.md](nodejs/nodejs-modules.md)                                                                                                                   | [expressjs/express-routing-middleware.md](expressjs/express-routing-middleware.md) |
| Async and event loop    | [javascript/javascript-async.md](javascript/javascript-async.md)                                                                                                                     | [reactjs/reactjs-core-proficiency.md](reactjs/reactjs-core-proficiency.md), [reactjs/reactjs-advanced-engineering.md](reactjs/reactjs-advanced-engineering.md) | [nodejs/nodejs-runtime-architecture.md](nodejs/nodejs-runtime-architecture.md), [nodejs/nodejs-asynchronous-programming.md](nodejs/nodejs-asynchronous-programming.md) | [expressjs/express-error-handling.md](expressjs/express-error-handling.md)         |
| State and data flow     | [javascript/javascript-system-design.md](javascript/javascript-system-design.md)                                                                                                     | [reactjs/reactjs-core-proficiency.md](reactjs/reactjs-core-proficiency.md), [reactjs/reactjs-advanced-engineering.md](reactjs/reactjs-advanced-engineering.md) | [nodejs/nodejs-data-modeling.md](nodejs/nodejs-data-modeling.md)                                                                                                       | [expressjs/express-restful-api.md](expressjs/express-restful-api.md)               |
| Performance             | [javascript/javascript-performance-memory.md](javascript/javascript-performance-memory.md), [javascript/javascript-advanced-frontend.md](javascript/javascript-advanced-frontend.md) | [reactjs/reactjs-advanced-engineering.md](reactjs/reactjs-advanced-engineering.md), [reactjs/reactjs-fiber-structure.md](reactjs/reactjs-fiber-structure.md)   | [nodejs/nodejs-runtime-architecture.md](nodejs/nodejs-runtime-architecture.md), [nodejs/nodejs-core-modules.md](nodejs/nodejs-core-modules.md)                         | [expressjs/express-advanced-features.md](expressjs/express-advanced-features.md)   |
| API design              | [javascript/javascript-system-design.md](javascript/javascript-system-design.md)                                                                                                     | [reactjs/reactjs-advanced-engineering.md](reactjs/reactjs-advanced-engineering.md)                                                                             | [nodejs/nodejs-data-modeling.md](nodejs/nodejs-data-modeling.md)                                                                                                       | [expressjs/express-restful-api.md](expressjs/express-restful-api.md)               |
| Errors and reliability  | [javascript/javascript-performance-memory.md](javascript/javascript-performance-memory.md)                                                                                           | [reactjs/reactjs-core-proficiency.md](reactjs/reactjs-core-proficiency.md), [reactjs/reactjs-expert-architectural.md](reactjs/reactjs-expert-architectural.md) | [nodejs/nodejs-asynchronous-programming.md](nodejs/nodejs-asynchronous-programming.md)                                                                                 | [expressjs/express-error-handling.md](expressjs/express-error-handling.md)         |
| Security                | [javascript/javascript-advanced-frontend.md](javascript/javascript-advanced-frontend.md)                                                                                             | [reactjs/reactjs-expert-architectural.md](reactjs/reactjs-expert-architectural.md)                                                                             | [nodejs/nodejs-runtime-architecture.md](nodejs/nodejs-runtime-architecture.md)                                                                                         | [expressjs/express-advanced-features.md](expressjs/express-advanced-features.md)   |
| System design           | [javascript/javascript-system-design.md](javascript/javascript-system-design.md)                                                                                                     | [reactjs/reactjs-expert-architectural.md](reactjs/reactjs-expert-architectural.md)                                                                             | [nodejs/nodejs-roadmap.md](nodejs/nodejs-roadmap.md)                                                                                                                   | [expressjs/express-roadmap.md](expressjs/express-roadmap.md)                       |

## Suggested Preparation Paths

### Frontend-Focused Path

1. [javascript/javascript-first-principle.md](javascript/javascript-first-principle.md)
2. [javascript/javascript-async.md](javascript/javascript-async.md)
3. [reactjs/reactjs-foundations.md](reactjs/reactjs-foundations.md)
4. [reactjs/reactjs-core-proficiency.md](reactjs/reactjs-core-proficiency.md)
5. [reactjs/reactjs-advanced-engineering.md](reactjs/reactjs-advanced-engineering.md)
6. [system-design/frontend-system-design.md](system-design/frontend-system-design.md)

### Backend-Focused Path

1. [nodejs/nodejs-runtime-architecture.md](nodejs/nodejs-runtime-architecture.md)
2. [nodejs/nodejs-asynchronous-programming.md](nodejs/nodejs-asynchronous-programming.md)
3. [expressjs/express-routing-middleware.md](expressjs/express-routing-middleware.md)
4. [expressjs/express-restful-api.md](expressjs/express-restful-api.md)
5. [system-design/backend-system-design.md](system-design/backend-system-design.md)

### Full-Stack JavaScript Path

1. [javascript/javascript-roadmap.md](javascript/javascript-roadmap.md)
2. [nodejs/nodejs-roadmap.md](nodejs/nodejs-roadmap.md)
3. [expressjs/express-roadmap.md](expressjs/express-roadmap.md)
4. [reactjs/reactjs-roadmap.md](reactjs/reactjs-roadmap.md)
5. [system-design/frontend-system-design-question.md](system-design/frontend-system-design-question.md)
6. [system-design/backend-system-design.md](system-design/backend-system-design.md)
7. [system-design/backend-system-design-question.md](system-design/backend-system-design-question.md)

### Last-Minute Interview Drill Path

1. [javascript/javascript-interview-cheat-sheet.md](javascript/javascript-interview-cheat-sheet.md)
2. [reactjs/reactjs-interview-cheat-sheet.md](reactjs/reactjs-interview-cheat-sheet.md)
3. [nodejs/nodejs-interview-cheat-sheet.md](nodejs/nodejs-interview-cheat-sheet.md)
4. [expressjs/express-interview-cheat-sheet.md](expressjs/express-interview-cheat-sheet.md)
5. [system-design/frontend-system-design-cheat-sheet.md](system-design/frontend-system-design-cheat-sheet.md)
6. [system-design/backend-system-design-question.md](system-design/backend-system-design-question.md)

## Cross-Stack Questions To Practice

### Explain the event loop from browser to Node.js.

Use:

- [javascript/javascript-async.md](javascript/javascript-async.md)
- [nodejs/nodejs-runtime-architecture.md](nodejs/nodejs-runtime-architecture.md)
- [expressjs/express-error-handling.md](expressjs/express-error-handling.md)

### Explain state from UI to API.

Use:

- [reactjs/reactjs-core-proficiency.md](reactjs/reactjs-core-proficiency.md)
- [reactjs/reactjs-advanced-engineering.md](reactjs/reactjs-advanced-engineering.md)
- [expressjs/express-restful-api.md](expressjs/express-restful-api.md)
- [nodejs/nodejs-data-modeling.md](nodejs/nodejs-data-modeling.md)

### Explain performance from browser rendering to backend response flow.

Use:

- [javascript/javascript-performance-memory.md](javascript/javascript-performance-memory.md)
- [reactjs/reactjs-fiber-structure.md](reactjs/reactjs-fiber-structure.md)
- [nodejs/nodejs-core-modules.md](nodejs/nodejs-core-modules.md)
- [expressjs/express-advanced-features.md](expressjs/express-advanced-features.md)

### Explain backend API, caching, auth, and database tradeoffs.

Use:

- [system-design/backend-system-design.md](system-design/backend-system-design.md)
- [nodejs/nodejs-data-modeling.md](nodejs/nodejs-data-modeling.md)
- [expressjs/express-restful-api.md](expressjs/express-restful-api.md)
- [expressjs/express-advanced-features.md](expressjs/express-advanced-features.md)

## Best Way To Get Interview Value From This Sheet

- Use this sheet when isolated stack knowledge starts feeling disconnected
- Move from browser and UI behavior to API design and backend runtime behavior in the same answer
- Prefer tradeoffs and flow explanations over stack-by-stack definitions
- Revisit the rows that expose weak connections between frontend and backend concepts

## Final Advice

If an interviewer asks a big question, answer it as a connected system. Start from the user interaction, move through frontend state and rendering, then explain API design and backend runtime behavior.
