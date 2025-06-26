# Express.js Routing & Middleware

Routing and middleware are the backbone of Express.js, enabling modular, maintainable, and powerful web applications.

## 1. Routing System

Express maps HTTP methods and URL paths to handler functions. You can use:

-   String paths (e.g., `/users`)
-   Named parameters (e.g., `/users/:id`)
-   Regular expressions
-   Wildcards (e.g., `/api/*`)

```js
app.get("/users/:id", getUserById);
app.post("/products", createProduct);
app.all("/api/*", authenticate);
```

## 2. Route Handlers & Middleware

Express allows multiple handler functions per route, supporting separation of concerns (e.g., authentication, validation, business logic).

Middleware functions have the signature `(req, res, next) => { ... }` and can:

-   Execute code
-   Modify `req` and `res`
-   End the request-response cycle
-   Call `next()` to pass control

### Types of Middleware

-   **Application-level:** `app.use()`
-   **Router-level:** `router.use()`
-   **Error-handling:** Four arguments `(err, req, res, next)`
-   **Built-in:** e.g., `express.json()`
-   **Third-party:** e.g., `morgan`, `cors`

```js
const logger = (req, res, next) => {
	console.log(req.url);
	next();
};
app.use(logger);
```

## 3. Modular Routing

Use `express.Router()` to organize routes by feature/domain:

```js
const router = require("express").Router();
router.get("/profile", userProfileHandler);
app.use("/users", router);
```

## 4. Best Practices

-   Keep routes and middleware modular
-   Use middleware for cross-cutting concerns (logging, auth, validation)
-   Place error-handling middleware last

See also: [express-error-handling.md] for error middleware.
