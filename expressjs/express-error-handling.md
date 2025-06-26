# Error Handling in Express.js

Robust error handling is the difference between a development prototype and a production-ready application. Express provides multiple mechanisms for catching and handling errors gracefully.

## Understanding Express Error Handling

**Problem:** Applications encounter various types of errors - validation failures, database connection issues, authentication problems, and unexpected runtime errors. Without proper handling, these can crash the server or expose sensitive information.

**Theory:** Express uses a combination of automatic error catching for synchronous code and manual error propagation for asynchronous code, culminating in centralized error-handling middleware.

## Error-Handling Middleware

Error-handling middleware is special middleware with **four parameters** instead of three:

```js
// Standard middleware: (req, res, next)
// Error middleware: (err, req, res, next)
```

Express recognizes this signature and only calls this middleware when an error occurs.

### Basic Error Handler

```js
// Basic error handling middleware
app.use((err, req, res, next) => {
	console.error(err.stack);

	res.status(err.status || 500).json({
		success: false,
		message: err.message || "Internal Server Error",
		...(process.env.NODE_ENV === "development" && { stack: err.stack }),
	});
});
```

### Advanced Error Handler with Custom Error Classes

```js
// Custom error classes for different error types
class AppError extends Error {
	constructor(message, statusCode) {
		super(message);
		this.statusCode = statusCode;
		this.status = `${statusCode}`.startsWith("4") ? "fail" : "error";
		this.isOperational = true;

		Error.captureStackTrace(this, this.constructor);
	}
}

class ValidationError extends AppError {
	constructor(message) {
		super(message, 400);
		this.name = "ValidationError";
	}
}

class NotFoundError extends AppError {
	constructor(resource = "Resource") {
		super(`${resource} not found`, 404);
		this.name = "NotFoundError";
	}
}

// Comprehensive error handling middleware
const globalErrorHandler = (err, req, res, next) => {
	let error = { ...err };
	error.message = err.message;

	// Log error for debugging
	console.error(err);

	// Mongoose bad ObjectId
	if (err.name === "CastError") {
		const message = "Resource not found";
		error = new NotFoundError(message);
	}

	// Mongoose duplicate key
	if (err.code === 11000) {
		const value = err.errmsg.match(/(["'])(\\?.)*?\1/)[0];
		const message = `Duplicate field value: ${value}. Please use another value`;
		error = new ValidationError(message);
	}

	// Mongoose validation error
	if (err.name === "ValidationError") {
		const errors = Object.values(err.errors).map((val) => val.message);
		const message = `Invalid input data. ${errors.join(". ")}`;
		error = new ValidationError(message);
	}

	res.status(error.statusCode || 500).json({
		success: false,
		error: {
			message: error.message || "Server Error",
			...(process.env.NODE_ENV === "development" && { stack: err.stack }),
		},
	});
};

module.exports = {
	AppError,
	ValidationError,
	NotFoundError,
	globalErrorHandler,
};
```

## Synchronous vs. Asynchronous Errors

### Synchronous Errors (Automatically Caught)

```js
app.get("/sync-error", (req, res) => {
	// This will be automatically caught by Express
	throw new Error("Synchronous error occurred!");
});

app.get("/json-parse-error", (req, res) => {
	// This will also be caught automatically
	const malformedJson = '{"invalid": json}';
	JSON.parse(malformedJson); // Throws SyntaxError
});
```

### Asynchronous Errors (Manual Handling Required)

```js
const { AppError } = require("./middleware/errorHandler");

// Wrong way - error won't be caught
app.get("/async-error-wrong", (req, res) => {
	setTimeout(() => {
		throw new Error("This error will crash the server!");
	}, 100);
});

// Correct way - pass error to next()
app.get("/async-error-correct", (req, res, next) => {
	setTimeout(() => {
		try {
			throw new Error("Async error handled properly");
		} catch (error) {
			next(error); // Pass to error middleware
		}
	}, 100);
});

// With Promises
app.get("/promise-error", (req, res, next) => {
	Promise.reject(new Error("Promise rejected")).catch(next); // Automatically passes error to next()
});

// With async/await
app.get("/async-await-error", async (req, res, next) => {
	try {
		await someAsyncOperation();
		res.json({ success: true });
	} catch (error) {
		next(error);
	}
});
```

### Async Error Wrapper Utility

```js
// Utility to automatically catch async errors
const asyncHandler = (fn) => (req, res, next) => {
	Promise.resolve(fn(req, res, next)).catch(next);
};

// Usage
app.get(
	"/users/:id",
	asyncHandler(async (req, res) => {
		const user = await User.findById(req.params.id);
		if (!user) {
			throw new NotFoundError("User");
		}
		res.json({ user });
	})
);
```

## Practical Error Handling Examples

### Database Connection Errors

```js
const mongoose = require("mongoose");

mongoose
	.connect(process.env.MONGODB_URI)
	.then(() => console.log("Database connected"))
	.catch((err) => {
		console.error("Database connection failed:", err.message);
		process.exit(1);
	});

// Handle database disconnection
mongoose.connection.on("disconnected", () => {
	console.log("Database disconnected");
});

mongoose.connection.on("error", (err) => {
	console.error("Database error:", err);
});
```

### Route-Level Error Handling

```js
const {
	ValidationError,
	NotFoundError,
} = require("../middleware/errorHandler");

// User controller with proper error handling
class UserController {
	static async createUser(req, res, next) {
		try {
			const { email, username, password } = req.body;

			// Input validation
			if (!email || !username || !password) {
				throw new ValidationError(
					"Email, username, and password are required"
				);
			}

			// Check if user already exists
			const existingUser = await User.findOne({
				$or: [{ email }, { username }],
			});
			if (existingUser) {
				throw new ValidationError(
					"User with this email or username already exists"
				);
			}

			const user = await User.create({ email, username, password });

			res.status(201).json({
				success: true,
				data: {
					user: {
						id: user._id,
						email: user.email,
						username: user.username,
					},
				},
			});
		} catch (error) {
			next(error);
		}
	}

	static async getUserById(req, res, next) {
		try {
			const user = await User.findById(req.params.id);

			if (!user) {
				throw new NotFoundError("User");
			}

			res.json({
				success: true,
				data: { user },
			});
		} catch (error) {
			next(error);
		}
	}
}
```

### 404 Handler for Unknown Routes

```js
// Handle 404 for unknown routes (place before error handler)
app.all("*", (req, res, next) => {
	const err = new NotFoundError(
		`Can't find ${req.originalUrl} on this server`
	);
	next(err);
});

// Global error handler (must be last)
app.use(globalErrorHandler);
```

## Process-Level Error Handling

For ultimate robustness, handle uncaught exceptions and unhandled rejections:

```js
// Handle uncaught exceptions
process.on("uncaughtException", (err) => {
	console.log("UNCAUGHT EXCEPTION! 💥 Shutting down...");
	console.log(err.name, err.message);
	process.exit(1);
});

// Start server
const server = app.listen(PORT, () => {
	console.log(`App running on port ${PORT}...`);
});

// Handle unhandled promise rejections
process.on("unhandledRejection", (err) => {
	console.log("UNHANDLED REJECTION! 💥 Shutting down...");
	console.log(err.name, err.message);
	server.close(() => {
		process.exit(1);
	});
});

// Graceful shutdown on SIGTERM
process.on("SIGTERM", () => {
	console.log("👋 SIGTERM RECEIVED. Shutting down gracefully");
	server.close(() => {
		console.log("💥 Process terminated!");
	});
});
```

## Error Logging and Monitoring

### Using Winston for Logging

```js
const winston = require("winston");

const logger = winston.createLogger({
	level: "info",
	format: winston.format.combine(
		winston.format.timestamp(),
		winston.format.errors({ stack: true }),
		winston.format.json()
	),
	defaultMeta: { service: "express-app" },
	transports: [
		new winston.transports.File({
			filename: "logs/error.log",
			level: "error",
		}),
		new winston.transports.File({ filename: "logs/combined.log" }),
	],
});

if (process.env.NODE_ENV !== "production") {
	logger.add(
		new winston.transports.Console({
			format: winston.format.simple(),
		})
	);
}

// Enhanced error handler with logging
const globalErrorHandler = (err, req, res, next) => {
	// Log error details
	logger.error("Express Error:", {
		error: err.message,
		stack: err.stack,
		url: req.url,
		method: req.method,
		ip: req.ip,
		userAgent: req.get("User-Agent"),
	});

	// Send response
	res.status(err.statusCode || 500).json({
		success: false,
		error: {
			message: err.message || "Internal Server Error",
		},
	});
};
```

## Best Practices Summary

1. **Always use error-handling middleware** with four parameters
2. **Handle async errors explicitly** using try-catch or error wrappers
3. **Create custom error classes** for different error types
4. **Log errors appropriately** but never expose sensitive data
5. **Use appropriate HTTP status codes** (400 for client errors, 500 for server errors)
6. **Implement graceful shutdown** for process-level errors
7. **Test error scenarios** to ensure they're handled correctly
8. **Monitor errors in production** using tools like Sentry or LogRocket

Proper error handling makes your Express.js applications robust, maintainable, and production-ready.

-   Handle uncaught exceptions and rejections at the process level
