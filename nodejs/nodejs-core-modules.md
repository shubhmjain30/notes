# Node.js Core Modules

Node.js comes with a powerful set of core modules that provide essential functionality for building server-side applications. Mastering these modules is key to writing efficient, idiomatic Node.js code.

## Conceptual Overview

**Why Core Modules?**
Core modules are built into Node.js and do not require installation. They offer low-level access to the file system, networking, streams, cryptography, and more.

**Mental Model:**

-   Think of core modules as the standard library of Node.js—tools you can rely on for most server-side tasks, optimized for performance and security.

## Key Modules

-   **fs:** File system operations (read, write, watch files)
-   **http/https:** Create web servers and clients
-   **path:** Work with file and directory paths
-   **os:** System information
-   **events:** Event-driven programming
-   **stream:** Handle streaming data
-   **crypto:** Cryptography utilities

## Best Practices

-   Prefer asynchronous methods to avoid blocking the event loop.
-   Use streams for large file or network operations.
-   Always handle errors in callbacks and promise chains.

## Common Pitfalls

-   Using synchronous methods in production code.
-   Not handling errors, leading to crashes.
-   Forgetting to close file descriptors or network connections.

## Interview Q&A

**Q: What is the difference between fs.readFile and fs.readFileSync?**
A: fs.readFile is asynchronous and non-blocking; fs.readFileSync blocks the event loop until the operation completes.

**Q: When should you use streams in Node.js?**
A: Use streams for processing large files or data sources to avoid loading everything into memory at once.

**Q: How do you handle errors in core module APIs?**
A: Always check for errors in callbacks or use try/catch with promises/async functions.

---

[[nodejs-roadmap|← Back to Node.js Roadmap]]

## File System (fs)

The `fs` module provides an API for interacting with the file system. It offers both synchronous and asynchronous methods, with the asynchronous versions being preferred for production applications.

### Reading Files

```javascript
const fs = require("fs");

// Asynchronous read (non-blocking)
fs.readFile("example.txt", "utf8", (err, data) => {
	if (err) {
		console.error("Error reading file:", err);
		return;
	}
	console.log("File content:", data);
});

// Synchronous read (blocking) - avoid in production server code
try {
	const data = fs.readFileSync("example.txt", "utf8");
	console.log("File content (sync):", data);
} catch (err) {
	console.error("Error reading file synchronously:", err);
}

// Promise-based API (Node.js 10+)
fs.promises
	.readFile("example.txt", "utf8")
	.then((data) => console.log("File content (promise):", data))
	.catch((err) => console.error("Error reading file with promises:", err));
```

### Writing Files

```javascript
const fs = require("fs");

const content = "Hello, Node.js!";

// Asynchronous write
fs.writeFile("output.txt", content, (err) => {
	if (err) {
		console.error("Error writing file:", err);
		return;
	}
	console.log("File written successfully");
});

// Synchronous write
try {
	fs.writeFileSync("output-sync.txt", content);
	console.log("File written successfully (sync)");
} catch (err) {
	console.error("Error writing file synchronously:", err);
}

// Appending to a file
fs.appendFile("log.txt", content + "\n", (err) => {
	if (err) {
		console.error("Error appending to file:", err);
		return;
	}
	console.log("Data appended to file");
});
```

### Working with Directories

```javascript
const fs = require("fs");

// Create a directory
fs.mkdir("new-directory", (err) => {
	if (err) {
		console.error("Error creating directory:", err);
		return;
	}
	console.log("Directory created");
});

// Read directory contents
fs.readdir(".", (err, files) => {
	if (err) {
		console.error("Error reading directory:", err);
		return;
	}
	console.log("Directory contents:", files);
});

// Check if a file exists
fs.access("example.txt", fs.constants.F_OK, (err) => {
	console.log(err ? "File does not exist" : "File exists");
});
```

### Streams for Large Files

```javascript
const fs = require("fs");

// Reading a large file with streams
const readStream = fs.createReadStream("large-file.txt", { encoding: "utf8" });

readStream.on("data", (chunk) => {
	console.log(`Received ${chunk.length} bytes of data`);
});

readStream.on("end", () => {
	console.log("Finished reading file");
});

readStream.on("error", (err) => {
	console.error("Error reading stream:", err);
});

// Copying a file with streams
const readStream = fs.createReadStream("source.txt");
const writeStream = fs.createWriteStream("destination.txt");

readStream.pipe(writeStream);

writeStream.on("finish", () => {
	console.log("File copied successfully");
});
```

## Path

The `path` module provides utilities for working with file and directory paths in a cross-platform way.

```javascript
const path = require("path");

// Joining path segments
const fullPath = path.join("/users", "username", "documents", "file.txt");
console.log("Joined path:", fullPath);
// On Windows: \users\username\documents\file.txt
// On POSIX: /users/username/documents/file.txt

// Resolving to an absolute path
const absolutePath = path.resolve("relative/path/file.txt");
console.log("Absolute path:", absolutePath);

// Getting path components
console.log("Directory name:", path.dirname(fullPath));
console.log("File name:", path.basename(fullPath));
console.log("Extension:", path.extname(fullPath));

// Parsing a path
const pathInfo = path.parse(fullPath);
console.log("Path components:", pathInfo);
/*
{
  root: '/',
  dir: '/users/username/documents',
  base: 'file.txt',
  ext: '.txt',
  name: 'file'
}
*/

// Normalizing a path (resolving '..' and '.')
const normalizedPath = path.normalize(
	"/users/./username/../username/documents/"
);
console.log("Normalized path:", normalizedPath);
// Output: /users/username/documents/
```

## HTTP

The `http` module allows Node.js to transfer data over HTTP, enabling the creation of both servers and clients.

### Creating an HTTP Server

```javascript
const http = require("http");

// Create a basic HTTP server
const server = http.createServer((req, res) => {
	// Get request information
	console.log(`Request method: ${req.method}`);
	console.log(`Request URL: ${req.url}`);
	console.log(`Headers:`, req.headers);

	// Set response headers
	res.setHeader("Content-Type", "text/html");
	res.setHeader("X-Powered-By", "Node.js");

	// Write response body
	res.write("<html><body>");
	res.write("<h1>Hello from Node.js HTTP Server!</h1>");
	res.write(`<p>You requested: ${req.url}</p>`);
	res.write("</body></html>");

	// End the response
	res.end();
});

// Start the server on port 3000
server.listen(3000, () => {
	console.log("Server running at http://localhost:3000/");
});
```

### Making HTTP Requests

```javascript
const http = require("http");

// GET request
const options = {
	hostname: "example.com",
	port: 80,
	path: "/",
	method: "GET",
};

const req = http.request(options, (res) => {
	console.log(`Status Code: ${res.statusCode}`);

	res.on("data", (chunk) => {
		console.log(`Received ${chunk.length} bytes of data`);
	});

	res.on("end", () => {
		console.log("Response ended");
	});
});

req.on("error", (error) => {
	console.error("Error making request:", error);
});

req.end();

// Simpler GET request using http.get
http.get("http://example.com", (res) => {
	let data = "";

	res.on("data", (chunk) => {
		data += chunk;
	});

	res.on("end", () => {
		console.log("Response data:", data);
	});
}).on("error", (err) => {
	console.error("Error:", err.message);
});
```

## URL

The `url` module provides utilities for URL resolution and parsing.

```javascript
const url = require("url");

// Parsing a URL
const myURL = new URL(
	"https://user:pass@example.com:8080/path/name?query=string#hash"
);

console.log("Protocol:", myURL.protocol); // https:
console.log("Username:", myURL.username); // user
console.log("Password:", myURL.password); // pass
console.log("Hostname:", myURL.hostname); // example.com
console.log("Port:", myURL.port); // 8080
console.log("Pathname:", myURL.pathname); // /path/name
console.log("Search:", myURL.search); // ?query=string
console.log("Hash:", myURL.hash); // #hash

// Working with query parameters
myURL.searchParams.append("sort", "desc");
console.log("URL with added param:", myURL.href);

console.log("Get param:", myURL.searchParams.get("query")); // string
myURL.searchParams.set("query", "updated");
console.log("Updated URL:", myURL.href);
```

## Events

The `events` module is the foundation of Node.js' asynchronous event-driven architecture.

```javascript
const EventEmitter = require("events");

// Create a custom event emitter
class MyEmitter extends EventEmitter {}
const myEmitter = new MyEmitter();

// Register an event handler
myEmitter.on("event", (arg1, arg2) => {
	console.log("Event triggered with arguments:", arg1, arg2);
});

// Register a one-time event handler
myEmitter.once("oneTimeEvent", () => {
	console.log("This will be called only once");
});

// Emit events
myEmitter.emit("event", "first arg", "second arg");
myEmitter.emit("oneTimeEvent");
myEmitter.emit("oneTimeEvent"); // This won't trigger the handler again

// Error handling
myEmitter.on("error", (err) => {
	console.error("Error event:", err.message);
});

// Emit an error (without a handler, this would crash the application)
myEmitter.emit("error", new Error("Something went wrong"));

// Get all registered event names
console.log("Event names:", myEmitter.eventNames());

// Get listener count
console.log('Listener count for "event":', myEmitter.listenerCount("event"));
```

## Utilities (util)

The `util` module provides various utility functions for debugging and more.

```javascript
const util = require("util");
const fs = require("fs");

// Promisify a callback-based function
const readFile = util.promisify(fs.readFile);

readFile("example.txt", "utf8")
	.then((data) => console.log("File content:", data))
	.catch((err) => console.error("Error:", err));

// Formatting strings
const formatted = util.format(
	"Hello, %s! You have %d new messages.",
	"User",
	5
);
console.log(formatted); // Hello, User! You have 5 new messages.

// Inspect an object (useful for debugging)
const obj = {
	name: "Node.js",
	features: ["fast", "scalable", "JavaScript"],
	versions: {
		current: "v16.x",
		lts: "v14.x",
	},
};

console.log(util.inspect(obj, { colors: true, depth: null }));

// Checking types
console.log(util.isArray([])); // true
console.log(util.isRegExp(/regex/)); // true
console.log(util.isDate(new Date())); // true
```

## OS (Operating System)

The `os` module provides operating system-related utility methods and properties.

```javascript
const os = require("os");

// System information
console.log("OS Platform:", os.platform());
console.log("OS Architecture:", os.arch());
console.log("OS Release:", os.release());
console.log("OS Type:", os.type());

// Memory information
const totalMemory = os.totalmem() / 1024 / 1024 / 1024;
const freeMemory = os.freemem() / 1024 / 1024 / 1024;
console.log(`Total Memory: ${totalMemory.toFixed(2)} GB`);
console.log(`Free Memory: ${freeMemory.toFixed(2)} GB`);
console.log(
	`Memory Usage: ${(((totalMemory - freeMemory) / totalMemory) * 100).toFixed(
		2
	)}%`
);

// CPU information
console.log("CPUs:", os.cpus().length);
console.log("CPU Model:", os.cpus()[0].model);

// Network interfaces
console.log("Network Interfaces:", os.networkInterfaces());

// User information
console.log("Hostname:", os.hostname());
console.log("Home Directory:", os.homedir());
console.log("Temporary Directory:", os.tmpdir());
console.log("User Info:", os.userInfo());
```

These core modules form the foundation of Node.js development and are essential to understand for building efficient server-side applications.
