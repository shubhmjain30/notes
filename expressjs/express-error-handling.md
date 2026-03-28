# Error Handling In Express

This file covers the patterns interviewers expect for predictable production-grade error handling.

## TL;DR

Express error handling should be centralized, consistent, and safe for production. Do not scatter ad hoc `try/catch` blocks and random response shapes across routes.

## Error Middleware

An Express error handler has four parameters.

```js
function errorHandler(err, req, res, next) {
	res.status(500).json({ message: "Internal server error" });
}
```

## Centralized Error Handling

### What To Know

- Route handlers should throw or forward errors
- Error middleware should convert errors into consistent responses
- Production responses should avoid leaking internals

## Async Errors

### The Problem

Async route handlers can fail outside normal sync control flow.

### Good Pattern

Wrap async handlers so rejections flow into error middleware.

```js
const asyncHandler = (fn) => (req, res, next) => {
	Promise.resolve(fn(req, res, next)).catch(next);
};
```

## Custom Error Types

### Why They Help

Custom errors let you separate business failures from unexpected system failures.

Examples:

- Validation error
- Authentication error
- Not found error

## Good Error Response Rules

- Keep shape consistent
- Include useful but safe messages
- Log details internally
- Avoid leaking stack traces in production

## Interview Answer

I prefer centralized Express error handling with async wrappers, custom error classes where useful, and a stable error response format so clients and logs remain predictable.
