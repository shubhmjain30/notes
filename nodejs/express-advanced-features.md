# Advanced Express.js Features

Express.js offers several advanced features that allow you to build sophisticated web applications. This guide covers server-side rendering, real-time communication, authentication, and other advanced capabilities.

[[Node.js Express.js Roadmap|← Back to Node.js Roadmap]]

## Server-Side Rendering with Template Engines

Express supports various template engines that allow you to generate HTML dynamically on the server.

### Setting Up EJS

EJS (Embedded JavaScript) is one of the most popular template engines for Express:

```bash
npm install ejs
```

Configure Express to use EJS:

```javascript
const express = require("express");
const path = require("path");
const app = express();

// Set the view engine to EJS
app.set("view engine", "ejs");
app.set("views", path.join(__dirname, "views"));

// Route that uses the template
app.get("/", (req, res) => {
	const data = {
		title: "Home Page",
		message: "Welcome to our website!",
		users: [
			{ name: "John", age: 30 },
			{ name: "Jane", age: 25 },
			{ name: "Bob", age: 35 },
		],
	};

	res.render("index", data);
});

app.listen(3000, () => {
	console.log("Server running on port 3000");
});
```

Create a view template (views/index.ejs):

```html
<!DOCTYPE html>
<html>
	<head>
		<title><%= title %></title>
		<link rel="stylesheet" href="/css/style.css" />
	</head>
	<body>
		<h1><%= message %></h1>

		<h2>User List</h2>
		<ul>
			<% users.forEach(user => { %>
			<li><%= user.name %> - <%= user.age %> years old</li>
			<% }); %>
		</ul>

		<%- include('partials/footer') %>
	</body>
</html>
```

Create a partial template (views/partials/footer.ejs):

```html
<footer>
	<p>&copy; <%= new Date().getFullYear() %> My Express App</p>
</footer>
```

### Using Pug (formerly Jade)

Pug offers a more concise syntax:

```bash
npm install pug
```

Configure Express to use Pug:

```javascript
app.set("view engine", "pug");
app.set("views", path.join(__dirname, "views"));
```

Create a Pug template (views/index.pug):

```pug
doctype html
html
  head
    title= title
    link(rel="stylesheet", href="/css/style.css")
  body
    h1= message

    h2 User List
    ul
      each user in users
        li #{user.name} - #{user.age} years old

    include partials/footer
```

## Real-Time Communication with Socket.IO

Socket.IO enables real-time, bidirectional communication between web clients and servers:

```bash
npm install socket.io
```

Implementing a basic chat application:

```javascript
// Server setup
const express = require("express");
const http = require("http");
const socketIo = require("socket.io");

const app = express();
const server = http.createServer(app);
const io = socketIo(server);

// Serve static files
app.use(express.static("public"));

// Serve the chat page
app.get("/", (req, res) => {
	res.sendFile(__dirname + "/public/chat.html");
});

// Socket.IO connection handling
io.on("connection", (socket) => {
	console.log("A user connected");

	// Handle chat messages
	socket.on("chat message", (msg) => {
		console.log("Message received: " + msg);
		// Broadcast the message to all connected clients
		io.emit("chat message", msg);
	});

	// Handle user disconnection
	socket.on("disconnect", () => {
		console.log("A user disconnected");
	});
});

server.listen(3000, () => {
	console.log("Server running on port 3000");
});
```

Create a client-side interface (public/chat.html):

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Socket.IO Chat</title>
		<style>
			body {
				margin: 0;
				padding-bottom: 3rem;
				font-family: -apple-system, BlinkMacSystemFont, "Segoe UI",
					Roboto, Helvetica, Arial, sans-serif;
			}
			#form {
				background: rgba(0, 0, 0, 0.15);
				padding: 0.25rem;
				position: fixed;
				bottom: 0;
				left: 0;
				right: 0;
				display: flex;
				height: 3rem;
				box-sizing: border-box;
				backdrop-filter: blur(10px);
			}
			#input {
				border: none;
				padding: 0 1rem;
				flex-grow: 1;
				border-radius: 2rem;
				margin: 0.25rem;
			}
			#input:focus {
				outline: none;
			}
			#form > button {
				background: #333;
				border: none;
				padding: 0 1rem;
				margin: 0.25rem;
				border-radius: 3px;
				outline: none;
				color: #fff;
			}
			#messages {
				list-style-type: none;
				margin: 0;
				padding: 0;
			}
			#messages > li {
				padding: 0.5rem 1rem;
			}
			#messages > li:nth-child(odd) {
				background: #efefef;
			}
		</style>
	</head>
	<body>
		<ul id="messages"></ul>
		<form id="form" action="">
			<input id="input" autocomplete="off" /><button>Send</button>
		</form>

		<script src="/socket.io/socket.io.js"></script>
		<script>
			const socket = io();

			const form = document.getElementById("form");
			const input = document.getElementById("input");
			const messages = document.getElementById("messages");

			form.addEventListener("submit", (e) => {
				e.preventDefault();
				if (input.value) {
					socket.emit("chat message", input.value);
					input.value = "";
				}
			});

			socket.on("chat message", (msg) => {
				const item = document.createElement("li");
				item.textContent = msg;
				messages.appendChild(item);
				window.scrollTo(0, document.body.scrollHeight);
			});
		</script>
	</body>
</html>
```

## Authentication with Passport.js

Passport.js is a popular authentication middleware for Express:

```bash
npm install passport passport-local express-session bcryptjs
```

Setting up local authentication:

```javascript
const express = require("express");
const session = require("express-session");
const passport = require("passport");
const LocalStrategy = require("passport-local").Strategy;
const bcrypt = require("bcryptjs");

const app = express();

// Mock user database
const users = [
	{
		id: 1,
		username: "user1",
		// Password: 'password123'
		passwordHash:
			"$2a$10$rQSL6CJgS9oq9vK7dMh8UeZywEHLjpYFGKj7LqkMHNAoZLxXDGEfm",
	},
];

// Middleware
app.use(express.urlencoded({ extended: false }));
app.use(express.json());
app.use(
	session({
		secret: "your-secret-key",
		resave: false,
		saveUninitialized: false,
	})
);

// Initialize Passport
app.use(passport.initialize());
app.use(passport.session());

// Passport Local Strategy
passport.use(
	new LocalStrategy((username, password, done) => {
		// Find user
		const user = users.find((u) => u.username === username);
		if (!user) {
			return done(null, false, { message: "Incorrect username." });
		}

		// Check password
		bcrypt.compare(password, user.passwordHash, (err, isMatch) => {
			if (err) throw err;
			if (isMatch) {
				return done(null, user);
			} else {
				return done(null, false, { message: "Incorrect password." });
			}
		});
	})
);

// Serialize user for session
passport.serializeUser((user, done) => {
	done(null, user.id);
});

// Deserialize user from session
passport.deserializeUser((id, done) => {
	const user = users.find((u) => u.id === id);
	done(null, user);
});

// Authentication middleware
function isAuthenticated(req, res, next) {
	if (req.isAuthenticated()) {
		return next();
	}
	res.redirect("/login");
}

// Routes
app.get("/", (req, res) => {
	res.send(`
    <h1>Home Page</h1>
    ${
		req.isAuthenticated()
			? `<p>Welcome, ${req.user.username}! <a href="/logout">Logout</a></p>`
			: '<p><a href="/login">Login</a></p>'
	}
  `);
});

app.get("/login", (req, res) => {
	res.send(`
    <h1>Login</h1>
    <form action="/login" method="post">
      <div>
        <label>Username:</label>
        <input type="text" name="username" required>
      </div>
      <div>
        <label>Password:</label>
        <input type="password" name="password" required>
      </div>
      <div>
        <input type="submit" value="Log In">
      </div>
    </form>
  `);
});

app.post(
	"/login",
	passport.authenticate("local", {
		successRedirect: "/dashboard",
		failureRedirect: "/login",
	})
);

app.get("/dashboard", isAuthenticated, (req, res) => {
	res.send(`
    <h1>Dashboard</h1>
    <p>Welcome, ${req.user.username}!</p>
    <p>This is a protected route.</p>
    <p><a href="/logout">Logout</a></p>
  `);
});

app.get("/logout", (req, res) => {
	req.logout(function (err) {
		if (err) {
			return next(err);
		}
		res.redirect("/");
	});
});

app.listen(3000, () => {
	console.log("Server running on port 3000");
});
```

## JWT Authentication

JSON Web Tokens provide a stateless authentication mechanism:

```bash
npm install jsonwebtoken
```

Implementing JWT authentication:

```javascript
const express = require("express");
const jwt = require("jsonwebtoken");
const bcrypt = require("bcryptjs");

const app = express();
app.use(express.json());

// Secret key for JWT
const JWT_SECRET = "your-secret-key";

// Mock user database
const users = [
	{
		id: 1,
		username: "user1",
		// Password: 'password123'
		passwordHash:
			"$2a$10$rQSL6CJgS9oq9vK7dMh8UeZywEHLjpYFGKj7LqkMHNAoZLxXDGEfm",
	},
];

// Authentication middleware
function authenticateToken(req, res, next) {
	const authHeader = req.headers["authorization"];
	const token = authHeader && authHeader.split(" ")[1]; // Bearer TOKEN

	if (!token) {
		return res.status(401).json({ message: "Access token required" });
	}

	jwt.verify(token, JWT_SECRET, (err, user) => {
		if (err) {
			return res
				.status(403)
				.json({ message: "Invalid or expired token" });
		}
		req.user = user;
		next();
	});
}

// Login route
app.post("/api/login", (req, res) => {
	const { username, password } = req.body;

	// Find user
	const user = users.find((u) => u.username === username);
	if (!user) {
		return res.status(401).json({ message: "Invalid credentials" });
	}

	// Check password
	bcrypt.compare(password, user.passwordHash, (err, isMatch) => {
		if (err || !isMatch) {
			return res.status(401).json({ message: "Invalid credentials" });
		}

		// Generate JWT
		const token = jwt.sign(
			{ id: user.id, username: user.username },
			JWT_SECRET,
			{ expiresIn: "1h" }
		);

		res.json({ token });
	});
});

// Protected route
app.get("/api/profile", authenticateToken, (req, res) => {
	res.json({
		id: req.user.id,
		username: req.user.username,
		message: "This is protected data",
	});
});

app.listen(3000, () => {
	console.log("Server running on port 3000");
});
```

## File Uploads with Multer

Multer is a middleware for handling multipart/form-data, primarily used for file uploads:

```bash
npm install multer
```

Setting up file uploads:

```javascript
const express = require("express");
const multer = require("multer");
const path = require("path");

const app = express();

// Set up storage for uploaded files
const storage = multer.diskStorage({
	destination: (req, file, cb) => {
		cb(null, "uploads/");
	},
	filename: (req, file, cb) => {
		cb(null, Date.now() + path.extname(file.originalname));
	},
});

// Create the multer instance
const upload = multer({
	storage: storage,
	limits: { fileSize: 1000000 }, // 1MB limit
	fileFilter: (req, file, cb) => {
		// Check file types
		const fileTypes = /jpeg|jpg|png|gif/;
		const extname = fileTypes.test(
			path.extname(file.originalname).toLowerCase()
		);
		const mimetype = fileTypes.test(file.mimetype);

		if (extname && mimetype) {
			return cb(null, true);
		} else {
			cb(new Error("Error: Images only!"));
		}
	},
});

// Serve static files
app.use(express.static("public"));
app.use("/uploads", express.static("uploads"));

// File upload route - single file
app.post("/upload/single", upload.single("image"), (req, res) => {
	if (!req.file) {
		return res.status(400).send("No file uploaded");
	}

	res.json({
		message: "File uploaded successfully",
		file: req.file,
	});
});

// File upload route - multiple files
app.post("/upload/multiple", upload.array("images", 5), (req, res) => {
	if (!req.files || req.files.length === 0) {
		return res.status(400).send("No files uploaded");
	}

	res.json({
		message: `${req.files.length} files uploaded successfully`,
		files: req.files,
	});
});

// Error handling for multer
app.use((err, req, res, next) => {
	if (err instanceof multer.MulterError) {
		// A Multer error occurred when uploading
		return res.status(400).json({ message: err.message });
	} else if (err) {
		// An unknown error occurred
		return res.status(500).json({ message: err.message });
	}
	next();
});

app.listen(3000, () => {
	console.log("Server running on port 3000");
});
```

Create a simple HTML form (public/upload.html):

```html
<!DOCTYPE html>
<html>
	<head>
		<title>File Upload</title>
	</head>
	<body>
		<h1>Single File Upload</h1>
		<form
			action="/upload/single"
			method="POST"
			enctype="multipart/form-data"
		>
			<input type="file" name="image" accept="image/*" />
			<button type="submit">Upload</button>
		</form>

		<h1>Multiple File Upload</h1>
		<form
			action="/upload/multiple"
			method="POST"
			enctype="multipart/form-data"
		>
			<input type="file" name="images" accept="image/*" multiple />
			<button type="submit">Upload</button>
		</form>
	</body>
</html>
```

## CORS (Cross-Origin Resource Sharing)

CORS is a security feature that restricts HTTP requests from different origins:

```bash
npm install cors
```

Implementing CORS:

```javascript
const express = require("express");
const cors = require("cors");

const app = express();

// Enable CORS for all routes
app.use(cors());

// Or with specific options
app.use(
	cors({
		origin: "https://example.com", // Restrict to specific origin
		methods: ["GET", "POST"], // Allow only GET and POST requests
		allowedHeaders: ["Content-Type", "Authorization"], // Allow specific headers
		credentials: true, // Allow cookies to be sent with requests
	})
);

// Enable CORS for a specific route
app.get("/api/data", cors(), (req, res) => {
	res.json({ message: "This route has CORS enabled" });
});

app.listen(3000, () => {
	console.log("Server running on port 3000");
});
```

## Rate Limiting

Protect your API from abuse by implementing rate limiting:

```bash
npm install express-rate-limit
```

Setting up rate limiting:

```javascript
const express = require("express");
const rateLimit = require("express-rate-limit");

const app = express();

// Create a limiter
const apiLimiter = rateLimit({
	windowMs: 15 * 60 * 1000, // 15 minutes
	max: 100, // Limit each IP to 100 requests per windowMs
	standardHeaders: true, // Return rate limit info in the `RateLimit-*` headers
	legacyHeaders: false, // Disable the `X-RateLimit-*` headers
	message:
		"Too many requests from this IP, please try again after 15 minutes",
});

// Apply the limiter to all API routes
app.use("/api", apiLimiter);

// Create a stricter limiter for login attempts
const loginLimiter = rateLimit({
	windowMs: 60 * 60 * 1000, // 1 hour
	max: 5, // 5 login attempts per hour
	message: "Too many login attempts, please try again after an hour",
});

// Apply the stricter limiter to the login route
app.use("/api/login", loginLimiter);

// Routes
app.get("/api/data", (req, res) => {
	res.json({ message: "API data" });
});

app.post("/api/login", (req, res) => {
	res.json({ message: "Login successful" });
});

app.listen(3000, () => {
	console.log("Server running on port 3000");
});
```

## Compression

Compress HTTP responses to improve performance:

```bash
npm install compression
```

Implementing compression:

```javascript
const express = require("express");
const compression = require("compression");

const app = express();

// Enable compression
app.use(compression());

// Routes
app.get("/api/large-data", (req, res) => {
	// This response will be compressed
	const largeData = {
		/* ... large data object ... */
	};
	res.json(largeData);
});

app.listen(3000, () => {
	console.log("Server running on port 3000");
});
```

## HTTPS in Express

Setting up HTTPS for secure communication:

```javascript
const express = require("express");
const https = require("https");
const fs = require("fs");
const path = require("path");

const app = express();

// Certificate files
const options = {
	key: fs.readFileSync(path.join(__dirname, "ssl", "private.key")),
	cert: fs.readFileSync(path.join(__dirname, "ssl", "certificate.crt")),
};

// Create HTTPS server
const server = https.createServer(options, app);

app.get("/", (req, res) => {
	res.send("Secure HTTPS server is running!");
});

server.listen(443, () => {
	console.log("HTTPS server running on port 443");
});
```

These advanced features enable you to build sophisticated, secure, and high-performance web applications with Express.js. By combining these capabilities, you can create modern web applications that meet complex requirements while maintaining good performance and security standards.
