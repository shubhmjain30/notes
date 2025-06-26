# Advanced Express.js Features

Beyond basic routing and middleware, Express.js supports sophisticated features for building modern, feature-rich web applications. This section covers server-side rendering, real-time communication, authentication, and security best practices.

## Server-Side Rendering (SSR)

**Problem:** While Single Page Applications (SPAs) are popular, they have drawbacks: poor SEO, slower initial page loads, and complexity in managing client-side state.

**Solution:** Server-Side Rendering generates HTML on the server before sending it to the client, improving SEO and initial load times.

### Template Engines Comparison

| Engine     | Syntax            | Learning Curve | Performance | Use Case              |
| ---------- | ----------------- | -------------- | ----------- | --------------------- |
| EJS        | HTML + JS         | Easy           | Good        | Familiar HTML syntax  |
| Pug        | Indentation-based | Medium         | Excellent   | Clean, minimal syntax |
| Handlebars | Mustache-style    | Easy           | Good        | Logic-less templates  |

### Complete EJS Implementation

```js
// app.js - EJS Setup
const express = require("express");
const path = require("path");
const app = express();

// Set EJS as template engine
app.set("view engine", "ejs");
app.set("views", path.join(__dirname, "views"));

// Serve static files
app.use(express.static(path.join(__dirname, "public")));

// Sample data (in production, this would come from a database)
const products = [
	{ id: 1, name: "Laptop", price: 999, category: "Electronics" },
	{ id: 2, name: "Book", price: 19, category: "Education" },
	{ id: 3, name: "Headphones", price: 199, category: "Electronics" },
];

// Routes
app.get("/", (req, res) => {
	res.render("index", {
		title: "Welcome to Our Store",
		message: "Discover amazing products",
		featuredProducts: products.slice(0, 2),
	});
});

app.get("/products", (req, res) => {
	const { category, search } = req.query;

	let filteredProducts = products;

	if (category) {
		filteredProducts = products.filter(
			(p) => p.category.toLowerCase() === category.toLowerCase()
		);
	}

	if (search) {
		filteredProducts = filteredProducts.filter((p) =>
			p.name.toLowerCase().includes(search.toLowerCase())
		);
	}

	res.render("products", {
		title: "Our Products",
		products: filteredProducts,
		currentCategory: category,
		searchTerm: search,
	});
});

app.get("/products/:id", (req, res) => {
	const product = products.find((p) => p.id === parseInt(req.params.id));

	if (!product) {
		return res.status(404).render("404", {
			title: "Product Not Found",
			message: "The requested product could not be found.",
		});
	}

	res.render("product-detail", {
		title: product.name,
		product,
	});
});
```

#### EJS Templates

```html
<!-- views/layout.ejs - Base layout -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><%= title %> | My Store</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="/css/style.css">
</head>
<body>
    <%- include('partials/navbar') %>

    <main class="container mt-4">
        <%- body %>
    </main>

    <%- include('partials/footer') %>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>

<!-- views/partials/navbar.ejs -->
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <div class="container">
        <a class="navbar-brand" href="/">My Store</a>
        <div class="navbar-nav">
            <a class="nav-link" href="/">Home</a>
            <a class="nav-link" href="/products">Products</a>
        </div>
    </div>
</nav>

<!-- views/index.ejs -->
<div class="hero-section bg-primary text-white p-5 mb-4 rounded">
    <h1><%= title %></h1>
    <p class="lead"><%= message %></p>
</div>

<div class="row">
    <div class="col-md-12">
        <h2>Featured Products</h2>
        <div class="row">
            <% featuredProducts.forEach(product => { %>
                <div class="col-md-6 mb-3">
                    <div class="card">
                        <div class="card-body">
                            <h5 class="card-title"><%= product.name %></h5>
                            <p class="card-text">$<%= product.price %></p>
                            <a href="/products/<%= product.id %>" class="btn btn-primary">View Details</a>
                        </div>
                    </div>
                </div>
            <% }); %>
        </div>
    </div>
</div>

<!-- views/products.ejs -->
<div class="d-flex justify-content-between align-items-center mb-4">
    <h1><%= title %></h1>

    <!-- Search and Filter Form -->
    <form method="GET" class="d-flex">
        <input type="text" name="search" class="form-control me-2"
               placeholder="Search products..." value="<%= searchTerm || '' %>">
        <select name="category" class="form-select me-2">
            <option value="">All Categories</option>
            <option value="Electronics" <%= currentCategory === 'Electronics' ? 'selected' : '' %>>Electronics</option>
            <option value="Education" <%= currentCategory === 'Education' ? 'selected' : '' %>>Education</option>
        </select>
        <button type="submit" class="btn btn-outline-primary">Filter</button>
    </form>
</div>

<div class="row">
    <% if (products.length === 0) { %>
        <div class="col-12">
            <div class="alert alert-info">No products found matching your criteria.</div>
        </div>
    <% } else { %>
        <% products.forEach(product => { %>
            <div class="col-md-4 mb-3">
                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title"><%= product.name %></h5>
                        <p class="card-text">Category: <%= product.category %></p>
                        <p class="card-text"><strong>$<%= product.price %></strong></p>
                        <a href="/products/<%= product.id %>" class="btn btn-primary">View Details</a>
                    </div>
                </div>
            </div>
        <% }); %>
    <% } %>
</div>
```

### Pug Implementation Example

```js
// Using Pug template engine
app.set("view engine", "pug");

app.get("/dashboard", (req, res) => {
	res.render("dashboard", {
		user: { name: "John Doe", role: "admin" },
		stats: { users: 150, orders: 89, revenue: 15420 },
	});
});
```

```pug
//- views/dashboard.pug
extends layout

block content
  .dashboard
    h1 Welcome back, #{user.name}!

    .row
      .col-md-4
        .card.bg-primary.text-white
          .card-body
            h3 #{stats.users}
            p Total Users
      .col-md-4
        .card.bg-success.text-white
          .card-body
            h3 #{stats.orders}
            p Orders Today
      .col-md-4
        .card.bg-info.text-white
          .card-body
            h3 $#{stats.revenue}
            p Revenue
```

## Real-Time Communication

**Problem:** Traditional HTTP is request-response based, making real-time features like chat, live notifications, or collaborative editing difficult to implement.

**Solution:** WebSockets provide full-duplex communication between client and server.

### Socket.IO Complete Implementation

```js
// app.js - Setting up Socket.IO
const express = require("express");
const http = require("http");
const socketIo = require("socket.io");
const path = require("path");

const app = express();
const server = http.createServer(app);
const io = socketIo(server, {
	cors: {
		origin: "http://localhost:3000",
		methods: ["GET", "POST"],
	},
});

app.use(express.static(path.join(__dirname, "public")));
app.set("view engine", "ejs");

// In-memory storage (use Redis in production)
const chatRooms = new Map();
const activeUsers = new Map();

// Routes
app.get("/", (req, res) => {
	res.render("chat", { title: "Real-time Chat" });
});

app.get("/admin", (req, res) => {
	const rooms = Array.from(chatRooms.entries()).map(([name, room]) => ({
		name,
		userCount: room.users.length,
		messageCount: room.messages.length,
	}));

	res.render("admin", {
		title: "Chat Admin",
		rooms,
		totalUsers: activeUsers.size,
	});
});

// Socket.IO event handlers
io.on("connection", (socket) => {
	console.log(`User connected: ${socket.id}`);

	// User joins a room
	socket.on("join-room", ({ username, room }) => {
		socket.username = username;
		socket.room = room;

		// Initialize room if it doesn't exist
		if (!chatRooms.has(room)) {
			chatRooms.set(room, {
				users: [],
				messages: [],
			});
		}

		const roomData = chatRooms.get(room);

		// Add user to room and active users
		roomData.users.push({ id: socket.id, username });
		activeUsers.set(socket.id, { username, room });

		socket.join(room);

		// Notify others in the room
		socket.to(room).emit("user-joined", {
			username,
			message: `${username} joined the room`,
			timestamp: new Date(),
		});

		// Send room info to the new user
		socket.emit("room-info", {
			users: roomData.users,
			messages: roomData.messages.slice(-50), // Last 50 messages
		});

		// Update user list for all users in room
		io.to(room).emit("users-update", roomData.users);

		console.log(`${username} joined room: ${room}`);
	});

	// Handle chat messages
	socket.on("chat-message", (data) => {
		const { message } = data;
		const room = socket.room;
		const username = socket.username;

		if (!room || !username) return;

		const messageData = {
			id: Date.now(),
			username,
			message,
			timestamp: new Date(),
			room,
		};

		// Store message in room
		const roomData = chatRooms.get(room);
		if (roomData) {
			roomData.messages.push(messageData);

			// Keep only last 100 messages
			if (roomData.messages.length > 100) {
				roomData.messages = roomData.messages.slice(-100);
			}
		}

		// Broadcast to all users in the room
		io.to(room).emit("chat-message", messageData);

		console.log(`Message in ${room} from ${username}: ${message}`);
	});

	// Handle typing indicators
	socket.on("typing-start", () => {
		socket.to(socket.room).emit("user-typing", {
			username: socket.username,
			isTyping: true,
		});
	});

	socket.on("typing-stop", () => {
		socket.to(socket.room).emit("user-typing", {
			username: socket.username,
			isTyping: false,
		});
	});

	// Handle disconnection
	socket.on("disconnect", () => {
		const user = activeUsers.get(socket.id);

		if (user) {
			const { username, room } = user;
			const roomData = chatRooms.get(room);

			if (roomData) {
				// Remove user from room
				roomData.users = roomData.users.filter(
					(u) => u.id !== socket.id
				);

				// Notify others
				socket.to(room).emit("user-left", {
					username,
					message: `${username} left the room`,
					timestamp: new Date(),
				});

				// Update user list
				io.to(room).emit("users-update", roomData.users);

				// Remove empty rooms
				if (roomData.users.length === 0) {
					chatRooms.delete(room);
				}
			}

			activeUsers.delete(socket.id);
			console.log(`${username} disconnected from ${room}`);
		}
	});
});

const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
	console.log(`Server running on port ${PORT}`);
});
```

#### Client-Side Socket.IO

```html
<!-- public/js/chat.js -->
<script src="/socket.io/socket.io.js"></script>
<script>
	const socket = io();

	const chatForm = document.getElementById("chat-form");
	const messageInput = document.getElementById("message-input");
	const messagesContainer = document.getElementById("messages");
	const usersContainer = document.getElementById("users");
	const typingIndicator = document.getElementById("typing-indicator");

	let typingTimer;
	const typingTimeout = 3000;

	// Join room on page load
	const username = prompt("Enter your username:");
	const room = prompt("Enter room name:") || "general";

	if (username) {
		socket.emit("join-room", { username, room });
	}

	// Send message
	chatForm.addEventListener("submit", (e) => {
		e.preventDefault();
		const message = messageInput.value.trim();

		if (message) {
			socket.emit("chat-message", { message });
			messageInput.value = "";
			socket.emit("typing-stop");
		}
	});

	// Typing indicators
	messageInput.addEventListener("input", () => {
		socket.emit("typing-start");

		clearTimeout(typingTimer);
		typingTimer = setTimeout(() => {
			socket.emit("typing-stop");
		}, typingTimeout);
	});

	// Socket event listeners
	socket.on("chat-message", (data) => {
		displayMessage(data);
		scrollToBottom();
	});

	socket.on("user-joined", (data) => {
		displaySystemMessage(data);
	});

	socket.on("user-left", (data) => {
		displaySystemMessage(data);
	});

	socket.on("users-update", (users) => {
		updateUsersList(users);
	});

	socket.on("user-typing", (data) => {
		showTypingIndicator(data);
	});

	socket.on("room-info", (data) => {
		updateUsersList(data.users);
		data.messages.forEach((message) => displayMessage(message));
		scrollToBottom();
	});

	// Helper functions
	function displayMessage(data) {
		const messageEl = document.createElement("div");
		messageEl.className = "message";
		messageEl.innerHTML = `
    <div class="message-header">
      <strong>${data.username}</strong>
      <small>${new Date(data.timestamp).toLocaleTimeString()}</small>
    </div>
    <div class="message-content">${escapeHtml(data.message)}</div>
  `;
		messagesContainer.appendChild(messageEl);
	}

	function displaySystemMessage(data) {
		const messageEl = document.createElement("div");
		messageEl.className = "system-message";
		messageEl.innerHTML = `
    <em>${data.message}</em>
    <small>${new Date(data.timestamp).toLocaleTimeString()}</small>
  `;
		messagesContainer.appendChild(messageEl);
	}

	function updateUsersList(users) {
		usersContainer.innerHTML = "<h4>Online Users</h4>";
		users.forEach((user) => {
			const userEl = document.createElement("div");
			userEl.textContent = user.username;
			usersContainer.appendChild(userEl);
		});
	}

	function showTypingIndicator(data) {
		if (data.isTyping) {
			typingIndicator.textContent = `${data.username} is typing...`;
		} else {
			typingIndicator.textContent = "";
		}
	}

	function scrollToBottom() {
		messagesContainer.scrollTop = messagesContainer.scrollHeight;
	}

	function escapeHtml(text) {
		const div = document.createElement("div");
		div.textContent = text;
		return div.innerHTML;
	}
</script>
```

## Authentication & Security

### JWT Authentication Implementation

```js
// middleware/auth.js
const jwt = require("jsonwebtoken");
const bcrypt = require("bcryptjs");
const { AppError } = require("./errorHandler");

class AuthMiddleware {
	// Generate JWT token
	static generateToken(payload) {
		return jwt.sign(payload, process.env.JWT_SECRET, {
			expiresIn: process.env.JWT_EXPIRES_IN || "7d",
		});
	}

	// Verify JWT token middleware
	static authenticateToken(req, res, next) {
		const authHeader = req.headers["authorization"];
		const token = authHeader && authHeader.split(" ")[1]; // Bearer TOKEN

		if (!token) {
			return next(new AppError("Access token is required", 401));
		}

		jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
			if (err) {
				return next(new AppError("Invalid or expired token", 403));
			}

			req.user = user;
			next();
		});
	}

	// Role-based authorization
	static authorize(...roles) {
		return (req, res, next) => {
			if (!req.user) {
				return next(new AppError("Authentication required", 401));
			}

			if (!roles.includes(req.user.role)) {
				return next(new AppError("Insufficient permissions", 403));
			}

			next();
		};
	}

	// Hash password
	static async hashPassword(password) {
		const saltRounds = 12;
		return await bcrypt.hash(password, saltRounds);
	}

	// Compare password
	static async comparePassword(password, hashedPassword) {
		return await bcrypt.compare(password, hashedPassword);
	}
}

// controllers/authController.js
const User = require("../models/User");
const AuthMiddleware = require("../middleware/auth");

class AuthController {
	static async register(req, res, next) {
		try {
			const { username, email, password, role = "user" } = req.body;

			// Check if user exists
			const existingUser = await User.findOne({
				$or: [{ email }, { username }],
			});

			if (existingUser) {
				throw new AppError(
					"User with this email or username already exists",
					409
				);
			}

			// Hash password
			const hashedPassword = await AuthMiddleware.hashPassword(password);

			// Create user
			const user = await User.create({
				username,
				email,
				password: hashedPassword,
				role,
			});

			// Generate token
			const token = AuthMiddleware.generateToken({
				id: user._id,
				username: user.username,
				email: user.email,
				role: user.role,
			});

			res.status(201).json({
				success: true,
				message: "User registered successfully",
				data: {
					token,
					user: {
						id: user._id,
						username: user.username,
						email: user.email,
						role: user.role,
					},
				},
			});
		} catch (error) {
			next(error);
		}
	}

	static async login(req, res, next) {
		try {
			const { email, password } = req.body;

			// Find user
			const user = await User.findOne({ email }).select("+password");

			if (!user) {
				throw new AppError("Invalid email or password", 401);
			}

			// Check password
			const isValidPassword = await AuthMiddleware.comparePassword(
				password,
				user.password
			);

			if (!isValidPassword) {
				throw new AppError("Invalid email or password", 401);
			}

			// Generate token
			const token = AuthMiddleware.generateToken({
				id: user._id,
				username: user.username,
				email: user.email,
				role: user.role,
			});

			res.json({
				success: true,
				message: "Login successful",
				data: {
					token,
					user: {
						id: user._id,
						username: user.username,
						email: user.email,
						role: user.role,
					},
				},
			});
		} catch (error) {
			next(error);
		}
	}

	static async getProfile(req, res, next) {
		try {
			const user = await User.findById(req.user.id);

			if (!user) {
				throw new AppError("User not found", 404);
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

module.exports = AuthController;
```

### Protected Routes Usage

```js
// routes/protected.js
const express = require("express");
const router = express.Router();
const { authenticateToken, authorize } = require("../middleware/auth");
const AuthController = require("../controllers/authController");

// Public routes
router.post("/register", AuthController.register);
router.post("/login", AuthController.login);

// Protected routes
router.get("/profile", authenticateToken, AuthController.getProfile);

// Admin only routes
router.get(
	"/admin/users",
	authenticateToken,
	authorize("admin"),
	(req, res) => {
		res.json({ message: "Admin access granted" });
	}
);

// Multi-role access
router.get(
	"/dashboard",
	authenticateToken,
	authorize("admin", "moderator"),
	(req, res) => {
		res.json({ message: "Dashboard access granted" });
	}
);

module.exports = router;
```

## Security Best Practices

### Essential Security Middleware

```js
const helmet = require("helmet");
const rateLimit = require("express-rate-limit");
const mongoSanitize = require("express-mongo-sanitize");
const xss = require("xss-clean");
const hpp = require("hpp");

// Security middleware setup
app.use(helmet()); // Set security headers

app.use(mongoSanitize()); // Prevent NoSQL injection attacks

app.use(xss()); // Clean user input from malicious XSS

app.use(
	hpp({
		// Prevent parameter pollution
		whitelist: ["sort", "fields", "page", "limit"], // Allow these duplicate parameters
	})
);

// Rate limiting
const limiter = rateLimit({
	windowMs: 15 * 60 * 1000, // 15 minutes
	max: 100, // limit each IP to 100 requests per windowMs
	message: "Too many requests from this IP, please try again later.",
	standardHeaders: true,
	legacyHeaders: false,
});

app.use("/api/", limiter);

// Stricter rate limiting for auth endpoints
const authLimiter = rateLimit({
	windowMs: 15 * 60 * 1000,
	max: 5,
	message: "Too many authentication attempts, please try again later.",
});

app.use("/api/auth/", authLimiter);
```

These advanced features transform Express.js from a simple web server into a powerful platform for building modern, secure, and interactive web applications.
