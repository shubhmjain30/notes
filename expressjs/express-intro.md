# Express.js: Introduction & Philosophy

Express.js is a minimal, unopinionated web framework for Node.js that simplifies building web applications and APIs. Built on top of Node.js's `http` module, Express provides essential web features while maintaining the flexibility to structure applications as needed.

## The Express Philosophy

**Problem:** Building web servers with just Node.js core modules is verbose and repetitive. Simple tasks like routing, parsing request bodies, and handling static files require significant boilerplate code.

**Solution:** Express provides a thin layer of fundamental web application features, without obscuring Node.js features. It follows the "unopinionated" philosophy - it doesn't force you into a specific project structure or architectural pattern.

**Core Principles:**

-   **Minimal:** Only provides essential features, leaving architectural decisions to developers
-   **Flexible:** Can be used for single-page apps, APIs, traditional web apps, or microservices
-   **Extensible:** Rich ecosystem of middleware for additional functionality

## Why Choose Express.js?

### Compared to Raw Node.js

```js
// Raw Node.js HTTP server
const http = require("http");
const url = require("url");

const server = http.createServer((req, res) => {
	const parsedUrl = url.parse(req.url, true);

	if (req.method === "GET" && parsedUrl.pathname === "/") {
		res.writeHead(200, { "Content-Type": "text/html" });
		res.end("<h1>Hello World!</h1>");
	} else if (req.method === "GET" && parsedUrl.pathname === "/api/users") {
		res.writeHead(200, { "Content-Type": "application/json" });
		res.end(JSON.stringify({ users: [] }));
	} else {
		res.writeHead(404, { "Content-Type": "text/plain" });
		res.end("Not Found");
	}
});

server.listen(3000);
```

```js
// Express equivalent
const express = require("express");
const app = express();

app.get("/", (req, res) => {
	res.send("<h1>Hello World!</h1>");
});

app.get("/api/users", (req, res) => {
	res.json({ users: [] });
});

app.listen(3000);
```

### Key Benefits

1. **Simplified Routing:** Declarative route definitions
2. **Middleware Architecture:** Modular request processing
3. **Built-in Features:** JSON parsing, static file serving, cookie handling
4. **Template Engine Support:** EJS, Pug, Handlebars integration
5. **Robust Error Handling:** Centralized error management

## When to Use Express.js

**Ideal For:**

-   RESTful APIs and microservices
-   Traditional web applications with server-side rendering
-   Real-time applications (with Socket.IO)
-   Prototyping and MVPs

**Consider Alternatives When:**

-   You need a full-stack framework with conventions (Next.js, Nuxt.js)
-   Building GraphQL APIs (Apollo Server might be better)
-   Maximum performance is critical (Fastify might be faster)

## Getting Started: Your First Express Application

### Installation

```bash
# Initialize a new Node.js project
npm init -y

# Install Express
npm install express

# Install common middleware (optional)
npm install morgan cors helmet
```

### Basic Server Structure

```js
// app.js
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

// Middleware for parsing JSON requests
app.use(express.json());

// Basic route
app.get("/", (req, res) => {
	res.json({
		message: "Welcome to Express!",
		timestamp: new Date().toISOString(),
	});
});

// API route with parameters
app.get("/api/users/:id", (req, res) => {
	const userId = req.params.id;
	res.json({
		user: {
			id: userId,
			name: `User ${userId}`,
		},
	});
});

// Start server
app.listen(PORT, () => {
	console.log(`Server running on http://localhost:${PORT}`);
});
```

### Project Structure Best Practices

```
my-express-app/
├── app.js              # Main application entry point
├── package.json        # Dependencies and scripts
├── routes/             # Route definitions
│   ├── users.js
│   └── products.js
├── middleware/         # Custom middleware
│   ├── auth.js
│   └── validation.js
├── controllers/        # Route handlers
│   ├── userController.js
│   └── productController.js
├── models/             # Data models
├── config/             # Configuration files
└── public/             # Static assets
```

This modular structure becomes essential as applications grow in complexity, allowing for better organization, testing, and maintenance.
