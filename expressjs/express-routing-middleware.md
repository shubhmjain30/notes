# Routing And Middleware

This is the most important Express file for interviews. If you can explain middleware clearly, you understand the framework.

## TL;DR

Express processes requests through a middleware chain. Each middleware function can inspect the request, modify it, end the response, or pass control to the next step.

## Routing Basics

### What To Know

- Routes match by HTTP method and path.
- Route params live in `req.params`.
- Query params live in `req.query`.
- Request body lives in `req.body` after the right parser middleware runs.

### Example

```js
app.get("/users/:id", (req, res) => {
	res.json({ id: req.params.id, filter: req.query.filter || null });
});
```

## Middleware Basics

### Shape

```js
function middleware(req, res, next) {
	// do work
	next();
}
```

### What Middleware Can Do

- Log requests
- Authenticate users
- Validate input
- Parse bodies
- Handle errors
- Attach values to `req`

## Middleware Order Matters

Express runs middleware in the order it is registered.

```js
app.use(express.json());
app.use(authMiddleware);
app.use("/api/users", userRouter);
```

If order is wrong, behavior changes.

## Router-Level Middleware

Use routers to group related routes and local middleware.

```js
const router = express.Router();

router.use(authMiddleware);
router.get("/profile", getProfile);
```

## Common Traps

- Registering middleware after the routes that need it
- Forgetting `next()` in non-terminal middleware
- Doing too much in one middleware function
- Mixing authentication, validation, and business logic in one place

## Interview Answer

Middleware is the core abstraction in Express. It creates a pipeline where each step can process the request and either continue, end the response, or fail into error handling.
