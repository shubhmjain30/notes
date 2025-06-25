# Building RESTful APIs with Express.js

RESTful APIs (Representational State Transfer) provide a standardized approach to building web services. Express.js is an excellent framework for creating robust, scalable RESTful APIs.

[[Node.js Express.js Roadmap|← Back to Node.js Roadmap]]

## RESTful API Principles

REST is an architectural style with the following key principles:

1. **Resource-Based**: Resources are identified by URLs (e.g., `/users`, `/products`)
2. **HTTP Methods**: Use standard HTTP methods for operations (GET, POST, PUT, DELETE)
3. **Stateless**: Each request contains all information needed to complete it
4. **Uniform Interface**: Consistent resource identification and manipulation
5. **Client-Server Architecture**: Separation of concerns between client and server
6. **Cacheable**: Responses should define themselves as cacheable when applicable

## Setting Up a RESTful API

### Project Structure

A well-organized project structure helps maintain your API as it grows:

```
api-project/
├── app.js                  # Entry point
├── config/                 # Configuration files
│   └── db.js               # Database configuration
├── controllers/            # Request handlers
│   └── productController.js
├── models/                 # Data models
│   └── Product.js
├── routes/                 # Route definitions
│   └── productRoutes.js
├── middleware/             # Custom middleware
│   ├── auth.js
│   └── errorHandler.js
├── utils/                  # Utility functions
│   └── apiFeatures.js
└── package.json
```

### Basic Setup

```javascript
// app.js
const express = require("express");
const productRoutes = require("./routes/productRoutes");
const errorHandler = require("./middleware/errorHandler");

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(express.json()); // Parse JSON request bodies

// Routes
app.use("/api/v1/products", productRoutes);

// Error handling middleware (should be last)
app.use(errorHandler);

app.listen(PORT, () => {
	console.log(`Server running on port ${PORT}`);
});
```

## Implementing CRUD Operations

### Model Definition

First, define your data model (using Mongoose in this example):

```javascript
// models/Product.js
const mongoose = require("mongoose");

const productSchema = new mongoose.Schema({
	name: {
		type: String,
		required: [true, "A product must have a name"],
		trim: true,
		maxlength: [100, "A product name cannot exceed 100 characters"],
	},
	price: {
		type: Number,
		required: [true, "A product must have a price"],
	},
	description: {
		type: String,
		trim: true,
	},
	category: {
		type: String,
		required: [true, "A product must have a category"],
		enum: {
			values: ["electronics", "clothing", "food", "books"],
			message: "Category must be: electronics, clothing, food, or books",
		},
	},
	inStock: {
		type: Boolean,
		default: true,
	},
	createdAt: {
		type: Date,
		default: Date.now(),
	},
});

const Product = mongoose.model("Product", productSchema);

module.exports = Product;
```

### Controller Functions

Create controller functions to handle CRUD operations:

```javascript
// controllers/productController.js
const Product = require("../models/Product");

// GET all products
exports.getAllProducts = async (req, res) => {
	try {
		const products = await Product.find();

		res.status(200).json({
			status: "success",
			results: products.length,
			data: {
				products,
			},
		});
	} catch (err) {
		res.status(500).json({
			status: "error",
			message: err.message,
		});
	}
};

// GET a single product
exports.getProduct = async (req, res) => {
	try {
		const product = await Product.findById(req.params.id);

		if (!product) {
			return res.status(404).json({
				status: "fail",
				message: "Product not found",
			});
		}

		res.status(200).json({
			status: "success",
			data: {
				product,
			},
		});
	} catch (err) {
		res.status(500).json({
			status: "error",
			message: err.message,
		});
	}
};

// CREATE a new product
exports.createProduct = async (req, res) => {
	try {
		const newProduct = await Product.create(req.body);

		res.status(201).json({
			status: "success",
			data: {
				product: newProduct,
			},
		});
	} catch (err) {
		res.status(400).json({
			status: "fail",
			message: err.message,
		});
	}
};

// UPDATE a product
exports.updateProduct = async (req, res) => {
	try {
		const product = await Product.findByIdAndUpdate(
			req.params.id,
			req.body,
			{
				new: true, // Return the updated document
				runValidators: true, // Run validators on update
			}
		);

		if (!product) {
			return res.status(404).json({
				status: "fail",
				message: "Product not found",
			});
		}

		res.status(200).json({
			status: "success",
			data: {
				product,
			},
		});
	} catch (err) {
		res.status(400).json({
			status: "fail",
			message: err.message,
		});
	}
};

// DELETE a product
exports.deleteProduct = async (req, res) => {
	try {
		const product = await Product.findByIdAndDelete(req.params.id);

		if (!product) {
			return res.status(404).json({
				status: "fail",
				message: "Product not found",
			});
		}

		res.status(204).json({
			status: "success",
			data: null,
		});
	} catch (err) {
		res.status(500).json({
			status: "error",
			message: err.message,
		});
	}
};
```

### Routes

Define routes that map to controller functions:

```javascript
// routes/productRoutes.js
const express = require("express");
const productController = require("../controllers/productController");
const router = express.Router();

router
	.route("/")
	.get(productController.getAllProducts)
	.post(productController.createProduct);

router
	.route("/:id")
	.get(productController.getProduct)
	.patch(productController.updateProduct)
	.delete(productController.deleteProduct);

module.exports = router;
```

## Advanced API Features

### Query Parameters for Filtering, Sorting, and Pagination

Create a utility class to handle advanced API features:

```javascript
// utils/apiFeatures.js
class APIFeatures {
	constructor(query, queryString) {
		this.query = query;
		this.queryString = queryString;
	}

	filter() {
		// 1) Basic filtering
		const queryObj = { ...this.queryString };
		const excludedFields = ["page", "sort", "limit", "fields"];
		excludedFields.forEach((field) => delete queryObj[field]);

		// 2) Advanced filtering (e.g., gte, gt, lte, lt)
		let queryStr = JSON.stringify(queryObj);
		queryStr = queryStr.replace(
			/\b(gte|gt|lte|lt)\b/g,
			(match) => `$${match}`
		);

		this.query = this.query.find(JSON.parse(queryStr));
		return this;
	}

	sort() {
		if (this.queryString.sort) {
			const sortBy = this.queryString.sort.split(",").join(" ");
			this.query = this.query.sort(sortBy);
		} else {
			// Default sort by creation date
			this.query = this.query.sort("-createdAt");
		}
		return this;
	}

	limitFields() {
		if (this.queryString.fields) {
			const fields = this.queryString.fields.split(",").join(" ");
			this.query = this.query.select(fields);
		} else {
			// Exclude the MongoDB internal field
			this.query = this.query.select("-__v");
		}
		return this;
	}

	paginate() {
		const page = parseInt(this.queryString.page, 10) || 1;
		const limit = parseInt(this.queryString.limit, 10) || 100;
		const skip = (page - 1) * limit;

		this.query = this.query.skip(skip).limit(limit);
		return this;
	}
}

module.exports = APIFeatures;
```

Update the `getAllProducts` controller to use these features:

```javascript
// controllers/productController.js
const APIFeatures = require("../utils/apiFeatures");

exports.getAllProducts = async (req, res) => {
	try {
		// Execute query with features
		const features = new APIFeatures(Product.find(), req.query)
			.filter()
			.sort()
			.limitFields()
			.paginate();

		const products = await features.query;

		// Send response
		res.status(200).json({
			status: "success",
			results: products.length,
			data: {
				products,
			},
		});
	} catch (err) {
		res.status(500).json({
			status: "error",
			message: err.message,
		});
	}
};
```

### Error Handling Middleware

Create a centralized error handling middleware:

```javascript
// middleware/errorHandler.js
const errorHandler = (err, req, res, next) => {
	err.statusCode = err.statusCode || 500;
	err.status = err.status || "error";

	// Development error response (with details)
	if (process.env.NODE_ENV === "development") {
		return res.status(err.statusCode).json({
			status: err.status,
			error: err,
			message: err.message,
			stack: err.stack,
		});
	}

	// Production error response (without sensitive details)
	if (err.isOperational) {
		// Known, operational errors
		return res.status(err.statusCode).json({
			status: err.status,
			message: err.message,
		});
	}

	// Programming or unknown errors: don't leak error details
	console.error("ERROR 💥", err);
	return res.status(500).json({
		status: "error",
		message: "Something went wrong",
	});
};

module.exports = errorHandler;
```

## API Authentication

Implement JWT-based authentication:

```javascript
// controllers/authController.js
const jwt = require("jsonwebtoken");
const User = require("../models/User");

// Create and send JWT token
const signToken = (id) => {
	return jwt.sign({ id }, process.env.JWT_SECRET, {
		expiresIn: process.env.JWT_EXPIRES_IN,
	});
};

exports.signup = async (req, res) => {
	try {
		const newUser = await User.create({
			name: req.body.name,
			email: req.body.email,
			password: req.body.password,
			passwordConfirm: req.body.passwordConfirm,
		});

		const token = signToken(newUser._id);

		res.status(201).json({
			status: "success",
			token,
			data: {
				user: newUser,
			},
		});
	} catch (err) {
		res.status(400).json({
			status: "fail",
			message: err.message,
		});
	}
};

exports.login = async (req, res) => {
	try {
		const { email, password } = req.body;

		// Check if email and password exist
		if (!email || !password) {
			return res.status(400).json({
				status: "fail",
				message: "Please provide email and password",
			});
		}

		// Check if user exists && password is correct
		const user = await User.findOne({ email }).select("+password");

		if (!user || !(await user.correctPassword(password, user.password))) {
			return res.status(401).json({
				status: "fail",
				message: "Incorrect email or password",
			});
		}

		// If everything is ok, send token to client
		const token = signToken(user._id);

		res.status(200).json({
			status: "success",
			token,
		});
	} catch (err) {
		res.status(400).json({
			status: "fail",
			message: err.message,
		});
	}
};

// Protect routes middleware
exports.protect = async (req, res, next) => {
	try {
		// 1) Get token and check if it exists
		let token;
		if (
			req.headers.authorization &&
			req.headers.authorization.startsWith("Bearer")
		) {
			token = req.headers.authorization.split(" ")[1];
		}

		if (!token) {
			return res.status(401).json({
				status: "fail",
				message: "You are not logged in. Please log in to get access",
			});
		}

		// 2) Verify token
		const decoded = jwt.verify(token, process.env.JWT_SECRET);

		// 3) Check if user still exists
		const currentUser = await User.findById(decoded.id);
		if (!currentUser) {
			return res.status(401).json({
				status: "fail",
				message: "The user belonging to this token no longer exists",
			});
		}

		// 4) Check if user changed password after the token was issued
		if (currentUser.changedPasswordAfter(decoded.iat)) {
			return res.status(401).json({
				status: "fail",
				message: "User recently changed password! Please log in again",
			});
		}

		// Grant access to protected route
		req.user = currentUser;
		next();
	} catch (err) {
		res.status(401).json({
			status: "fail",
			message: "Authentication failed",
		});
	}
};
```

Apply the authentication middleware to protect routes:

```javascript
// routes/productRoutes.js
const express = require("express");
const productController = require("../controllers/productController");
const authController = require("../controllers/authController");
const router = express.Router();

router.route("/").get(productController.getAllProducts).post(
	authController.protect, // Protect this route
	productController.createProduct
);

router
	.route("/:id")
	.get(productController.getProduct)
	.patch(
		authController.protect, // Protect this route
		productController.updateProduct
	)
	.delete(
		authController.protect, // Protect this route
		productController.deleteProduct
	);

module.exports = router;
```

## API Documentation

Document your API using tools like Swagger/OpenAPI:

```javascript
// app.js
const swaggerJsDoc = require("swagger-jsdoc");
const swaggerUi = require("swagger-ui-express");

// Swagger configuration
const swaggerOptions = {
	definition: {
		openapi: "3.0.0",
		info: {
			title: "Product API",
			version: "1.0.0",
			description: "A simple Express Product API",
		},
		servers: [
			{
				url: "http://localhost:3000/api/v1",
			},
		],
	},
	apis: ["./routes/*.js"], // Path to the API routes
};

const swaggerDocs = swaggerJsDoc(swaggerOptions);
app.use("/api-docs", swaggerUi.serve, swaggerUi.setup(swaggerDocs));
```

Add JSDoc comments to your routes for Swagger:

```javascript
// routes/productRoutes.js
/**
 * @swagger
 * /products:
 *   get:
 *     summary: Returns all products
 *     tags: [Products]
 *     responses:
 *       200:
 *         description: The list of products
 *         content:
 *           application/json:
 *             schema:
 *               type: object
 *               properties:
 *                 status:
 *                   type: string
 *                   example: success
 *                 results:
 *                   type: integer
 *                   example: 10
 *                 data:
 *                   type: object
 *                   properties:
 *                     products:
 *                       type: array
 *                       items:
 *                         $ref: '#/components/schemas/Product'
 */
router.route("/").get(productController.getAllProducts);
```

By following these practices, you can build robust, scalable, and maintainable RESTful APIs with Express.js that adhere to industry standards and best practices.
