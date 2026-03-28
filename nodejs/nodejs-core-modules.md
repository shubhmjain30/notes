# Node.js Core Modules

This file is a practical revision sheet for the built-in modules that appear often in interviews and real code.

## TL;DR

Core modules are built into Node.js, so you can use them without installing external packages.

## fs

### What To Know

- File system access
- Sync and async APIs
- Streams for large files

### Example

```javascript
const fs = require("fs");

fs.readFile("data.txt", "utf8", (error, data) => {
	if (error) throw error;
	console.log(data);
});
```

## path

### Why It Matters

Helps build safe, cross-platform file paths.

```javascript
const path = require("path");
const filePath = path.join(__dirname, "data", "config.json");
```

## http

### What To Know

- Low-level HTTP server creation
- Useful for understanding how frameworks sit on top of Node

```javascript
const http = require("http");

const server = http.createServer((req, res) => {
	res.writeHead(200, { "Content-Type": "text/plain" });
	res.end("Hello");
});
```

## stream

### Why It Matters

Streams are central to Node's efficiency story for I/O-heavy work.

## process

### What To Know

- Environment variables
- Process lifecycle
- Exit codes
- Memory usage

## Common Interview Questions

### Why avoid sync file APIs in request paths?

Because they block the event loop.

### Why are streams useful?

Because they process data incrementally and avoid buffering everything in memory.
