# Express Fundamentals

This file covers the core Express mental model: what Express is, why it exists, and how a request flows through an app.

## TL;DR

Express is a minimal web framework for Node.js that simplifies routing, middleware composition, and HTTP response handling without forcing a heavy architecture.

## Why Express Exists

Raw Node `http` is powerful but repetitive. Express adds a thin abstraction for common web tasks:

- Routing
- Middleware chaining
- Request and response helpers
- JSON parsing
- Static file serving

## What Express Is Good At

- REST APIs
- Backend services
- Traditional server-rendered apps
- Lightweight services and prototypes

## When Express May Not Be The Best Fit

- When you want strong framework conventions out of the box
- When you want built-in module architecture and dependency injection
- When another runtime or framework better fits strict performance or organizational needs

## Basic Server Example

```js
const express = require("express");
const app = express();

app.use(express.json());

app.get("/health", (req, res) => {
	res.json({ status: "ok" });
});

app.listen(3000, () => {
	console.log("Server running on port 3000");
});
```

## Request And Response Cycle

### What To Know

- A client sends a request.
- Express matches the request to middleware and routes.
- Middleware can read, modify, end, or pass the request onward.
- A route handler eventually sends the response.

## Common Traps

- Forgetting to send a response.
- Forgetting to call `next()` when middleware should continue.
- Sending multiple responses for one request.

## Interview Answer

Express sits on top of Node's HTTP layer and makes web server development simpler by providing middleware composition, routing, and response helpers without hiding the underlying request-response model.
