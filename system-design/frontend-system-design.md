# Frontend System Design Foundations

This file is the concise foundation for frontend system design interviews. The goal is not to memorize a giant theory manual. The goal is to reason clearly about tradeoffs.

## TL;DR

Frontend system design is about state ownership, data flow, rendering strategy, performance, reliability, maintainability, and team scalability.

## What Interviewers Usually Want

- Clarify requirements and constraints
- Identify the hardest frontend problems early
- Make explicit tradeoffs
- Structure the UI and data flow clearly
- Show awareness of performance, accessibility, and security

## Core Pillars

### Performance

Think about:

- Bundle size
- Network latency
- Rendering cost
- Interaction responsiveness
- Memory pressure

### Scalability

Think about:

- More users
- More features
- More teams
- More data on screen

### Maintainability

Think about:

- Clear ownership boundaries
- Reusable components
- Predictable state flow
- Testability
- Observability

### Reliability

Think about:

- Error boundaries
- Loading and failure states
- Retry behavior
- Monitoring
- Graceful degradation

### Accessibility

Think about:

- Semantic HTML
- Keyboard navigation
- Focus management
- Screen reader support

### Security

Think about:

- XSS
- Secure data handling
- Content Security Policy awareness
- Safe rendering of untrusted content

## Architectural Choices

### Monolithic Frontend

Good when:

- Team size is small
- Scope is manageable
- Speed of implementation matters more than organizational separation

### Component-Based Architecture

Good when:

- Reuse and composability matter
- You want maintainable UI boundaries
- The application is growing but still cohesive

### Micro-Frontends

Good when:

- Many teams need deployment independence
- Organizational boundaries are strong
- The app is large enough to justify complexity

### Interview Answer

I do not default to micro-frontends. I choose them only when team and deployment boundaries justify the overhead.

## Rendering Strategies

### CSR

Good for highly interactive apps where SEO is not the primary concern.

### SSR

Good when initial content visibility and SEO matter.

### SSG

Good for mostly static pages.

### ISR

Good when you want many static benefits but content changes over time.

## State Strategy

### Think In Layers

- Local UI state
- Shared client state
- Server state
- URL state

### Good One-Liner

One of the biggest frontend design mistakes is putting all state into one global mechanism without separating transient UI state from server cache state.

## API Strategy

### What To Discuss

- REST vs GraphQL vs real-time channels
- Pagination and filtering
- Caching and invalidation
- Optimistic updates
- Error states and retries

## Performance Playbook

### High-Value Levers

- Code splitting
- Image optimization
- Virtualization
- Request deduplication
- Caching
- Memoization only where justified

## Good Senior Answer Pattern

1. Clarify constraints.
2. Define core user journeys.
3. Split state and data responsibilities.
4. Choose rendering and API strategy.
5. Explain performance, reliability, and security tradeoffs.
6. Mention observability and future scaling.
