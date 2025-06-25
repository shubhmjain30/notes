# Express.js Routing and Middleware

Routing and middleware are the core concepts that make Express.js powerful and flexible. Understanding how to effectively use them is essential for building well-structured web applications.

[[Node.js Express.js Roadmap|← Back to Node.js Roadmap]]

## Advanced Routing

### Route Methods

Express provides methods that correspond to HTTP methods. The most commonly used are:

```javascript
// Basic HTTP method routes
app.get("/users", (req, res) => {
	/* Handle GET request */
});
app.post("/users", (req, res) => {
	/* Handle POST request */
});
app.put("/users/:id", (req, res) => {
	/* Handle PUT request */
});
app.delete("/users/:id", (req, res) => {
	/* Handle DELETE request */
});
app.patch("/users/:id", (req, res) => {
	/* Handle PATCH request */
});

// Special method for handling all HTTP methods
app.all("/api/*", (req, res, next) => {
	console.log("Accessing the API...");
	next(); // Pass control to the next handler
});
```

### Route Paths

Route paths can be strings, string patterns, or regular expressions:

```javascript
// Simple string path
app.get("/users", (req, res) => {
	/* ... */
});

// String pattern with route parameters
app.get("/users/:userId/books/:bookId", (req, res) => {
	console.log(req.params.userId, req.params.bookId);
});

// Regular expression path
// This will match paths that start with /user/ followed by one or more digits
app.get(/\/user\/(\d+)/, (req, res) => {
	console.log(req.params[0]); // The captured value from the regex
});
```

### Route Handlers

You can provide multiple callback functions that behave like middleware:

```javascript
// Multiple callback functions
app.get(
	"/users/:id",
	(req, res, next) => {
		// First middleware - validate or load data
		console.log("Validating user ID...");
		if (isNaN(req.params.id)) {
			return res.status(400).send("Invalid user ID");
		}
		next(); // Pass control to the next handler
	},
	(req, res) => {
		// Final handler - send response
		res.send(`User profile for ID ${req.params.id}`);
	}
);

// Array of callback functions
const validateUser = (req, res, next) => {
	// Validation logic
	next();
};

const getUser = (req, res) => {
	// Get user logic
	res.send("User data");
};

app.get("/users/:id", [validateUser, getUser]);
```

## Express Router

For larger applications, the Express Router is essential for creating modular, mountable route handlers:

```javascript
// userRoutes.js
const express = require("express");
const router = express.Router();

// Middleware specific to this router
router.use((req, res, next) => {
	console.log("Time:", Date.now());
	next();
});

// Define routes on the router
router.get("/", (req, res) => {
	res.send("All users");
});

router.get("/:id", (req, res) => {
	res.send(`User with ID ${req.params.id}`);
});

router.post("/", (req, res) => {
	res.status(201).send("User created");
});

module.exports = router;
```

```javascript
// app.js
const express = require("express");
const app = express();
const userRoutes = require("./userRoutes");
const productRoutes = require("./productRoutes");

// Mount the routers at specific paths
app.use("/api/users", userRoutes);
app.use("/api/products", productRoutes);

app.listen(3000);
```

This approach allows you to:

-   Organize routes by resource or feature
-   Apply middleware specific to certain route groups
-   Create reusable route modules

## Middleware Deep Dive

Middleware functions are the backbone of Express applications. They have access to the request object, response object, and the next middleware function in the cycle.

### Middleware Types

#### Application-Level Middleware

```javascript
const express = require("express");
const app = express();

// Middleware with no mount path (runs for every request)
app.use((req, res, next) => {
	console.log("Request Time:", Date.now());
	next();
});

// Middleware with a mount path (runs only for specific paths)
app.use("/user/:id", (req, res, next) => {
	console.log("Request Type:", req.method);
	next();
});

// Middleware that only runs for specific HTTP methods
app.get(
	"/user/:id",
	(req, res, next) => {
		// If user ID is 0, skip to the next route
		if (req.params.id === "0") return next("route");
		// Otherwise pass control to the next middleware in this stack
		next();
	},
	(req, res, next) => {
		// This will be skipped for user ID 0
		res.send("Regular User");
	}
);

// This route will handle user ID 0
app.get("/user/:id", (req, res) => {
	res.send("Special User");
});
```

#### Router-Level Middleware

Router-level middleware works the same way as application-level middleware, except it is bound to an instance of `express.Router()`.

```javascript
const router = express.Router();

router.use((req, res, next) => {
	console.log("Router-specific middleware");
	next();
});

router.get("/users", (req, res, next) => {
	// ...
});
```

#### Error-Handling Middleware

Error-handling middleware has four arguments instead of three (err, req, res, next):

```javascript
app.use((err, req, res, next) => {
	console.error(err.stack);
	res.status(500).send("Something broke!");
});
```

#### Built-in Middleware

Express has several built-in middleware functions:

```javascript
// Parse JSON bodies
app.use(express.json());

// Parse URL-encoded bodies
app.use(express.urlencoded({ extended: true }));

// Serve static files
app.use(express.static("public"));
```

#### Third-Party Middleware

Many third-party middleware packages are available for common tasks:

```javascript
const morgan = require("morgan"); // HTTP request logger
const helmet = require("helmet"); // Security headers
const compression = require("compression"); // Compress responses
const cors = require("cors"); // Enable CORS

app.use(morgan("dev")); // Log requests
app.use(helmet()); // Set security headers
app.use(compression()); // Compress all responses
app.use(cors()); // Enable CORS for all requests
```

### Creating Custom Middleware

Custom middleware allows you to add specific functionality to your application:

```javascript
// Authentication middleware
const authenticate = (req, res, next) => {
	const token = req.headers.authorization;

	if (!token) {
		return res.status(401).json({ message: "Authentication required" });
	}

	try {
		// Verify token (simplified example)
		const user = verifyToken(token);
		req.user = user; // Attach user to request object
		next(); // Proceed to the next middleware/route handler
	} catch (error) {
		res.status(401).json({ message: "Invalid token" });
	}
};

// Usage
app.get("/protected", authenticate, (req, res) => {
	res.json({ message: "Protected data", user: req.user });
});
```

### Middleware Execution Order

The order in which middleware is defined is crucial - they execute in the sequence they are added:

```javascript
// This middleware will run first
app.use((req, res, next) => {
	console.log("First middleware");
	next();
});

// This middleware will run second
app.use((req, res, next) => {
	console.log("Second middleware");
	next();
});

// Route handler will run last
app.get("/", (req, res) => {
	res.send("Response");
});
```

### Middleware Chaining

You can chain middleware to create a processing pipeline:

```javascript
const validateInput = (req, res, next) => {
	if (!req.body.name) {
		return res.status(400).json({ error: "Name is required" });
	}
	next();
};

const sanitizeInput = (req, res, next) => {
	req.body.name = req.body.name.trim();
	next();
};

const logRequest = (req, res, next) => {
	console.log(`Processing request for ${req.body.name}`);
	next();
};

// Use the middleware chain
app.post(
	"/users",
	express.json(),
	validateInput,
	sanitizeInput,
	logRequest,
	(req, res) => {
		// Create user
		res.status(201).json({ message: "User created" });
	}
);
```

## Practical Examples

### API Rate Limiting

```javascript
const rateLimit = require("express-rate-limit");

// Create a limiter
const apiLimiter = rateLimit({
	windowMs: 15 * 60 * 1000, // 15 minutes
	max: 100, // Limit each IP to 100 requests per windowMs
	message:
		"Too many requests from this IP, please try again after 15 minutes",
});

// Apply to all requests to /api/
app.use("/api/", apiLimiter);
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
	// Process the request if validation passed
	(req, res) => {
		// Create user
		res.status(201).json({ message: "User created successfully" });
	}
);
```

Mastering routing and middleware is key to building efficient, maintainable Express applications. These concepts provide the structure and flexibility needed for everything from simple APIs to complex web applications.
