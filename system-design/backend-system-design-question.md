# Backend System Design Interview Prompts

This file is the practical prompt playbook for backend system design interviews in a Node.js and Express stack. Use it for mock interviews and structured answer practice.

## Universal Answer Framework

For almost every backend prompt:

1. Clarify traffic, clients, latency targets, and consistency requirements.
2. Identify the hardest backend problems first.
3. Define API boundaries and request flow.
4. Choose data model and storage strategy.
5. Explain caching, async work, and scaling strategy.
6. Cover auth, security, reliability, and observability.
7. Close with tradeoffs and future evolution.

## Common Prompts

### Design a REST API for an E-Commerce Platform

Focus on:

- Product, cart, and order resource boundaries
- Idempotency for order creation where needed
- Inventory consistency strategy
- Read-heavy catalog endpoints and caching
- Payment workflow boundaries

### Design a Chat Backend

Focus on:

- WebSocket or Socket.IO connection model
- Message persistence and ordering
- Presence and typing indicators
- Retry and reconnect behavior
- Fan-out strategy for group conversations

### Design a Notification Service

Focus on:

- Event producers and consumers
- Notification preferences and delivery rules
- Retry and dead-letter behavior
- Email, push, and in-app channel separation
- Rate limiting and duplicate suppression

### Design an Authentication and Authorization Service

Focus on:

- Session vs JWT tradeoffs
- Refresh token and revocation strategy
- Role and permission model
- Password reset and account recovery flow
- Audit logging for sensitive actions

### Design a File Upload and Media Processing Service

Focus on:

- Direct upload vs server-proxied upload
- Validation and size limits
- Background processing for thumbnails or transcoding
- Storage strategy and signed URL access
- Security scanning and abuse prevention

### Design a Search and Autocomplete Backend

Focus on:

- Query latency targets
- Debounced clients and rate limiting
- Caching hot queries
- Prefix search vs full-text search strategy
- Ranking and relevance evolution

### Design a Public Rate-Limited API

Focus on:

- API keys or OAuth model
- Per-user or per-client rate limits
- Burst vs steady-state policy
- Abuse detection and monitoring
- Versioning and deprecation strategy

### Design a Webhook Processing System

Focus on:

- Signature verification
- Retry and idempotency
- Queue-based processing
- Backpressure handling
- Visibility into failures and replay support

### Design a Background Job System

Focus on:

- Queue model
- Worker isolation from request path
- Retry and poison job handling
- Scheduling and delayed jobs
- Observability for job latency and failure rate

### Design an Analytics Event Ingestion API

Focus on:

- High-write ingestion path
- Batching and buffering
- Schema validation strategy
- Sampling and retention policies
- Separation between ingestion and reporting workloads

### Design a Caching Strategy for a Content API

Focus on:

- Edge cache vs app cache vs database cache
- Cache invalidation rules
- TTL decisions and stale tolerance
- Personalized vs public content boundaries
- Monitoring cache hit ratio and correctness issues

### Design a Multi-Tenant SaaS Backend

Focus on:

- Tenant isolation model
- Shared database vs separate database tradeoff
- Auth and authorization boundaries per tenant
- Rate limiting and noisy neighbor control
- Tenant-aware logging and observability

### Design a Payment and Order Processing Backend

Focus on:

- Payment provider integration boundaries
- Order state machine
- Idempotency keys for retries
- Compensation or reconciliation strategy
- Audit trail for financial operations

### Design a Feed Generation Backend

Focus on:

- Fan-out on write vs fan-out on read
- Ranking pipeline boundaries
- Cache strategy for hot feeds
- Pagination design
- Real-time update requirements

## What Strong Answers Usually Include

- Clear separation of request path work vs background work
- Explicit consistency tradeoffs
- Cache invalidation awareness
- Idempotency for retried or externally triggered workflows
- Authentication and authorization considered separately
- Observability and failure handling from the start

## Common Interview Mistakes

- Designing only happy-path CRUD endpoints
- Ignoring retries and duplicate requests
- Ignoring background processing opportunities
- Saying "use Redis" or "use Kafka" without explaining why
- Avoiding consistency and stale-data tradeoffs
- Skipping auth, rate limiting, and abuse prevention

## Practice Advice

For each prompt, answer in this order:

1. One-minute architecture sketch
2. Three-minute deep dive on the hardest tradeoff
3. One-minute explanation of failure handling

That pattern usually exposes whether the design is superficial or production-aware.
