# Frontend System Design Cheat Sheet

This is the rapid-fire revision layer for frontend system design interviews.

## Questions And Model Answers

### What is frontend system design?

It is the practice of structuring the client side of an application so it remains scalable, maintainable, performant, and reliable as requirements grow.

### What are the main frontend system design pillars?

Performance, scalability, maintainability, reliability, accessibility, and security.

### Monolith vs micro-frontends?

Monoliths are simpler and better for smaller or more cohesive teams. Micro-frontends are useful when team and deployment boundaries justify added complexity.

### CSR vs SSR?

CSR is simpler for highly interactive apps. SSR improves first content visibility and SEO at the cost of more complexity.

### What is the biggest frontend state mistake?

Putting every kind of state into one global solution instead of separating local UI state, shared client state, and server state.

### What should you always mention in a system design answer?

Performance, failure handling, accessibility, security, and tradeoffs.
