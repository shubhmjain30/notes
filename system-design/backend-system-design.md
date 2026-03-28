# Backend System Design For Node.js And Express

This file is the backend system design companion to the frontend system design notes. It is written for JavaScript interviews where the backend stack is primarily Node.js plus Express.

## TL;DR

Backend system design in a Node.js and Express stack is mostly about request flow, API design, async behavior, caching strategy, authentication, data ownership, and operational tradeoffs.

## What Interviewers Usually Want

- Clear API boundaries
- Good understanding of Node.js runtime constraints
- Clean Express request pipeline design
- Caching and performance tradeoffs
- Authentication and authorization strategy
- Database tradeoff reasoning
- Observability and resilience awareness

## The Stack Roles

### Node.js

Node.js is the runtime.

It matters because of:

- Event loop behavior
- Non-blocking I/O strengths
- Weakness under CPU-heavy work on the main thread

### Express

Express is the HTTP layer and request pipeline.

It matters because of:

- Routing
- Middleware composition
- Validation boundaries
- Error handling and response shape consistency

### Good One-Liner

Node.js gives you the runtime model. Express gives you the transport and middleware layer. Good backend design needs both to be used deliberately.

## API Design Tradeoffs

### REST

Good when:

- Resources map naturally to routes
- Caching through HTTP semantics is useful
- Simplicity and broad compatibility matter

Tradeoffs:

- Can over-fetch or under-fetch
- Deeply related reads can become chatty

### GraphQL

Good when:

- Clients need flexible data shapes
- Frontend teams need to compose data efficiently
- A single endpoint simplifies client querying

Tradeoffs:

- More operational complexity
- Harder caching story in some cases
- Requires stronger schema governance

### Real-Time Channels

Good when:

- Notifications, chat, live dashboards, collaboration, or presence updates matter

Tradeoffs:

- More connection management complexity
- More operational and consistency concerns

### Interview Answer

I choose the API style based on access patterns. REST is usually the default for clarity and operational simplicity. I use GraphQL when client data composition is the real bottleneck. I add real-time channels only when the product actually needs live behavior.

## Request Flow Design

### Recommended Layers

- Router
- Validation middleware
- Authentication middleware
- Controller
- Service layer
- Data access layer
- Error middleware

### Why This Matters

This keeps HTTP concerns separate from business logic and storage concerns.

### Common Trap

Do not put validation, auth, business logic, and raw database queries into one large route handler.

## Caching Tradeoffs

Caching is one of the fastest ways to improve backend performance, but only if cache ownership and invalidation are clear.

### Main Layers

#### CDN Or Edge Cache

Best for:

- Static assets
- Public responses with clear cacheability

#### Application Cache

Best for:

- Expensive computed responses
- Frequently requested reference data
- Aggregated read models

#### Database Cache

Best for:

- Repeated query-heavy workloads
- Read-heavy applications

### Key Tradeoffs

- Better latency vs harder invalidation
- Lower database load vs risk of stale data
- Simpler reads vs more operational complexity

### Good Interview Answer

I only add caching when I know what is expensive, how stale data is allowed to be, and how invalidation will work. A fast cache with unclear invalidation usually becomes a correctness problem.

## Authentication And Authorization

### Authentication

Proves who the user is.

Common choices:

- Session-based auth
- JWT or token-based auth
- OAuth or SSO in larger systems

### Authorization

Determines what the user is allowed to do.

Common approaches:

- Role-based access control
- Permission-based checks
- Resource ownership checks

### Session Vs JWT Tradeoff

#### Session-Based Auth

Good when:

- Server-managed sessions are acceptable
- Revocation needs to be straightforward
- Traditional web apps or controlled clients are common

Tradeoffs:

- Server-side session storage needed
- Horizontal scaling needs shared session infrastructure

#### JWT Or Stateless Token Auth

Good when:

- APIs serve multiple clients
- Stateless validation is valuable
- Distributed services need portable identity tokens

Tradeoffs:

- Revocation is harder
- Token size and expiration strategy matter
- Bad token handling can create security issues quickly

### Interview Answer

I separate authentication from authorization. I choose sessions when server-managed control and revocation matter most, and I choose token-based auth when client diversity and service portability matter more.

## Database Tradeoffs

### SQL

Good when:

- Relationships are strong
- Transactions matter
- Data integrity rules are strict

Use with this stack:

- Sequelize or another ORM if it improves productivity
- Raw SQL when performance or query control demands it

### NoSQL

Good when:

- Data is document-oriented
- Schema evolution is frequent
- Read patterns favor denormalized documents

Use with this stack:

- Mongoose for MongoDB model structure and validation

### Good Interview Answer

I choose the database model based on access patterns, relationship complexity, and consistency needs. I do not choose SQL or NoSQL based on trend alone.

## Scaling Tradeoffs

### Horizontal Scaling

Usually the first scaling lever for Node and Express services.

Consider:

- Stateless app instances
- Shared cache or session store if needed
- Load balancing

### Background Processing

Move long-running or heavy work out of the request-response path.

Examples:

- Email sending
- Image processing
- Report generation
- Webhook retries

### Good One-Liner

If a request path becomes slow because of work that does not need to finish before the response, it is a candidate for background processing.

## Reliability And Observability

### What To Mention

- Structured logging
- Request IDs and trace correlation
- Metrics and alerting
- Health checks
- Graceful shutdown
- Retry and timeout policy

### Common Mistake

Designing only the happy path and saying nothing about failure, timeout, or degraded operation.

## Strong Interview Answer Pattern

1. Clarify traffic, clients, data shape, and latency constraints.
2. Define API style and request boundaries.
3. Explain auth and authorization.
4. Choose data model and storage tradeoffs.
5. Add caching where it is justified.
6. Cover scaling, async jobs, and observability.
7. Close with tradeoffs instead of pretending there is one perfect design.

## Related Notes

- [nodejs/nodejs-runtime-architecture.md](../nodejs/nodejs-runtime-architecture.md)
- [nodejs/nodejs-asynchronous-programming.md](../nodejs/nodejs-asynchronous-programming.md)
- [nodejs/nodejs-data-modeling.md](../nodejs/nodejs-data-modeling.md)
- [expressjs/express-restful-api.md](../expressjs/express-restful-api.md)
- [expressjs/express-error-handling.md](../expressjs/express-error-handling.md)
- [expressjs/express-advanced-features.md](../expressjs/express-advanced-features.md)
