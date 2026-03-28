# JavaScript Frontend System Design

This file is for senior frontend interviews. The goal is not to memorize a framework-specific architecture diagram. The goal is to reason clearly about data flow, boundaries, performance, resilience, and developer experience.

## TL;DR

In frontend system design, the hardest problems are usually state ownership, data flow, performance under load, and long-term maintainability.

## What Interviewers Usually Want

- Clear separation of concerns
- Good state ownership decisions
- API and caching strategy
- Rendering and performance awareness
- Failure handling and observability
- Scalability for teams as well as for traffic

## Core Design Buckets

### State

Ask:

- What state is local?
- What state is shared?
- What is server state vs client UI state?
- How often does each piece change?

### Data Flow

Ask:

- Where is data fetched?
- Where is it cached?
- How are retries, errors, and invalidation handled?

### Rendering

Ask:

- What renders frequently?
- What can be deferred or virtualized?
- What can be lazy-loaded?

### Team Boundaries

Ask:

- How do features stay isolated?
- What belongs in a shared design system?
- How are contracts documented and enforced?

## Good Senior Answers Sound Like This

- Keep ephemeral UI state local.
- Separate client state from server cache state.
- Prefer feature boundaries over type-based folders in large apps.
- Use measurement before optimization.
- Design for observability from the start.

## Common Frontend System Design Prompts

### Design a dashboard with many widgets

Focus on data-fetching ownership, widget isolation, caching, lazy loading, and render boundaries.

### Design an infinite feed

Focus on pagination, virtualization, prefetching, stale data handling, and optimistic interactions.

### Design a collaborative editor

Focus on concurrency, conflict handling, local latency, and synchronization strategy.

## What To Say About Performance

I think in layers: bundle size, network cost, render frequency, DOM cost, and memory retention. Different problems need different fixes.

## What To Say About Maintainability

I prefer clear ownership boundaries, small shared abstractions, and a system that is easy to debug over a clever architecture that only a few people understand.
