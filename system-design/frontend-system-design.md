# Frontend System Design Foundations

Interview-focused notes for frontend system design, covering state ownership, rendering strategy, performance, reliability, accessibility, and architecture tradeoffs.

This file is designed for preparation, not passive reading. Use it to build clear system design answers, reason about tradeoffs, and connect product requirements to frontend architecture decisions.

## What This File Covers

- State ownership, data flow, rendering strategy, and API interaction
- Performance, reliability, accessibility, security, and maintainability
- Architecture choices from monoliths to micro-frontends
- A reusable answer pattern for frontend system design interviews

## How To Use This File

1. Start here before practicing prompt-based frontend system design rounds.
2. Use each section as a checklist for the tradeoffs you should mention.
3. Map the ideas to one product you already know, such as a feed, dashboard, or commerce UI.
4. Continue with the prompt and cheat-sheet files for drills and fast recall.
5. Practice giving a 5 to 10 minute answer using the answer pattern near the end.

## Best Companion Files

| File                                                                                   | Purpose                                                        | When to Read          |
| -------------------------------------------------------------------------------------- | -------------------------------------------------------------- | --------------------- |
| [frontend-system-design-question.md](frontend-system-design-question.md)               | Prompt playbook for mock interviews                            | After this foundation |
| [frontend-system-design-cheat-sheet.md](frontend-system-design-cheat-sheet.md)         | Rapid-fire revision layer                                      | Before interviews     |
| [../reactjs/reactjs-roadmap.md](../reactjs/reactjs-roadmap.md)                         | React interview path that supports UI architecture discussions | Alongside this file   |
| [../javascript/javascript-system-design.md](../javascript/javascript-system-design.md) | Frontend architecture companion from the JavaScript side       | After this foundation |

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

## Strong Interview Answer Pattern

1. Clarify constraints.
2. Define core user journeys.
3. Split state and data responsibilities.
4. Choose rendering and API strategy.
5. Explain performance, reliability, and security tradeoffs.
6. Mention observability and future scaling.

## Suggested Preparation Plans

### 2-Hour Refresh

- Read this file once from top to bottom
- Drill the rendering, state, performance, and reliability sections aloud
- Review [frontend-system-design-question.md](frontend-system-design-question.md)
- Finish with [frontend-system-design-cheat-sheet.md](frontend-system-design-cheat-sheet.md)

### 1-Day Revision

- Read this foundation file and turn each major section into a spoken answer
- Practice 3 to 5 prompts from [frontend-system-design-question.md](frontend-system-design-question.md)
- Revisit React architecture topics using [../reactjs/reactjs-roadmap.md](../reactjs/reactjs-roadmap.md)
- End with [frontend-system-design-cheat-sheet.md](frontend-system-design-cheat-sheet.md)

## Best Way To Get Interview Value From This File

- Do not memorize architecture names without explaining when they fit
- Separate local UI state, shared client state, server state, and URL state clearly
- Always mention performance, reliability, accessibility, and security
- Re-read only the sections you struggle to include in mock answers

## Final Advice

Use this file to build your reasoning model first. Once that model is clear, use the prompt file to practice applying it under interview pressure.
