# Introduction to Express.js

Express.js is a minimal and flexible Node.js web application framework that provides a robust set of features for building web applications and APIs. It simplifies the server creation process that is already available in Node.js, making it easier to organize your application's functionality.

[[Node.js Express.js Roadmap|← Back to Node.js Roadmap]]

## Why Express.js?

**Problem:** Creating web servers with just Node.js core modules is verbose, requires manual routing, and lacks standardized patterns for common web development tasks.

**Theory:** Express provides a thin layer of fundamental web application features without obscuring Node.js features, offering:

-   Simplified routing
-   Middleware architecture for request processing
-   Easy integration with template engines
-   Standardized request and response handling

**When to use:** Express is ideal for building web applications, RESTful APIs, and microservices when you need more structure than raw Node.js but don't want the complexity of full-stack frameworks.

## Getting Started

```bash
# Initialize project and install Express
mkdir my-express-app && cd my-express-app
npm init -y
npm install express
```

```javascript
// Minimal Express application
const express = require("express");
const app = express();
const port = 3000;

app.get("/", (req, res) => {
	res.send("Hello World!");
});

app.listen(port, () => {
	console.log(`Server running at http://localhost:${port}`);
});
```

## Routing System

**Problem:** How do we map HTTP requests to specific handlers based on URL paths and HTTP methods?

**Theory:** Express routing determines how an application responds to client requests to specific endpoints (URI paths) and HTTP methods (GET, POST, etc.).

**Key concepts:**

-   Routes can be defined for any HTTP method
-   Routes can contain route parameters
-   Multiple handler functions can process a request (middleware)
-   Route modules can be mounted at different paths

```javascript
// Basic routing patterns
app.get("/users", (req, res) => {
	// Handle GET request to /users
	res.json(users);
});

app.post("/users", (req, res) => {
	// Handle POST request to /users
	res.status(201).send("User created");
});

// Route with parameters
app.get("/users/:id", (req, res) => {
	const userId = req.params.id;
	res.send(`User details for ${userId}`);
});

// Route with multiple handlers (middleware chain)
app.get("/protected", authenticateUser, authorizeUser, (req, res) =>
	res.send("Protected content")
);
```

## Middleware Architecture

**Problem:** How do we process requests through a series of functions that can modify the request/response objects or end the request cycle?

**Theory:** Middleware functions are functions that have access to the request object, response object, and the next middleware function. They can:

-   Execute any code
-   Modify the request and response objects
-   End the request-response cycle
-   Call the next middleware in the stack

**Types of middleware:**

1. Application-level middleware
2. Router-level middleware
3. Error-handling middleware
4. Built-in middleware
5. Third-party middleware

```javascript
// Middleware anatomy
function myMiddleware(req, res, next) {
	// Do something with request or response
	console.log(`${req.method} request to ${req.url}`);

	// Pass control to next middleware
	next();

	// Or end the cycle:
	// res.send("Response from middleware");
}

// Using middleware
app.use(myMiddleware); // Apply to all routes

app.use("/api", myMiddleware); // Apply to specific path

// Built-in middleware
app.use(express.json()); // Parse JSON request bodies
app.use(express.static("public")); // Serve static files
```

**Why it matters:** Middleware is the foundation of Express.js's flexibility. Understanding the middleware pattern is crucial for building maintainable Express applications.

## Request and Response Objects

**Problem:** How do we access request data and send appropriate responses in different formats?

**Theory:** Express enhances Node's native request and response objects with additional properties and methods for easier web application development.

```javascript
app.get("/api/items/:id", (req, res) => {
	// Request object properties
	const itemId = req.params.id; // URL parameters
	const query = req.query.filter; // Query string parameters
	const token = req.headers.authorization; // Headers
	const data = req.body; // Request body (with body-parser)

	// Response methods
	res.status(200); // Set status code
	res.set("Content-Type", "text/html"); // Set headers

	// Sending responses (each ends the request)
	res.send("Plain text or HTML"); // Auto-detects content type
	res.json({ item: "data" }); // Sends JSON with correct headers
	res.sendFile("/path/to/file.pdf"); // Sends a file
	res.render("template", { data }); // Renders a view template
	res.redirect("/new-url"); // HTTP redirect
});
```

## Error Handling

**Problem:** How do we handle errors consistently across an Express application?

**Theory:** Express provides a centralized error handling mechanism through special middleware functions with four parameters.

```javascript
// Synchronous error handling
app.get("/items/:id", (req, res, next) => {
	try {
		const item = getItemById(req.params.id);
		if (!item) {
			// Create and pass error to Express
			const error = new Error("Item not found");
			error.statusCode = 404;
			throw error;
		}
		res.json(item);
	} catch (err) {
		// Pass errors to Express error handler
		next(err);
	}
});

// Asynchronous error handling
app.get("/async-data", async (req, res, next) => {
	try {
		const data = await fetchDataAsync();
		res.json(data);
	} catch (err) {
		next(err);
	}
});

// Centralized error handler
app.use((err, req, res, next) => {
	const statusCode = err.statusCode || 500;
	res.status(statusCode).json({
		status: "error",
		message: err.message,
		stack: process.env.NODE_ENV === "development" ? err.stack : undefined,
	});
});
```

## Organizing Express Applications

**Problem:** How do we structure larger Express applications to maintain code quality and separation of concerns?

**Theory:** Express applications benefit from modular organization patterns that separate routes, controllers, middleware, and configuration.

**Common project structure:**

```
my-express-app/
├── app.js              # Application entry point
├── config/             # Configuration files
├── controllers/        # Route handlers
├── middleware/         # Custom middleware
├── models/             # Data models
├── routes/             # Route definitions
├── services/           # Business logic
├── utils/              # Utility functions
├── views/              # View templates
└── public/             # Static files
```

**Route organization example:**

```javascript
// routes/users.js
const express = require("express");
const router = express.Router();
const userController = require("../controllers/userController");

router.get("/", userController.getAllUsers);
router.post("/", userController.createUser);
router.get("/:id", userController.getUserById);
router.put("/:id", userController.updateUser);
router.delete("/:id", userController.deleteUser);

module.exports = router;

// app.js
const userRoutes = require("./routes/users");
app.use("/api/users", userRoutes);
```

Understanding Express.js fundamentals provides the foundation for building efficient, maintainable web applications with Node.js. As your applications grow in complexity, you'll leverage more advanced Express features and patterns.
