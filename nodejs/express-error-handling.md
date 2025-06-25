# Error Handling in Express.js

Proper error handling is essential for building robust and reliable Express.js applications. This guide explores comprehensive strategies for handling errors in Express, from basic error middleware to advanced error management techniques.

[[Node.js Express.js Roadmap|← Back to Node.js Roadmap]]

## Understanding Express Error Handling

Express comes with a built-in error handler that takes care of any errors that might occur in your application. However, for production applications, you'll want to implement your own error handling logic.

### The Error-Handling Middleware

Error-handling middleware is defined with **four** parameters instead of the usual three (err, req, res, next):

```javascript
app.use((err, req, res, next) => {
	// Error handling logic
});
```

Express recognizes this pattern and treats it as an error handler. When an error occurs in any middleware or route handler, Express will skip all remaining middleware and route handlers and go directly to the error-handling middleware.

## Handling Synchronous Errors

Synchronous errors are automatically caught by Express:

```javascript
app.get("/sync-error", (req, res) => {
	// This error will be caught automatically
	throw new Error("Something went wrong!");
});
```

## Handling Asynchronous Errors

Asynchronous errors require explicit handling:

### Using Next

```javascript
app.get("/async-error", (req, res, next) => {
	fs.readFile("/file-does-not-exist", (err, data) => {
		if (err) {
			// Pass the error to Express
			return next(err);
		}
		res.send(data);
	});
});
```

### Using Try/Catch with Async/Await

```javascript
app.get("/async-await-error", async (req, res, next) => {
	try {
		const data = await fs.promises.readFile("/file-does-not-exist");
		res.send(data);
	} catch (err) {
		// Pass the caught error to Express
		next(err);
	}
});
```

### Using Express 5 (upcoming)

Express 5 (currently in alpha) will automatically catch errors in async functions, eliminating the need for try/catch blocks:

```javascript
// In Express 5, this will work without try/catch
app.get("/express5-async", async (req, res) => {
	const data = await fs.promises.readFile("/file-does-not-exist");
	res.send(data);
});
```

## Creating a Centralized Error Handler

A well-structured error handling system typically includes:

1. Custom error classes
2. A centralized error-handling middleware
3. Error handling utilities

### Custom Error Classes

```javascript
// utils/appError.js
class AppError extends Error {
	constructor(message, statusCode) {
		super(message);

		this.statusCode = statusCode;
		this.status = `${statusCode}`.startsWith("4") ? "fail" : "error";
		this.isOperational = true;

		Error.captureStackTrace(this, this.constructor);
	}
}

module.exports = AppError;
```

### Centralized Error Handler

```javascript
// middleware/errorHandler.js
const AppError = require("../utils/appError");

// Development error handler - shows detailed error information
const sendErrorDev = (err, res) => {
	res.status(err.statusCode).json({
		status: err.status,
		error: err,
		message: err.message,
		stack: err.stack,
	});
};

// Production error handler - shows limited error information
const sendErrorProd = (err, res) => {
	// Operational, trusted error: send message to client
	if (err.isOperational) {
		res.status(err.statusCode).json({
			status: err.status,
			message: err.message,
		});
	}
	// Programming or unknown error: don't leak error details
	else {
		// 1) Log error
		console.error("ERROR 💥", err);

		// 2) Send generic message
		res.status(500).json({
			status: "error",
			message: "Something went very wrong!",
		});
	}
};

// Handle specific error types
const handleCastErrorDB = (err) => {
	const message = `Invalid ${err.path}: ${err.value}`;
	return new AppError(message, 400);
};

const handleDuplicateFieldsDB = (err) => {
	const value = err.errmsg.match(/(["'])(\\?.)*?\1/)[0];
	const message = `Duplicate field value: ${value}. Please use another value!`;
	return new AppError(message, 400);
};

const handleValidationErrorDB = (err) => {
	const errors = Object.values(err.errors).map((el) => el.message);
	const message = `Invalid input data. ${errors.join(". ")}`;
	return new AppError(message, 400);
};

const handleJWTError = () =>
	new AppError("Invalid token. Please log in again!", 401);

const handleJWTExpiredError = () =>
	new AppError("Your token has expired! Please log in again.", 401);

// Main error handling middleware
module.exports = (err, req, res, next) => {
	err.statusCode = err.statusCode || 500;
	err.status = err.status || "error";

	if (process.env.NODE_ENV === "development") {
		sendErrorDev(err, res);
	} else if (process.env.NODE_ENV === "production") {
		let error = { ...err };
		error.message = err.message;

		// Handle specific error types
		if (error.name === "CastError") error = handleCastErrorDB(error);
		if (error.code === 11000) error = handleDuplicateFieldsDB(error);
		if (error.name === "ValidationError")
			error = handleValidationErrorDB(error);
		if (error.name === "JsonWebTokenError") error = handleJWTError();
		if (error.name === "TokenExpiredError") error = handleJWTExpiredError();

		sendErrorProd(error, res);
	}
};
```

### Using the Error Handler

```javascript
// app.js
const express = require("express");
const AppError = require("./utils/appError");
const globalErrorHandler = require("./middleware/errorHandler");

const app = express();

// Routes
app.get("/api/items/:id", (req, res, next) => {
	if (req.params.id === "invalid") {
		return next(new AppError("Item not found", 404));
	}
	res.json({ id: req.params.id, name: "Sample Item" });
});

// 404 handler for undefined routes
app.all("*", (req, res, next) => {
	next(new AppError(`Can't find ${req.originalUrl} on this server!`, 404));
});

// Global error handler - must be the last middleware
app.use(globalErrorHandler);

app.listen(3000, () => {
	console.log("Server running on port 3000");
});
```

## Handling Errors in Async Route Handlers

To avoid repetitive try/catch blocks, you can create a wrapper function:

```javascript
// utils/catchAsync.js
module.exports = (fn) => {
	return (req, res, next) => {
		fn(req, res, next).catch(next);
	};
};
```

```javascript
// routes/userRoutes.js
const express = require("express");
const router = express.Router();
const userController = require("../controllers/userController");
const catchAsync = require("../utils/catchAsync");

router.get("/", catchAsync(userController.getAllUsers));
router.get("/:id", catchAsync(userController.getUser));
router.post("/", catchAsync(userController.createUser));
// ...

module.exports = router;
```

```javascript
// controllers/userController.js
const User = require("../models/User");
const AppError = require("../utils/appError");

exports.getAllUsers = async (req, res) => {
	const users = await User.find();

	res.status(200).json({
		status: "success",
		results: users.length,
		data: { users },
	});
};

exports.getUser = async (req, res, next) => {
	const user = await User.findById(req.params.id);

	if (!user) {
		return next(new AppError("No user found with that ID", 404));
	}

	res.status(200).json({
		status: "success",
		data: { user },
	});
};

// Other controller methods...
```

## Handling Uncaught Exceptions and Unhandled Rejections

For errors that occur outside of the Express request-response cycle:

```javascript
// server.js
const app = require("./app");

// Handle uncaught exceptions
process.on("uncaughtException", (err) => {
	console.error("UNCAUGHT EXCEPTION! 💥 Shutting down...");
	console.error(err.name, err.message);
	process.exit(1);
});

const server = app.listen(3000, () => {
	console.log("Server running on port 3000");
});

// Handle unhandled promise rejections
process.on("unhandledRejection", (err) => {
	console.error("UNHANDLED REJECTION! 💥 Shutting down...");
	console.error(err.name, err.message);
	server.close(() => {
		process.exit(1);
	});
});
```

## Validation Errors

For API input validation, use a library like express-validator:

```javascript
const { body, validationResult } = require("express-validator");

app.post(
	"/users",
	// Validation middleware
	[
		body("email").isEmail().withMessage("Please provide a valid email"),
		body("password")
			.isLength({ min: 8 })
			.withMessage("Password must be at least 8 characters long")
			.matches(/\d/)
			.withMessage("Password must contain a number"),
	],
	// Handle validation errors
	(req, res, next) => {
		const errors = validationResult(req);
		if (!errors.isEmpty()) {
			return res.status(400).json({
				status: "fail",
				errors: errors.array(),
			});
		}
		next();
	},
	// Process the request
	(req, res) => {
		// Create user...
		res.status(201).json({
			status: "success",
			message: "User created",
		});
	}
);
```

## Error Logging

In production environments, it's important to log errors for debugging:

```javascript
const winston = require("winston");

// Create a logger
const logger = winston.createLogger({
	level: "error",
	format: winston.format.combine(
		winston.format.timestamp(),
		winston.format.json()
	),
	transports: [
		new winston.transports.File({ filename: "error.log", level: "error" }),
		new winston.transports.Console(),
	],
});

// Use in error handler
const globalErrorHandler = (err, req, res, next) => {
	// Log the error
	logger.error({
		message: err.message,
		stack: err.stack,
		url: req.originalUrl,
		method: req.method,
		body: req.body,
		params: req.params,
		query: req.query,
	});

	// Rest of error handling logic...
};
```

## Best Practices for Error Handling

1. **Always use asynchronous error handling** for asynchronous code
2. **Create custom error classes** for different types of errors
3. **Centralize error handling** in a single middleware
4. **Differentiate between operational and programming errors**
5. **Log errors** appropriately but don't expose sensitive details to clients
6. **Set appropriate HTTP status codes** for different error types
7. **Handle uncaught exceptions and unhandled rejections** at the process level
8. **Validate input** and provide clear error messages for invalid data
9. **Use consistent error response format** across your API

By implementing these error handling strategies, you'll create more robust, maintainable, and user-friendly Express applications that can gracefully handle failures and provide meaningful feedback to users and developers alike.
