# Node.js Modules

This file covers how Node organizes code through modules and how CommonJS differs from ES Modules.

## TL;DR

Modules define code boundaries, dependency relationships, and loading behavior. In Node.js interviews, the most common comparison is CommonJS vs ES Modules.

## CommonJS

### Syntax

```javascript
const fs = require("fs");
module.exports = { readConfig };
```

### What To Know

- Synchronous-style loading syntax
- Traditional Node default for many codebases
- Uses `require`, `module.exports`, and `exports`

## ES Modules

### Syntax

```javascript
import fs from "fs";
export function readConfig() {}
```

### What To Know

- Standard JavaScript module syntax
- Uses `import` and `export`
- Increasingly common in modern Node apps

## CommonJS Vs ES Modules

| Topic             | CommonJS                          | ES Modules              |
| ----------------- | --------------------------------- | ----------------------- |
| Import syntax     | `require()`                       | `import`                |
| Export syntax     | `module.exports`                  | `export`                |
| Ecosystem history | Older Node default                | Modern standard         |
| Loading style     | Traditionally synchronous-looking | Static import structure |

## Module Resolution

### What To Know

Node resolves:

- Core modules
- Relative files
- Installed packages

### Why It Matters

Resolution affects debugging, package structure, and import correctness.

## Module Caching

### TL;DR

A loaded module is cached, so repeated imports usually return the same module instance.

### Why It Matters

This affects shared state and performance.

## Rapid-Fire Questions

### CommonJS vs ES Modules?

CommonJS uses `require` and `module.exports`. ES Modules use `import` and `export` and are the standard modern syntax.

### Why does module caching matter?

Because repeated loads can share the same module instance and preserve state.
