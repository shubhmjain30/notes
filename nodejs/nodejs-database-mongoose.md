# Mongoose Essentials

This file covers the MongoDB side of the Node.js stack using Mongoose.

## TL;DR

Mongoose is an ODM for MongoDB that helps define schemas, models, validation, and query behavior for document databases.

## What Mongoose Gives You

- Schemas
- Models
- Validation
- Middleware hooks
- Query helpers

## Basic Schema And Model

```javascript
const mongoose = require("mongoose");

const userSchema = new mongoose.Schema({
	name: { type: String, required: true },
	email: { type: String, required: true, unique: true },
});

const User = mongoose.model("User", userSchema);
```

## CRUD Basics

### Create

```javascript
const user = await User.create({ name: "Ada", email: "ada@example.com" });
```

### Read

```javascript
const users = await User.find();
```

### Update

```javascript
await User.findByIdAndUpdate(id, { name: "Grace" });
```

### Delete

```javascript
await User.findByIdAndDelete(id);
```

## Relationships

### What To Know

- Use ObjectId references for linked documents
- Use `populate` when you need referenced documents resolved
- Do not over-populate blindly in hot paths

## Validation

Schema validation is useful, but API-layer validation still matters too.

## Interview Questions

### What does Mongoose do?

It provides schema, model, validation, and document utilities on top of MongoDB.

### When would you use populate?

When you need referenced documents resolved, but I would use it carefully because it can hide query cost.
