# Express.js Routing and Middleware

Routing and middleware are the core concepts that make Express.js powerful and flexible. Understanding how to effectively use them is essential for building well-structured web applications.

[[Node.js Express.js Roadmap|← Back to Node.js Roadmap]]

## Routing System

**Problem:** How do we organize request handlers in a maintainable way as applications grow in complexity?

**Theory:** Express routing provides a structured way to map HTTP methods and URL paths to specific handler functions. The routing system allows for modular organization of endpoints based on resources, features, or domains.

### Route Methods and Paths

```javascript
// HTTP method-based routing
app.get("/products", listProducts); // GET request
app.post("/products", createProduct); // POST request
app.put("/products/:id", updateProduct); // PUT request
app.delete("/products/:id", deleteProduct); // DELETE request

// Path patterns
app.get("/users/:userId", getUserProfile); // Named parameter
app.get(/\/files\/(.+)/, serveFile); // Regular expression
app.all("/api/*", authenticate); // Wildcard matching
```

**When to use different path types:**

-   **String paths:** For most routes with fixed segments
-   **Named parameters:** When you need to capture variable parts of the URL
-   **Regular expressions:** For complex pattern matching requirements
-   **Wildcards:** For applying middleware to groups of routes

### Route Handlers

**Problem:** How do we process requests through multiple functions that may have different responsibilities?

**Theory:** Express allows multiple handler functions (middleware) for a single route, enabling separation of concerns like authentication, validation, and business logic.

```javascript
// Multiple handlers for a single route
app.get(
	"/dashboard",
	authenticate, // Check if user is logged in
	authorize("admin"), // Check if user has admin role
	loadDashboardData, // Retrieve necessary data
	renderDashboard // Send response to client
);

// Conditional next() routing
app.get("/users/:id", (req, res, next) => {
	if (req.params.id === "me") {
		// Special case handling
		return res.redirect(`/users/${req.user.id}`);
	}
	// Regular case - continue to next handler
	next();
});
```

**Best practices:**

-   Keep route handlers focused on a single responsibility
-   Use middleware chains for cross-cutting concerns
-   Consider extracting complex logic to controller functions

## Express Router

**Problem:** How do we organize routes for large applications with many endpoints?

**Theory:** Express Router enables modular, mountable route handlers that can be organized by resource, feature, or domain and attached to the main application.

**When to use:** When your application has many routes that can be logically grouped, or when you want to create reusable route modules.

```javascript
// users.js - Router module
const router = express.Router();

// Router-specific middleware
router.use(trackUserActivity);

// Define routes relative to mount point
router.get("/", listUsers);
router.post("/", createUser);
router.get("/:id", getUser);
router.put("/:id", updateUser);
router.delete("/:id", deleteUser);

module.exports = router;

// app.js - Main application
const userRoutes = require("./routes/users");
const productRoutes = require("./routes/products");

// Mount routers at specific paths
app.use("/api/v1/users", userRoutes);
app.use("/api/v1/products", productRoutes);
```

**Benefits:**

-   Modular organization by resource or feature
-   Encapsulated middleware specific to routes
-   Reusable route modules across applications
-   Cleaner main application file

## Middleware Architecture

**Problem:** How do we implement cross-cutting concerns and process requests through a pipeline of operations?

**Theory:** Middleware functions form a pipeline where each function can access and modify the request/response objects, end the request cycle, or pass control to the next middleware.

### Middleware Flow

```javascript
// Middleware execution flow
app.use((req, res, next) => {
	console.log("Middleware 1 - Start");
	next();
	console.log("Middleware 1 - After next()"); // Executes after route handler
});

app.use((req, res, next) => {
	console.log("Middleware 2 - Start");
	next();
	console.log("Middleware 2 - After next()");
});

app.get("/", (req, res) => {
	console.log("Route handler");
	res.send("Response");
});

// Console output for GET /
// Middleware 1 - Start
// Middleware 2 - Start
// Route handler
// Middleware 2 - After next()
// Middleware 1 - After next()
```

### Middleware Categories

**1. Application-Level Middleware**

```javascript
// Runs for every request
app.use((req, res, next) => {
	req.requestTime = Date.now();
	next();
});

// Runs only for specific paths
app.use("/api", apiKeyValidator);
```

**2. Router-Level Middleware**

```javascript
const router = express.Router();
router.use(resourceLogger);
router.get("/:id", getResource);
```

**3. Error-Handling Middleware**

```javascript
// Must have 4 parameters to be recognized as error handler
app.use((err, req, res, next) => {
	console.error(err.stack);
	res.status(err.statusCode || 500).json({ error: err.message });
});
```

**4. Built-in Middleware**

```javascript
// Parse JSON request bodies
app.use(express.json());

// Parse URL-encoded request bodies
app.use(express.urlencoded({ extended: true }));

// Serve static files
app.use(express.static("public"));
```

**5. Third-Party Middleware**

```javascript
const morgan = require("morgan"); // HTTP request logger
const helmet = require("helmet"); // Security headers
const compression = require("compression"); // Response compression

app.use(morgan("dev"));
app.use(helmet());
app.use(compression());
```

## Middleware Design Patterns

**Problem:** How do we implement common web application requirements using middleware?

**Theory:** Middleware can be composed and configured to implement various patterns for authentication, validation, error handling, and more.

### Authentication Middleware

```javascript
function authenticate(req, res, next) {
	const token = req.headers.authorization?.split(" ")[1];

	if (!token) {
		return res.status(401).json({ message: "Authentication required" });
	}

	try {
		const decoded = jwt.verify(token, process.env.JWT_SECRET);
		req.user = decoded; // Attach user info to request
		next(); // Proceed to the next middleware/route handler
	} catch (error) {
		res.status(401).json({ message: "Invalid token" });
	}
}

// Use on specific routes
app.get("/protected", authenticate, (req, res) => {
	res.json({ message: "Protected data", user: req.user });
});
```

### Request Validation

```javascript
const { body, validationResult } = require("express-validator");

app.post(
	"/users",
	// Validation middleware
	[
		body("email").isEmail().normalizeEmail(),
		body("password").isLength({ min: 8 }),
		body("name").not().isEmpty().trim(),
	],
	// Handle validation results
	(req, res, next) => {
		const errors = validationResult(req);
		if (!errors.isEmpty()) {
			return res.status(400).json({ errors: errors.array() });
		}
		next();
	},
	// Process valid request
	createUser
);
```

### Rate Limiting

```javascript
const rateLimit = require("express-rate-limit");

// Create a limiter
const apiLimiter = rateLimit({
	windowMs: 15 * 60 * 1000, // 15 minutes
	max: 100, // Limit each IP to 100 requests per windowMs
	message: "Too many requests, please try again after 15 minutes",
});

// Apply to all API routes
app.use("/api/", apiLimiter);
```

## Best Practices

**1. Middleware Organization**

-   Organize middleware by functionality (auth, logging, etc.)
-   Apply middleware in the correct order (parsing before validation, etc.)
-   Use middleware composition for reusable combinations

**2. Error Handling**

-   Always use a centralized error handler
-   Properly propagate errors using `next(error)`
-   Use custom error classes for different error types

**3. Route Organization**

-   Group routes by resource or feature
-   Use versioning for API routes
-   Keep route handlers thin by moving business logic to services

**4. Performance Considerations**

-   Place more frequently used routes and middleware earlier
-   Use efficient middleware only where needed
-   Consider conditional middleware application

Understanding these routing and middleware patterns is essential for building maintainable, secure, and efficient Express applications. The middleware architecture provides the flexibility to solve a wide range of web application requirements while keeping code modular and focused.
