# Building RESTful APIs In Express

This file focuses on API design, route structure, validation, and common Express project organization patterns.

## TL;DR

A good Express API is not just a set of routes. It has clear resource design, consistent validation, predictable status codes, and clean separation between routing, business logic, and persistence.

## REST Basics

### What To Know

- Resources are nouns such as `/users` or `/orders`
- HTTP methods express action
- Status codes should communicate outcome clearly

### Common CRUD Pattern

- `GET /users`
- `GET /users/:id`
- `POST /users`
- `PATCH /users/:id`
- `DELETE /users/:id`

## Good Project Structure

A clean Express API usually separates:

- Routes
- Controllers
- Services
- Data access layer
- Middleware

### Why This Matters

This prevents route files from becoming a mix of HTTP plumbing, validation, business logic, and database code.

## Validation

### What To Know

- Validate at the API boundary
- Return consistent error shapes
- Do not trust client input

### Example Pattern

```js
app.post("/users", validateCreateUser, createUser);
```

## Status Codes

### Good Defaults

- `200` for successful reads and updates
- `201` for successful creation
- `204` for successful delete with no body
- `400` for validation failures
- `401` for unauthenticated
- `403` for unauthorized
- `404` for not found
- `500` for unexpected server failure

## Pagination And Filtering

### What To Know

Interviews often expect at least a basic answer for:

- Pagination
- Filtering
- Sorting
- Search parameters

### Good One-Liner

I expose these through query parameters and keep the API response consistent with metadata when needed.

## Interview Answer

When building an Express REST API, I focus on clear resources, consistent HTTP semantics, validation at the edges, and separation of transport logic from business logic.
