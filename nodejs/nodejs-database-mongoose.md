# MongoDB and Mongoose in Node.js

MongoDB is a popular NoSQL database, and Mongoose is an Object Document Mapper (ODM) that brings structure and validation to MongoDB documents. Mastering Mongoose is key for building scalable, maintainable Node.js applications with MongoDB.

## Conceptual Overview

**Why use MongoDB and Mongoose?**
MongoDB offers flexible, schema-less storage, while Mongoose adds schemas, validation, and powerful querying to keep your data consistent and your code organized.

**Mental Model:**

-   Think of MongoDB as a dynamic filing cabinet, and Mongoose as the set of rules that ensure every file (document) is complete and correctly formatted before it's stored.

## Key Concepts

-   **Schemas:** Define the structure and constraints of your documents.
-   **Models:** Interface for interacting with collections.
-   **Validation:** Enforce data integrity at the application level.
-   **Population:** Reference documents across collections (like SQL joins).

## Best Practices

-   Always define schemas, even though MongoDB is schema-less.
-   Use validation and default values to prevent bad data.
-   Index frequently queried fields for performance.
-   Handle connection errors and timeouts gracefully.

## Common Pitfalls

-   Relying solely on client-side validation.
-   Not handling schema migrations as your app evolves.
-   Overusing population (can cause performance issues).

## Interview Q&A

**Q: What is the difference between MongoDB and Mongoose?**
A: MongoDB is the database; Mongoose is an ODM that provides schemas, validation, and a structured API for working with MongoDB in Node.js.

**Q: How do you enforce data integrity in MongoDB?**
A: Use Mongoose schemas and validation, and consider MongoDB's built-in validation rules for critical data.

**Q: When would you choose MongoDB over SQL?**
A: When you need flexible schemas, rapid iteration, or to store unstructured or semi-structured data.

---

[[nodejs-roadmap|← Back to Node.js Roadmap]]

## Getting Started with Mongoose

### Installation

First, install Mongoose in your Node.js project:

```bash
npm install mongoose
```

### Connecting to MongoDB

```javascript
const mongoose = require("mongoose");

// Connect to MongoDB (local instance)
mongoose
	.connect("mongodb://localhost:27017/my_database")
	.then(() => console.log("MongoDB connected successfully"))
	.catch((err) => console.error("MongoDB connection error:", err));

// Connect to MongoDB Atlas (cloud)
mongoose
	.connect(
		"mongodb+srv://username:password@cluster0.mongodb.net/my_database?retryWrites=true&w=majority"
	)
	.then(() => console.log("MongoDB Atlas connected successfully"))
	.catch((err) => console.error("MongoDB Atlas connection error:", err));
```

### Connection Options

```javascript
// With connection options
mongoose
	.connect("mongodb://localhost:27017/my_database", {
		useNewUrlParser: true,
		useUnifiedTopology: true,
		serverSelectionTimeoutMS: 5000,
		socketTimeoutMS: 45000,
	})
	.then(() => console.log("MongoDB connected successfully"))
	.catch((err) => console.error("MongoDB connection error:", err));
```

## Defining Schemas and Models

### Basic Schema

```javascript
const mongoose = require("mongoose");
const { Schema } = mongoose;

// Define a schema
const userSchema = new Schema({
	name: String,
	email: String,
	password: String,
	age: Number,
	isActive: Boolean,
	createdAt: { type: Date, default: Date.now },
});

// Create a model from the schema
const User = mongoose.model("User", userSchema);

// Export the model
module.exports = User;
```

### Schema with Validation

```javascript
const mongoose = require("mongoose");
const { Schema } = mongoose;

const productSchema = new Schema({
	name: {
		type: String,
		required: [true, "Product name is required"],
		trim: true,
		minlength: [2, "Name must be at least 2 characters"],
		maxlength: [100, "Name cannot exceed 100 characters"],
	},
	price: {
		type: Number,
		required: [true, "Price is required"],
		min: [0, "Price cannot be negative"],
	},
	description: {
		type: String,
		trim: true,
		maxlength: [1000, "Description cannot exceed 1000 characters"],
	},
	category: {
		type: String,
		required: true,
		enum: {
			values: ["electronics", "clothing", "food", "books"],
			message: "{VALUE} is not a valid category",
		},
	},
	inStock: {
		type: Boolean,
		default: true,
	},
	quantity: {
		type: Number,
		default: 0,
		validate: {
			validator: function (value) {
				return value >= 0;
			},
			message: "Quantity cannot be negative",
		},
	},
	tags: [String],
	image: {
		type: String,
		default: "default.jpg",
	},
	ratings: [
		{
			user: {
				type: mongoose.Schema.Types.ObjectId,
				ref: "User",
			},
			rating: {
				type: Number,
				min: 1,
				max: 5,
			},
			review: String,
		},
	],
	createdAt: {
		type: Date,
		default: Date.now,
	},
	updatedAt: {
		type: Date,
		default: Date.now,
	},
});

const Product = mongoose.model("Product", productSchema);
module.exports = Product;
```

## CRUD Operations with Mongoose

### Create (Insert) Documents

```javascript
// Create a single document
const createUser = async () => {
	try {
		// Method 1: Create an instance and save
		const user = new User({
			name: "John Doe",
			email: "john@example.com",
			password: "password123",
			age: 30,
			isActive: true,
		});

		const savedUser = await user.save();
		console.log("User saved:", savedUser);

		// Method 2: Use create() method
		const anotherUser = await User.create({
			name: "Jane Smith",
			email: "jane@example.com",
			password: "password456",
			age: 25,
			isActive: true,
		});

		console.log("Another user created:", anotherUser);

		// Create multiple documents
		const users = await User.create([
			{ name: "Alice", email: "alice@example.com", age: 28 },
			{ name: "Bob", email: "bob@example.com", age: 32 },
		]);

		console.log("Multiple users created:", users);
	} catch (error) {
		console.error("Error creating user:", error.message);
	}
};
```

### Read (Query) Documents

```javascript
// Find all documents
const findAllUsers = async () => {
	try {
		const users = await User.find();
		console.log("All users:", users);
		return users;
	} catch (error) {
		console.error("Error finding users:", error.message);
	}
};

// Find with filters
const findActiveUsers = async () => {
	try {
		const activeUsers = await User.find({ isActive: true });
		console.log("Active users:", activeUsers);
		return activeUsers;
	} catch (error) {
		console.error("Error finding active users:", error.message);
	}
};

// Find a single document by ID
const findUserById = async (userId) => {
	try {
		const user = await User.findById(userId);
		if (!user) {
			console.log("User not found");
			return null;
		}
		console.log("Found user:", user);
		return user;
	} catch (error) {
		console.error("Error finding user by ID:", error.message);
	}
};

// Find a single document by a field
const findUserByEmail = async (email) => {
	try {
		const user = await User.findOne({ email });
		if (!user) {
			console.log("User not found");
			return null;
		}
		console.log("Found user:", user);
		return user;
	} catch (error) {
		console.error("Error finding user by email:", error.message);
	}
};

// Advanced queries
const advancedQueries = async () => {
	try {
		// Greater than
		const olderUsers = await User.find({ age: { $gt: 25 } });

		// Less than or equal to
		const youngUsers = await User.find({ age: { $lte: 30 } });

		// In array
		const specificUsers = await User.find({
			name: { $in: ["John Doe", "Jane Smith"] },
		});

		// Logical operators
		const complexQuery = await User.find({
			$or: [{ age: { $lt: 20 } }, { isActive: true }],
		});

		// Regular expression
		const nameSearch = await User.find({ name: /^J/ }); // Names starting with J

		console.log("Users with complex queries:", {
			olderUsers,
			youngUsers,
			specificUsers,
			complexQuery,
			nameSearch,
		});
	} catch (error) {
		console.error("Error with advanced queries:", error.message);
	}
};

// Selecting specific fields
const selectFields = async () => {
	try {
		// Include only name and email
		const users = await User.find().select("name email");

		// Exclude password (prefix with minus)
		const secureUsers = await User.find().select("-password");

		console.log("Selected fields:", {
			users,
			secureUsers,
		});
	} catch (error) {
		console.error("Error selecting fields:", error.message);
	}
};

// Sorting, limiting, and skipping
const paginateUsers = async (page = 1, limit = 10) => {
	try {
		const skip = (page - 1) * limit;

		const users = await User.find()
			.sort({ createdAt: -1 }) // Sort by creation date (newest first)
			.skip(skip)
			.limit(limit);

		const total = await User.countDocuments();

		console.log(`Page ${page} of users:`, users);
		console.log(`Showing ${users.length} of ${total} users`);

		return {
			users,
			totalPages: Math.ceil(total / limit),
			currentPage: page,
			totalUsers: total,
		};
	} catch (error) {
		console.error("Error paginating users:", error.message);
	}
};
```

### Update Documents

```javascript
// Update a single document by ID
const updateUserById = async (userId, updateData) => {
	try {
		// Method 1: findByIdAndUpdate
		const updatedUser = await User.findByIdAndUpdate(
			userId,
			updateData,
			{ new: true, runValidators: true } // Return updated document & run validators
		);

		if (!updatedUser) {
			console.log("User not found");
			return null;
		}

		console.log("Updated user:", updatedUser);
		return updatedUser;
	} catch (error) {
		console.error("Error updating user:", error.message);
	}
};

// Update multiple documents
const updateManyUsers = async (filter, updateData) => {
	try {
		const result = await User.updateMany(filter, updateData);

		console.log(`Updated ${result.modifiedCount} users`);
		return result;
	} catch (error) {
		console.error("Error updating multiple users:", error.message);
	}
};

// Update with operators
const updateWithOperators = async (userId) => {
	try {
		// Increment age by 1
		const user = await User.findByIdAndUpdate(
			userId,
			{ $inc: { age: 1 } },
			{ new: true }
		);

		console.log("User age incremented:", user);
		return user;
	} catch (error) {
		console.error("Error updating with operators:", error.message);
	}
};
```

### Delete Documents

```javascript
// Delete a single document by ID
const deleteUserById = async (userId) => {
	try {
		const deletedUser = await User.findByIdAndDelete(userId);

		if (!deletedUser) {
			console.log("User not found");
			return null;
		}

		console.log("Deleted user:", deletedUser);
		return deletedUser;
	} catch (error) {
		console.error("Error deleting user:", error.message);
	}
};

// Delete multiple documents
const deleteInactiveUsers = async () => {
	try {
		const result = await User.deleteMany({ isActive: false });

		console.log(`Deleted ${result.deletedCount} inactive users`);
		return result;
	} catch (error) {
		console.error("Error deleting inactive users:", error.message);
	}
};
```

## Relationships in Mongoose

### Referencing (Normalization)

```javascript
// Author schema
const authorSchema = new Schema({
	name: String,
	bio: String,
	website: String,
});

const Author = mongoose.model("Author", authorSchema);

// Book schema with reference to Author
const bookSchema = new Schema({
	title: String,
	author: {
		type: Schema.Types.ObjectId,
		ref: "Author",
	},
	publishedYear: Number,
	genres: [String],
});

const Book = mongoose.model("Book", bookSchema);

// Creating related documents
const createAuthorAndBooks = async () => {
	try {
		// Create an author
		const author = await Author.create({
			name: "J.K. Rowling",
			bio: "British author best known for the Harry Potter series",
			website: "https://www.jkrowling.com",
		});

		// Create books referencing the author
		const books = await Book.create([
			{
				title: "Harry Potter and the Philosopher's Stone",
				author: author._id,
				publishedYear: 1997,
				genres: ["Fantasy", "Young Adult"],
			},
			{
				title: "Harry Potter and the Chamber of Secrets",
				author: author._id,
				publishedYear: 1998,
				genres: ["Fantasy", "Young Adult"],
			},
		]);

		console.log("Author and books created:", { author, books });
	} catch (error) {
		console.error("Error creating author and books:", error.message);
	}
};

// Populating referenced documents
const findBooksWithAuthors = async () => {
	try {
		// Find books and populate author information
		const books = await Book.find().populate("author");

		// Selecting specific fields from the populated document
		const booksWithAuthorName = await Book.find().populate(
			"author",
			"name"
		);

		console.log("Books with author details:", books);
		console.log("Books with author name only:", booksWithAuthorName);
	} catch (error) {
		console.error("Error finding books with authors:", error.message);
	}
};
```

### Embedding (Denormalization)

```javascript
// Comment schema (embedded)
const commentSchema = new Schema({
	user: String,
	text: String,
	createdAt: { type: Date, default: Date.now },
});

// Post schema with embedded comments
const postSchema = new Schema({
	title: String,
	content: String,
	author: String,
	comments: [commentSchema],
	createdAt: { type: Date, default: Date.now },
});

const Post = mongoose.model("Post", postSchema);

// Creating a post with embedded comments
const createPostWithComments = async () => {
	try {
		const post = await Post.create({
			title: "Introduction to Mongoose",
			content: "Mongoose is an ODM for MongoDB...",
			author: "John Doe",
			comments: [
				{ user: "Alice", text: "Great post!" },
				{ user: "Bob", text: "Thanks for sharing." },
			],
		});

		console.log("Post with comments created:", post);
	} catch (error) {
		console.error("Error creating post with comments:", error.message);
	}
};

// Adding a comment to an existing post
const addCommentToPost = async (postId, comment) => {
	try {
		const post = await Post.findById(postId);

		if (!post) {
			console.log("Post not found");
			return null;
		}

		post.comments.push(comment);
		await post.save();

		console.log("Comment added to post:", post);
		return post;
	} catch (error) {
		console.error("Error adding comment to post:", error.message);
	}
};
```

## Middleware (Hooks)

Mongoose middleware (also called pre and post hooks) are functions that run during the execution of asynchronous functions.

```javascript
const userSchema = new Schema({
	name: String,
	email: String,
	password: String,
	createdAt: { type: Date, default: Date.now },
});

// Pre-save middleware (runs before save)
userSchema.pre("save", function (next) {
	// 'this' refers to the document being saved
	console.log(`Saving user: ${this.name}`);

	// Example: Update timestamp before saving
	this.updatedAt = Date.now();

	next(); // Call next to continue with the save operation
});

// Post-save middleware (runs after save)
userSchema.post("save", function (doc) {
	// 'doc' refers to the saved document
	console.log(`User saved: ${doc.name}`);
});

// Pre-find middleware
userSchema.pre("find", function () {
	// 'this' refers to the query object
	console.log("Find operation about to execute");
	this.startTime = Date.now();
});

// Post-find middleware
userSchema.post("find", function (docs) {
	console.log(`Find operation executed in ${Date.now() - this.startTime}ms`);
});

// Pre-remove middleware
userSchema.pre("remove", function (next) {
	console.log(`About to remove user: ${this.name}`);
	next();
});

const User = mongoose.model("User", userSchema);
```

### Password Hashing Example

```javascript
const bcrypt = require("bcryptjs");

const userSchema = new Schema({
	name: String,
	email: {
		type: String,
		required: true,
		unique: true,
		lowercase: true,
	},
	password: {
		type: String,
		required: true,
		minlength: 8,
	},
});

// Hash password before saving
userSchema.pre("save", async function (next) {
	// Only hash the password if it's modified (or new)
	if (!this.isModified("password")) return next();

	try {
		// Generate a salt
		const salt = await bcrypt.genSalt(10);

		// Hash the password along with the new salt
		this.password = await bcrypt.hash(this.password, salt);
		next();
	} catch (error) {
		next(error);
	}
});

// Method to compare passwords
userSchema.methods.comparePassword = async function (candidatePassword) {
	return await bcrypt.compare(candidatePassword, this.password);
};

const User = mongoose.model("User", userSchema);
```

## Instance and Static Methods

```javascript
// Instance method (available on document instances)
userSchema.methods.getFullName = function () {
	return `${this.firstName} ${this.lastName}`;
};

userSchema.methods.getPublicProfile = function () {
	const userObject = this.toObject();

	// Remove sensitive fields
	delete userObject.password;
	delete userObject.__v;

	return userObject;
};

// Static method (available on the model)
userSchema.statics.findByEmail = function (email) {
	return this.findOne({ email });
};

userSchema.statics.findActive = function () {
	return this.find({ isActive: true });
};

const User = mongoose.model("User", userSchema);

// Using instance methods
const user = await User.findById(userId);
const fullName = user.getFullName();
const publicProfile = user.getPublicProfile();

// Using static methods
const foundUser = await User.findByEmail("john@example.com");
const activeUsers = await User.findActive();
```

## Virtuals

Virtuals are document properties that are not stored in MongoDB but can be accessed like regular properties.

```javascript
const userSchema = new Schema({
	firstName: String,
	lastName: String,
	email: String,
	birthDate: Date,
});

// Virtual property for fullName
userSchema.virtual("fullName").get(function () {
	return `${this.firstName} ${this.lastName}`;
});

// Virtual property with setter
userSchema.virtual("fullName").set(function (name) {
	const parts = name.split(" ");
	this.firstName = parts[0];
	this.lastName = parts[1];
});

// Virtual property for age
userSchema.virtual("age").get(function () {
	if (!this.birthDate) return null;

	const today = new Date();
	const birthDate = this.birthDate;
	let age = today.getFullYear() - birthDate.getFullYear();

	// Adjust age if birthday hasn't occurred yet this year
	const monthDiff = today.getMonth() - birthDate.getMonth();
	if (
		monthDiff < 0 ||
		(monthDiff === 0 && today.getDate() < birthDate.getDate())
	) {
		age--;
	}

	return age;
});

// Include virtuals when converting to JSON
userSchema.set("toJSON", { virtuals: true });
userSchema.set("toObject", { virtuals: true });

const User = mongoose.model("User", userSchema);
```

## Transactions

For operations that require atomicity across multiple documents:

```javascript
const mongoose = require("mongoose");

// Models
const Account = mongoose.model(
	"Account",
	new mongoose.Schema({
		name: String,
		balance: Number,
	})
);

// Function to transfer money between accounts
const transferMoney = async (fromAccountId, toAccountId, amount) => {
	// Start a session
	const session = await mongoose.startSession();

	try {
		// Start a transaction
		session.startTransaction();

		// Find the source account and update its balance
		const fromAccount = await Account.findById(fromAccountId, null, {
			session,
		});
		if (!fromAccount || fromAccount.balance < amount) {
			throw new Error("Insufficient funds or account not found");
		}

		fromAccount.balance -= amount;
		await fromAccount.save({ session });

		// Find the destination account and update its balance
		const toAccount = await Account.findById(toAccountId, null, {
			session,
		});
		if (!toAccount) {
			throw new Error("Destination account not found");
		}

		toAccount.balance += amount;
		await toAccount.save({ session });

		// Commit the transaction
		await session.commitTransaction();
		session.endSession();

		return { fromAccount, toAccount };
	} catch (error) {
		// Abort transaction on error
		await session.abortTransaction();
		session.endSession();
		throw error;
	}
};
```

## Indexes

```javascript
const userSchema = new Schema({
	name: String,
	email: {
		type: String,
		unique: true, // Creates a unique index
	},
	createdAt: Date,
});

// Single field index
userSchema.index({ name: 1 }); // 1 for ascending, -1 for descending

// Compound index
userSchema.index({ createdAt: -1, name: 1 });

// Text index
userSchema.index({ description: "text" });

// TTL index (documents expire after 1 day)
userSchema.index({ createdAt: 1 }, { expireAfterSeconds: 86400 });

const User = mongoose.model("User", userSchema);

// Creating indexes programmatically
async function createIndexes() {
	try {
		await User.createIndexes();
		console.log("Indexes created successfully");
	} catch (error) {
		console.error("Error creating indexes:", error);
	}
}
```

## Error Handling

```javascript
// Connect with error handling
mongoose
	.connect("mongodb://localhost:27017/my_database")
	.then(() => console.log("MongoDB connected successfully"))
	.catch((err) => {
		console.error("MongoDB connection error:", err);
		process.exit(1); // Exit with failure
	});

// Handle connection events
mongoose.connection.on("connected", () => {
	console.log("Mongoose connected to the database");
});

mongoose.connection.on("error", (err) => {
	console.error("Mongoose connection error:", err);
});

mongoose.connection.on("disconnected", () => {
	console.log("Mongoose disconnected from the database");
});

// Graceful shutdown
process.on("SIGINT", async () => {
	await mongoose.connection.close();
	console.log("Mongoose connection closed due to application termination");
	process.exit(0);
});
```

Mongoose provides a powerful and flexible way to interact with MongoDB in your Node.js applications. By leveraging its features like schemas, validation, middleware, and relationships, you can build robust and maintainable applications with structured data models.
