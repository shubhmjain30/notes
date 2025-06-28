# Data Persistence with Sequelize and PostgreSQL

Sequelize is a powerful ORM (Object-Relational Mapping) for Node.js, making it easy to interact with relational databases using JavaScript. Understanding how to model, query, and manage data with Sequelize is essential for building robust, scalable Node.js applications.

## Conceptual Overview

**Why use an ORM like Sequelize?**
ORMs abstract away SQL queries, allowing you to interact with your database using JavaScript objects and methods. This improves productivity, reduces boilerplate, and helps prevent SQL injection.

**Mental Model:**

-   Think of Sequelize as a translator between your JavaScript code and the SQL database. You define models (tables), and Sequelize handles the SQL behind the scenes.

## Key Concepts

-   **Models:** Represent tables in your database.
-   **Associations:** Define relationships (one-to-one, one-to-many, many-to-many).
-   **Migrations:** Manage schema changes over time.
-   **Querying:** Use model methods to read/write data.

## Best Practices

-   Use migrations to version-control your schema.
-   Validate data at both the model and application level.
-   Use transactions for multi-step operations.
-   Keep business logic out of models.

## Common Pitfalls

-   Not handling connection errors or pool limits.
-   Overusing eager loading (can cause performance issues).
-   Ignoring SQL injection risks in raw queries.

## Interview Q&A

**Q: What is an ORM and why use one?**
A: An ORM maps database tables to JavaScript objects, simplifying data access and reducing the need for raw SQL.

**Q: How do you define relationships in Sequelize?**
A: Use association methods like `hasMany`, `belongsTo`, and `belongsToMany` to define relationships between models.

**Q: How do you handle schema changes in production?**
A: Use Sequelize migrations to apply and track schema changes safely.

---

[[nodejs-roadmap|← Back to Node.js Roadmap]]

## Sequelize Setup and Configuration

### Installation

To get started with Sequelize, you need to install the core package along with the driver for your database:

```bash
# Install Sequelize core
npm install sequelize

# Install PostgreSQL driver
npm install pg pg-hstore

# For other databases
# npm install mysql2
# npm install mariadb
# npm install sqlite3
# npm install tedious # Microsoft SQL Server
```

### Basic Connection Setup

```javascript
// db/connection.js
const { Sequelize } = require("sequelize");

// Option 1: Passing connection parameters directly
const sequelize = new Sequelize("database", "username", "password", {
	host: "localhost",
	port: 5432,
	dialect: "postgres", // 'mysql', 'mariadb', 'sqlite', 'mssql'
	logging: false, // Set to console.log to see SQL queries
	pool: {
		max: 5, // Maximum number of connection in pool
		min: 0, // Minimum number of connection in pool
		acquire: 30000, // Maximum time, in milliseconds, that pool will try to get connection before throwing error
		idle: 10000, // Maximum time, in milliseconds, that a connection can be idle before being released
	},
});

// Option 2: Using connection URI
// const sequelize = new Sequelize('postgres://username:password@localhost:5432/database');

// Test the connection
async function testConnection() {
	try {
		await sequelize.authenticate();
		console.log("Connection has been established successfully.");
	} catch (error) {
		console.error("Unable to connect to the database:", error);
	}
}

testConnection();

module.exports = sequelize;
```

### Environment-Based Configuration

For production applications, it's best to use environment variables for database configuration:

```javascript
// config/database.js
require("dotenv").config();

module.exports = {
	development: {
		username: process.env.DB_USERNAME || "postgres",
		password: process.env.DB_PASSWORD || "postgres",
		database: process.env.DB_NAME || "app_development",
		host: process.env.DB_HOST || "localhost",
		port: process.env.DB_PORT || 5432,
		dialect: "postgres",
		logging: console.log,
	},
	test: {
		username: process.env.DB_USERNAME || "postgres",
		password: process.env.DB_PASSWORD || "postgres",
		database: process.env.DB_NAME || "app_test",
		host: process.env.DB_HOST || "localhost",
		port: process.env.DB_PORT || 5432,
		dialect: "postgres",
		logging: false,
	},
	production: {
		username: process.env.DB_USERNAME,
		password: process.env.DB_PASSWORD,
		database: process.env.DB_NAME,
		host: process.env.DB_HOST,
		port: process.env.DB_PORT,
		dialect: "postgres",
		logging: false,
		dialectOptions: {
			ssl: {
				require: true,
				rejectUnauthorized: false,
			},
		},
		pool: {
			max: 5,
			min: 0,
			acquire: 30000,
			idle: 10000,
		},
	},
};

// db/connection.js
const { Sequelize } = require("sequelize");
const env = process.env.NODE_ENV || "development";
const config = require("../config/database")[env];

const sequelize = new Sequelize(
	config.database,
	config.username,
	config.password,
	config
);

module.exports = sequelize;
```

## Defining Models and Associations

### Creating Models

Models in Sequelize represent tables in your database:

```javascript
// models/user.js
const { DataTypes } = require("sequelize");
const sequelize = require("../db/connection");

const User = sequelize.define(
	"User",
	{
		// Model attributes
		id: {
			type: DataTypes.UUID,
			defaultValue: DataTypes.UUIDV4,
			primaryKey: true,
		},
		firstName: {
			type: DataTypes.STRING,
			allowNull: false,
		},
		lastName: {
			type: DataTypes.STRING,
			allowNull: false,
		},
		email: {
			type: DataTypes.STRING,
			allowNull: false,
			unique: true,
			validate: {
				isEmail: true,
			},
		},
		password: {
			type: DataTypes.STRING,
			allowNull: false,
		},
		role: {
			type: DataTypes.ENUM("user", "admin"),
			defaultValue: "user",
		},
		isActive: {
			type: DataTypes.BOOLEAN,
			defaultValue: true,
		},
		lastLogin: {
			type: DataTypes.DATE,
		},
	},
	{
		// Model options
		tableName: "users", // Explicitly specify table name
		timestamps: true, // Enable createdAt and updatedAt
		paranoid: true, // Enable soft deletes (deletedAt)
		indexes: [{ fields: ["email"] }],
	}
);

module.exports = User;
```

### Defining Associations

Sequelize supports various types of associations between models:

```javascript
// models/index.js
const sequelize = require("../db/connection");
const User = require("./user");
const Post = require("./post");
const Comment = require("./comment");
const Tag = require("./tag");

// One-to-Many: A User has many Posts
User.hasMany(Post, {
	foreignKey: "userId",
	as: "posts",
	onDelete: "CASCADE",
});
Post.belongsTo(User, {
	foreignKey: "userId",
	as: "author",
});

// One-to-Many: A Post has many Comments
Post.hasMany(Comment, {
	foreignKey: "postId",
	as: "comments",
	onDelete: "CASCADE",
});
Comment.belongsTo(Post, {
	foreignKey: "postId",
	as: "post",
});

// Many-to-Many: Posts can have multiple Tags, and Tags can be on multiple Posts
Post.belongsToMany(Tag, {
	through: "PostTags",
	foreignKey: "postId",
	otherKey: "tagId",
	as: "tags",
});
Tag.belongsToMany(Post, {
	through: "PostTags",
	foreignKey: "tagId",
	otherKey: "postId",
	as: "posts",
});

// One-to-One: User has one Profile
User.hasOne(Profile, {
	foreignKey: "userId",
	as: "profile",
	onDelete: "CASCADE",
});
Profile.belongsTo(User, {
	foreignKey: "userId",
	as: "user",
});

module.exports = {
	sequelize,
	User,
	Post,
	Comment,
	Tag,
};
```

### Model Hooks (Lifecycle Events)

Hooks allow you to execute code before or after specific events:

```javascript
// models/user.js
const bcrypt = require("bcrypt");

const User = sequelize.define(
	"User",
	{
		// ... attributes
	},
	{
		hooks: {
			// Hash password before creating a user
			beforeCreate: async (user) => {
				if (user.password) {
					const salt = await bcrypt.genSalt(10);
					user.password = await bcrypt.hash(user.password, salt);
				}
			},
			// Hash password before updating a user
			beforeUpdate: async (user) => {
				if (user.changed("password")) {
					const salt = await bcrypt.genSalt(10);
					user.password = await bcrypt.hash(user.password, salt);
				}
			},
		},
	}
);

// Instance method to compare passwords
User.prototype.comparePassword = async function (candidatePassword) {
	return await bcrypt.compare(candidatePassword, this.password);
};
```

## CRUD Operations with Sequelize

### Creating Records

```javascript
// Create a single record
const createUser = async (userData) => {
	try {
		const user = await User.create({
			firstName: userData.firstName,
			lastName: userData.lastName,
			email: userData.email,
			password: userData.password,
		});
		return user;
	} catch (error) {
		console.error("Error creating user:", error);
		throw error;
	}
};

// Bulk create multiple records
const createManyUsers = async (usersData) => {
	try {
		const users = await User.bulkCreate(usersData, {
			validate: true, // Run validations for each record
		});
		return users;
	} catch (error) {
		console.error("Error bulk creating users:", error);
		throw error;
	}
};
```

### Reading Records

```javascript
// Find all records
const getAllUsers = async () => {
	try {
		const users = await User.findAll({
			attributes: ["id", "firstName", "lastName", "email"], // Select specific attributes
			where: { isActive: true }, // Filter by condition
		});
		return users;
	} catch (error) {
		console.error("Error fetching users:", error);
		throw error;
	}
};

// Find a single record by primary key
const getUserById = async (userId) => {
	try {
		const user = await User.findByPk(userId, {
			include: [
				{
					model: Post,
					as: "posts",
					include: [
						{ model: Comment, as: "comments" },
						{ model: Tag, as: "tags" },
					],
				},
				{ model: Profile, as: "profile" },
			],
		});
		return user;
	} catch (error) {
		console.error(`Error fetching user with ID ${userId}:`, error);
		throw error;
	}
};

// Find with complex conditions
const searchUsers = async (query) => {
	const { Op } = require("sequelize");

	try {
		const users = await User.findAll({
			where: {
				[Op.or]: [
					{ firstName: { [Op.iLike]: `%${query}%` } },
					{ lastName: { [Op.iLike]: `%${query}%` } },
					{ email: { [Op.iLike]: `%${query}%` } },
				],
				isActive: true,
			},
			order: [
				["lastName", "ASC"],
				["firstName", "ASC"],
			],
			limit: 20,
			offset: 0,
		});
		return users;
	} catch (error) {
		console.error(`Error searching users:`, error);
		throw error;
	}
};
```

### Updating Records

```javascript
// Update a single record
const updateUser = async (userId, userData) => {
	try {
		const [updatedRowsCount, updatedRows] = await User.update(userData, {
			where: { id: userId },
			returning: true, // Return the updated rows (PostgreSQL only)
		});

		if (updatedRowsCount === 0) {
			throw new Error(`User with ID ${userId} not found`);
		}

		return updatedRows[0];
	} catch (error) {
		console.error(`Error updating user with ID ${userId}:`, error);
		throw error;
	}
};

// Upsert (update or insert)
const upsertUser = async (userData) => {
	try {
		const [user, created] = await User.upsert(userData, {
			returning: true,
		});

		return { user, created };
	} catch (error) {
		console.error("Error upserting user:", error);
		throw error;
	}
};
```

### Deleting Records

```javascript
// Hard delete
const deleteUser = async (userId) => {
	try {
		const deletedRowsCount = await User.destroy({
			where: { id: userId },
			force: true, // Use force: true for hard delete when paranoid is enabled
		});

		if (deletedRowsCount === 0) {
			throw new Error(`User with ID ${userId} not found`);
		}

		return deletedRowsCount;
	} catch (error) {
		console.error(`Error deleting user with ID ${userId}:`, error);
		throw error;
	}
};

// Soft delete (when paranoid: true is set on the model)
const softDeleteUser = async (userId) => {
	try {
		const deletedRowsCount = await User.destroy({
			where: { id: userId },
		});

		if (deletedRowsCount === 0) {
			throw new Error(`User with ID ${userId} not found`);
		}

		return deletedRowsCount;
	} catch (error) {
		console.error(`Error soft deleting user with ID ${userId}:`, error);
		throw error;
	}
};

// Restore soft-deleted record
const restoreUser = async (userId) => {
	try {
		const restoredRowsCount = await User.restore({
			where: { id: userId },
		});

		if (restoredRowsCount === 0) {
			throw new Error(`User with ID ${userId} not found or not deleted`);
		}

		return restoredRowsCount;
	} catch (error) {
		console.error(`Error restoring user with ID ${userId}:`, error);
		throw error;
	}
};
```

## Database Synchronization and Migrations

### Synchronizing Models with the Database

For development, you can use Sequelize's sync method:

```javascript
// Warning: Not recommended for production
const syncDatabase = async () => {
	try {
		// Sync all models
		await sequelize.sync({ force: true }); // force: true drops tables before recreating
		console.log("Database synchronized");
	} catch (error) {
		console.error("Error synchronizing database:", error);
		throw error;
	}
};

// Safer alternative for development
const alterDatabase = async () => {
	try {
		// Sync all models, altering tables as needed
		await sequelize.sync({ alter: true });
		console.log("Database altered");
	} catch (error) {
		console.error("Error altering database:", error);
		throw error;
	}
};
```

### Using Sequelize CLI for Migrations

For production, use Sequelize CLI to create and run migrations:

```bash
# Install Sequelize CLI globally
npm install -g sequelize-cli

# Initialize Sequelize project
npx sequelize-cli init

# Create a migration
npx sequelize-cli migration:generate --name create-users-table

# Run migrations
npx sequelize-cli db:migrate

# Undo the most recent migration
npx sequelize-cli db:migrate:undo

# Undo all migrations
npx sequelize-cli db:migrate:undo:all
```

Example migration file:

```javascript
// migrations/YYYYMMDDHHMMSS-create-users-table.js
"use strict";

module.exports = {
	up: async (queryInterface, Sequelize) => {
		await queryInterface.createTable("users", {
			id: {
				type: Sequelize.UUID,
				defaultValue: Sequelize.UUIDV4,
				primaryKey: true,
			},
			firstName: {
				type: Sequelize.STRING,
				allowNull: false,
			},
			lastName: {
				type: Sequelize.STRING,
				allowNull: false,
			},
			email: {
				type: Sequelize.STRING,
				allowNull: false,
				unique: true,
			},
			password: {
				type: Sequelize.STRING,
				allowNull: false,
			},
			role: {
				type: Sequelize.ENUM("user", "admin"),
				defaultValue: "user",
			},
			isActive: {
				type: Sequelize.BOOLEAN,
				defaultValue: true,
			},
			lastLogin: {
				type: Sequelize.DATE,
			},
			createdAt: {
				type: Sequelize.DATE,
				allowNull: false,
			},
			updatedAt: {
				type: Sequelize.DATE,
				allowNull: false,
			},
			deletedAt: {
				type: Sequelize.DATE,
			},
		});

		// Add indexes
		await queryInterface.addIndex("users", ["email"]);
	},

	down: async (queryInterface, Sequelize) => {
		await queryInterface.dropTable("users");
	},
};
```

### Seeders for Test Data

Sequelize CLI also supports seeders for populating your database with test data:

```bash
# Create a seeder
npx sequelize-cli seed:generate --name demo-users

# Run seeders
npx sequelize-cli db:seed:all

# Undo the most recent seeder
npx sequelize-cli db:seed:undo

# Undo all seeders
npx sequelize-cli db:seed:undo:all
```

Example seeder file:

```javascript
// seeders/YYYYMMDDHHMMSS-demo-users.js
"use strict";
const bcrypt = require("bcrypt");
const { v4: uuidv4 } = require("uuid");

module.exports = {
	up: async (queryInterface, Sequelize) => {
		const hashedPassword = await bcrypt.hash("password123", 10);

		return queryInterface.bulkInsert("users", [
			{
				id: uuidv4(),
				firstName: "John",
				lastName: "Doe",
				email: "john@example.com",
				password: hashedPassword,
				role: "admin",
				isActive: true,
				createdAt: new Date(),
				updatedAt: new Date(),
			},
			{
				id: uuidv4(),
				firstName: "Jane",
				lastName: "Smith",
				email: "jane@example.com",
				password: hashedPassword,
				role: "user",
				isActive: true,
				createdAt: new Date(),
				updatedAt: new Date(),
			},
		]);
	},

	down: async (queryInterface, Sequelize) => {
		return queryInterface.bulkDelete("users", null, {});
	},
};
```

## Transactions

Sequelize supports transactions to ensure data integrity:

```javascript
// Using managed transactions
const transferFunds = async (fromAccountId, toAccountId, amount) => {
	try {
		const result = await sequelize.transaction(async (t) => {
			// Find accounts within the transaction
			const fromAccount = await Account.findByPk(fromAccountId, {
				transaction: t,
			});
			const toAccount = await Account.findByPk(toAccountId, {
				transaction: t,
			});

			if (!fromAccount || !toAccount) {
				throw new Error("One or both accounts not found");
			}

			if (fromAccount.balance < amount) {
				throw new Error("Insufficient funds");
			}

			// Update accounts within the transaction
			await fromAccount.decrement("balance", {
				by: amount,
				transaction: t,
			});
			await toAccount.increment("balance", {
				by: amount,
				transaction: t,
			});

			// Create transaction record
			await Transaction.create(
				{
					fromAccountId,
					toAccountId,
					amount,
					type: "transfer",
					status: "completed",
				},
				{ transaction: t }
			);

			// Return updated accounts
			return {
				fromAccount: await fromAccount.reload({ transaction: t }),
				toAccount: await toAccount.reload({ transaction: t }),
			};
		});

		return result;
	} catch (error) {
		console.error("Error transferring funds:", error);
		throw error;
	}
};
```

## Performance Optimization

### Eager Loading vs Lazy Loading

```javascript
// Eager loading (more efficient for related data)
const getUserWithPosts = async (userId) => {
	const user = await User.findByPk(userId, {
		include: [{ model: Post, as: "posts" }],
	});
	return user;
};

// Lazy loading (more flexible but can lead to N+1 query problem)
const getUserThenPosts = async (userId) => {
	const user = await User.findByPk(userId);
	const posts = await user.getPosts(); // Assuming association is set up
	return { user, posts };
};
```

### Raw Queries for Complex Operations

```javascript
const getPostStats = async () => {
	const [results] = await sequelize.query(
		`
    SELECT 
      p.id, 
      p.title, 
      COUNT(c.id) AS comment_count, 
      AVG(c.rating) AS avg_rating
    FROM posts p
    LEFT JOIN comments c ON p.id = c.post_id
    GROUP BY p.id, p.title
    ORDER BY comment_count DESC
    LIMIT 10
  `,
		{
			type: sequelize.QueryTypes.SELECT,
		}
	);

	return results;
};
```

By mastering Sequelize, you can efficiently work with relational databases in your Node.js applications, ensuring data integrity, performance, and maintainability.
