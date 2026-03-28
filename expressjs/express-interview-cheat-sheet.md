# Express Interview Questions And Model Answers

This is the rapid-fire revision sheet for the Express folder.

## Fundamentals

### What is Express?

Express is a minimal web framework for Node.js that simplifies routing, middleware, and HTTP response handling.

### Why use Express over raw Node `http`?

Because it removes repetitive boilerplate and provides a cleaner abstraction for common web server tasks.

### What is middleware?

Middleware is a function in the request pipeline that can inspect the request, modify it, end the response, or pass control onward.

## Routing

### What is the difference between `req.params`, `req.query`, and `req.body`?

`req.params` comes from the path, `req.query` comes from the query string, and `req.body` comes from the parsed request payload.

### Why does middleware order matter?

Because Express executes middleware in registration order.

## APIs

### What makes an Express API RESTful?

Clear resource-based routes, proper HTTP methods, consistent status codes, and predictable response design.

### Why separate controllers and services?

To keep HTTP concerns separate from business logic and make the code easier to test and maintain.

## Errors

### How do you handle async errors in Express?

Wrap async handlers and forward rejections to centralized error middleware.

### Why use centralized error handling?

It keeps error responses consistent and prevents duplicated logic across routes.

## Production

### How do you secure an Express app?

Use validation, authentication middleware, secure headers, rate limiting, dependency hygiene, and careful error responses.

### What is a common Express performance mistake?

Blocking the event loop inside handlers or middleware with slow synchronous work.
