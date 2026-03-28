# Frontend System Design Interview Prompts

This file is the practical prompt playbook for frontend system design interviews. Use it for mock interviews and structured answer practice.

## Universal Answer Framework

For almost every prompt:

1. Clarify users, scale, and constraints.
2. Identify the hardest frontend problems.
3. Propose the UI architecture.
4. Explain state and data flow.
5. Explain rendering and performance strategy.
6. Cover reliability, accessibility, and security.
7. Close with tradeoffs.

## Common Prompts

### Design a News Feed

Focus on:

- Infinite scrolling
- Pagination strategy
- Optimistic interactions
- Media loading
- Virtualization if scale is high

### Design a Video Platform

Focus on:

- Streaming UX
- Progressive loading
- Comment and recommendation panels
- Caching and media delivery
- Responsive controls

### Design an E-Commerce Frontend

Focus on:

- Product listing and filtering
- Cart state
- Search and sort
- Checkout flow
- Performance for image-heavy pages

### Design a Chat Application

Focus on:

- Real-time message updates
- Presence and typing indicators
- Offline recovery
- Message ordering and retry states
- Long-list rendering performance

### Design a Dashboard

Focus on:

- Widget isolation
- Data fetching ownership
- Polling vs push updates
- Heavy chart rendering
- Loading and partial failure handling

### Design an Autocomplete Component

Focus on:

- Debouncing
- Keyboard navigation
- Accessibility
- Request cancellation
- Result caching

### Design an Infinite Scroll List

Focus on:

- Pagination
- Virtualization
- Placeholder loading states
- Error recovery
- Memory usage

### Design a Collaborative Editor

Focus on:

- Sync model
- Conflict handling
- Presence indicators
- Local responsiveness
- Recovery after reconnect

## What Strong Answers Usually Include

- Clear separation of local state vs server state
- Explicit performance strategy
- Accessibility awareness
- Failure handling and observability
- Tradeoff reasoning instead of framework bias

## Common Interview Mistakes

- Jumping into implementation too early
- Ignoring error and loading states
- Ignoring accessibility
- Ignoring data-fetching ownership
- Saying "I would use X library" without explaining why
