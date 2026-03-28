# Sequelize Essentials

This file covers the SQL side of the Node.js stack using Sequelize.

## TL;DR

Sequelize is an ORM that maps JavaScript models to relational database tables and helps manage associations, queries, and schema workflows.

## What Sequelize Gives You

- Models
- Associations
- Validations
- Query helpers
- Migration-friendly workflows

## Basic Model

```javascript
const { DataTypes } = require("sequelize");

const User = sequelize.define("User", {
	name: {
		type: DataTypes.STRING,
		allowNull: false,
	},
	email: {
		type: DataTypes.STRING,
		allowNull: false,
		unique: true,
	},
});
```

## CRUD Basics

### Create

```javascript
await User.create({ name: "Ada", email: "ada@example.com" });
```

### Read

```javascript
const users = await User.findAll();
```

### Update

```javascript
await User.update({ name: "Grace" }, { where: { id } });
```

### Delete

```javascript
await User.destroy({ where: { id } });
```

## Associations

### What To Know

- `hasOne`
- `belongsTo`
- `hasMany`
- `belongsToMany`

### Interview Answer

Sequelize associations model relational links directly and map well to SQL concepts such as foreign keys and join tables.

## Validation

Sequelize supports validation, but database constraints still matter too.

## Interview Questions

### What does Sequelize solve?

It provides a model-based way to work with SQL databases from JavaScript while handling associations, validation, and query generation.

### Why use an ORM at all?

It improves developer productivity and consistency, though I still keep SQL behavior and query cost in mind.
