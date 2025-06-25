# Introduction to Express.js

Express.js is a minimal and flexible Node.js web application framework that provides a robust set of features for building web applications and APIs. It simplifies the server creation process that is already available in Node.js, making it easier to organize your application's functionality.

[[Node.js Express.js Roadmap|← Back to Node.js Roadmap]]

## Getting Started with Express

### Installation

To begin using Express, you need to initialize a Node.js project and install Express:

```bash
# Create a new directory for your project
mkdir my-express-app
cd my-express-app

# Initialize a new Node.js project
npm init -y

# Install Express
npm install express
```

### Creating Your First Express Application

```javascript
// app.js
const express = require("express");
const app = express();
const port = 3000;

// Define a route for the homepage
app.get("/", (req, res) => {
	res.send("Hello World from Express!");
});

// Start the server
app.listen(port, () => {
	console.log(`Example app listening at http://localhost:${port}`);
});
```

To run your application:

```bash
node app.js
```

## Basic Routing

Routing refers to determining how an application responds to a client request to a specific endpoint, which is a URI (or path) and a specific HTTP request method (GET, POST, etc.).

```javascript
// Basic route structure
app.METHOD(PATH, HANDLER);

// Examples
app.get("/about", (req, res) => {
	res.send("About page");
});

app.post("/submit-data", (req, res) => {
	res.send("Data submitted");
});

app.put("/update/:id", (req, res) => {
	res.send(`Updating item ${req.params.id}`);
});

app.delete("/delete/:id", (req, res) => {
	res.send(`Deleting item ${req.params.id}`);
});
```

## Serving Static Files

Express can serve static files like images, CSS, and JavaScript files. Use the `express.static` built-in middleware function:

```javascript
// Serve static files from the 'public' directory
app.use(express.static("public"));

// Multiple static directories
app.use(express.static("public"));
app.use(express.static("files"));

// Virtual path prefix
app.use("/static", express.static("public"));
// Access files at http://localhost:3000/static/images/logo.png
```

## Request and Response Objects

Express enhances the Node.js request (`req`) and response (`res`) objects with additional methods and properties:

```javascript
app.get("/users/:userId", (req, res) => {
	// Request object properties and methods
	console.log("Request URL:", req.url);
	console.log("Request method:", req.method);
	console.log("Request headers:", req.headers);
	console.log("Request path parameters:", req.params); // { userId: '123' }
	console.log("Request query parameters:", req.query); // ?sort=asc&page=2

	// Response object methods
	res.status(200); // Set HTTP status code
	res.set("Content-Type", "text/html"); // Set response header

	// Sending responses
	res.send("Plain text response");
	// or
	res.json({ user: "John", id: req.params.userId });
	// or
	res.sendFile("/path/to/file.html");
	// or
	res.render("template", { user: "John" }); // With a template engine
});
```

## Middleware

Middleware functions are functions that have access to the request object, the response object, and the next middleware function in the application's request-response cycle.

```javascript
// Custom middleware function
const loggerMiddleware = (req, res, next) => {
	console.log(`${new Date().toISOString()} - ${req.method} ${req.url}`);
	next(); // Call next to pass control to the next middleware
};

// Apply middleware to all routes
app.use(loggerMiddleware);

// Apply middleware to specific routes
app.use("/api", loggerMiddleware);

// Built-in middleware
app.use(express.json()); // Parse JSON request bodies
app.use(express.urlencoded({ extended: true })); // Parse URL-encoded request bodies
```

## Route Parameters

Route parameters are named URL segments used to capture values at specific positions in the URL:

```javascript
app.get("/users/:userId/books/:bookId", (req, res) => {
	// Access parameters via req.params
	console.log("User ID:", req.params.userId);
	console.log("Book ID:", req.params.bookId);

	res.send(
		`Fetching book ${req.params.bookId} for user ${req.params.userId}`
	);
});
```

## Query Parameters

Query parameters are passed in the URL after a question mark:

```javascript
// URL: /search?q=express&category=framework
app.get("/search", (req, res) => {
	const query = req.query.q;
	const category = req.query.category;

	res.send(`Searching for "${query}" in category "${category}"`);
});
```

## Error Handling

Express has a built-in error handler that takes care of any errors that might be encountered in the app. The default error handler is added at the end of the middleware function stack.

```javascript
// Custom error handling middleware
app.use((err, req, res, next) => {
	console.error(err.stack);
	res.status(500).send("Something broke!");
});

// Route with error handling
app.get("/error-demo", (req, res, next) => {
	try {
		// Some operation that might throw an error
		throw new Error("Demonstration error");
	} catch (error) {
		next(error); // Pass error to Express
	}
});
```

## Setting Up a Project Structure

For larger applications, it's best to organize your code into a structured format:

```
my-express-app/
├── app.js                  # Entry point
├── package.json            # Project metadata and dependencies
├── public/                 # Static files (CSS, images, client-side JS)
│   ├── css/
│   ├── js/
│   └── images/
├── routes/                 # Route handlers
│   ├── index.js
│   └── users.js
├── controllers/            # Route controller logic
│   └── userController.js
├── models/                 # Data models
│   └── User.js
├── views/                  # Template files
│   ├── layouts/
│   └── partials/
├── middleware/             # Custom middleware
│   └── auth.js
└── config/                 # Configuration files
    └── database.js
```

This modular structure helps in maintaining a clean and organized codebase as your application grows.

Express.js provides a minimalist approach to building web applications, giving you the freedom to structure your application as you see fit while providing the essential tools needed for web development.
