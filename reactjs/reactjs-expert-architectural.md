# Stage 4: Expert And Architectural

This file is for senior and lead-level interview preparation. The focus is not syntax. The focus is how React behaves at scale and how to make defensible architecture decisions.

## Checklist

- Reconciliation and Fiber
- Lanes and scheduling
- Server Components and hydration
- State architecture at scale
- Migration strategy
- Security and monitoring

## Reconciliation And Fiber

### TL;DR

React keeps UI updates efficient by reconciling trees and applying the smallest practical set of changes. Fiber made that work interruptible and priority-aware.

### What To Know

- Reconciliation compares previous and next element trees.
- Different element types usually mean remount.
- Keys preserve child identity.
- Fiber replaced the old synchronous stack reconciler.

For the interview-focused internals summary, use [reactjs-fiber-structure.md](reactjs-fiber-structure.md).

## Lanes And Scheduling

### TL;DR

React assigns work different priorities so urgent interactions do not wait behind low-priority rendering.

### Why It Matters

- Typing should stay responsive.
- Expensive updates should not block urgent ones.
- This is part of how transitions work in React 18.

### Interview Answer

Lanes are React's priority model for updates. They let React schedule urgent and non-urgent work differently so the UI can remain responsive under load.

## Server Components And Hydration

### What To Know

- Server Components run on the server and reduce client bundle size.
- Client Components handle interactivity.
- Hydration attaches client behavior to server-rendered HTML.
- Streaming improves perceived performance by sending UI in chunks.

### Good One-Liner

Server Components move more work to the server and ship less JavaScript to the browser, while hydration and streaming control how interactive UI becomes available on the client.

## State Architecture At Scale

### Core Principle

The hardest React state problem is not how to store data. It is deciding who should own it.

### Good Rules

- Keep ephemeral UI state local.
- Share low-frequency values with Context.
- Use a structured external store when cross-feature coordination becomes complex.
- Separate server state from client UI state.

### Interview Trap

Do not mix server-fetched cache state, form state, and transient UI state into one giant store without a reason.

## Large-Scale Component Architecture

### What Strong Answers Usually Mention

- Feature-based folder boundaries
- Shared design system components
- Clear data-fetching ownership
- Stable state boundaries
- Test strategy by layer
- Performance budgets and profiling culture

### Good One-Liner

At scale, I care more about ownership boundaries, state placement, and observability than about choosing a fashionable library.

## Migration Strategy

### Common Migration Topics

- Class components to hooks
- CRA to Vite or Next.js
- Classic Redux to RTK
- Ad hoc fetch logic to a proper data layer

### What Interviewers Want

They want to know whether you can improve a real codebase gradually without destabilizing delivery.

### Good One-Liner

I prefer incremental migrations behind clear boundaries, with compatibility layers and measurable checkpoints, instead of large rewrites that stall product work.

## Security And Monitoring

### Security Topics To Know

- XSS and `dangerouslySetInnerHTML`
- Token handling
- Content Security Policy awareness
- Escaping and sanitizing untrusted content

### Monitoring Topics To Know

- Error tracking
- Real user monitoring
- Core Web Vitals
- Performance tracing

### Interview Answer

Production React quality is not only about rendering. It is also about protecting users, detecting failures early, and measuring real performance in the field.

## Senior Interview Prompts

### How would you structure a large React app?

I would start with clear feature boundaries, localize state wherever possible, use Context selectively, adopt an external store only when justified, and standardize data fetching, testing, and observability patterns early.

### When would you adopt Server Components?

When bundle size, server-side data access, and rendering cost justify moving work off the client, especially in framework-based React apps such as modern Next.js.

### How do you approach migrations?

I optimize for incremental risk reduction. I keep delivery moving while isolating old patterns behind boundaries and replacing them piece by piece.

## What To Revise Next

Review [reactjs-fiber-structure.md](reactjs-fiber-structure.md) for internals and [reactjs-advanced-engineering.md](reactjs-advanced-engineering.md) for the practical architecture tradeoffs interviewers ask most often.
