# Express Interview Roadmap

Interview-focused notes for Express request flow, middleware, REST API design, error handling, and production concerns.

This folder is designed for preparation, not passive reading. Use it to revise quickly, study in a sane order, and build strong answers around the request pipeline, API boundaries, and pragmatic backend tradeoffs.

## What This Folder Covers

- Express philosophy, setup, and request-response basics
- Routing, middleware composition, and request lifecycle design
- REST API structure, validation, controllers, and services
- Async error handling, response consistency, and production readiness
- Authentication, security, performance, and deployment awareness

## How To Use This Folder

1. Start with this roadmap.
2. Read the canonical topic notes in order.
3. Prioritize the highest-yield topics if time is limited.
4. Finish with the cheat sheet before interviews.
5. Practice giving short spoken answers for each core concept.

## File Map

| File                                                                 | Purpose                                                      | When to Read         |
| -------------------------------------------------------------------- | ------------------------------------------------------------ | -------------------- |
| [express-intro.md](express-intro.md)                                 | Express philosophy, setup, request-response model            | Start here           |
| [express-routing-middleware.md](express-routing-middleware.md)       | Routing, middleware chain, request lifecycle                 | Core Express topic   |
| [express-restful-api.md](express-restful-api.md)                     | REST API structure, validation, controllers, services        | API interviews       |
| [express-error-handling.md](express-error-handling.md)               | Error middleware, async errors, response patterns            | Production readiness |
| [express-advanced-features.md](express-advanced-features.md)         | Auth, security, real-time, performance, deployment awareness | Mid to senior prep   |
| [express-interview-cheat-sheet.md](express-interview-cheat-sheet.md) | Rapid-fire interview questions and model answers             | Before interviews    |

## Reading Order

1. [express-intro.md](express-intro.md)
2. [express-routing-middleware.md](express-routing-middleware.md)
3. [express-restful-api.md](express-restful-api.md)
4. [express-error-handling.md](express-error-handling.md)
5. [express-advanced-features.md](express-advanced-features.md)
6. [express-interview-cheat-sheet.md](express-interview-cheat-sheet.md)

## Highest-Yield Topics

If time is limited, prioritize these in order:

1. What Express is and why it exists
2. Routing and middleware
3. Request-response lifecycle
4. REST conventions
5. Input validation
6. Centralized error handling
7. Async handler patterns
8. Auth and security basics
9. App structure
10. Performance and deployment basics

## Interview Buckets

### Junior

- What is Express?
- Why use Express over raw Node `http`?
- What is middleware?
- How do routes work?
- How do you parse JSON request bodies?

### Mid-level

- How does the middleware chain work?
- How do you structure an Express API?
- How do you validate requests?
- How do you handle async errors?
- How do you design REST endpoints?

### Senior

- How would you secure an Express API?
- How would you avoid duplicated middleware logic?
- How would you structure a large Express codebase?
- When is Express a better fit than a more opinionated framework?
- How do you think about observability and production resilience?

## Suggested Preparation Plans

### 2-Hour Refresh

- Read [express-intro.md](express-intro.md)
- Read [express-routing-middleware.md](express-routing-middleware.md)
- Read [express-restful-api.md](express-restful-api.md)
- Review [express-error-handling.md](express-error-handling.md)
- Finish with [express-interview-cheat-sheet.md](express-interview-cheat-sheet.md)

### 1-Day Revision

- Cover request-response basics, routing, and middleware first
- Review REST design, validation, controllers, and service boundaries
- Revisit async error handling and response consistency
- Finish with security, auth, and production-awareness topics
- End with [express-interview-cheat-sheet.md](express-interview-cheat-sheet.md)

## Best Way To Get Interview Value From This Folder

- Explain Express as a request pipeline, not just a list of APIs
- Keep HTTP concerns, validation, business logic, and data access clearly separated in your answers
- Use error-handling and advanced-features notes to support production-readiness discussions
- Re-read only the topics you miss in mock interviews

## Final Advice

Start from the roadmap, then use the topic files as canonical references instead of jumping straight into isolated deep dives.
