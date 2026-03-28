# Advanced Express Topics

This file covers the mid-level and senior Express topics that usually come up after fundamentals and API design.

## TL;DR

Advanced Express discussions are usually about security, authentication, real-time integration, performance, and production discipline.

## Authentication

### What To Know

- Session-based auth
- Token-based auth such as JWT
- Middleware-based protection of routes

### Interview Answer

Authentication in Express is usually enforced through middleware that verifies identity before protected handlers run.

## Security Basics

### High-Value Topics

- Helmet for security headers
- Rate limiting
- Input validation and sanitization
- CORS configuration
- Dependency hygiene

### Common Trap

Do not treat CORS as a security mechanism by itself. It is a browser policy control, not full API security.

## Real-Time Features

### What To Know

Express often works alongside Socket.IO or WebSocket layers for real-time behavior.

Good examples:

- Chat
- Notifications
- Live dashboards

## Performance Awareness

### Good Rules

- Avoid blocking middleware
- Compress responses where appropriate
- Cache when justified
- Stream large payloads
- Keep request handlers thin

## Deployment Awareness

### What To Know

- Environment-based config
- Reverse proxy awareness
- Logging and monitoring
- Process managers such as PM2
- Container deployment basics

## Interview Answer

At scale, Express discussions are less about route syntax and more about how middleware, security, performance, and deployment practices work together in production.
