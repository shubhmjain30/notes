# Building RESTful APIs with Express.js

REST (Representational State Transfer) is an architectural style for designing networked applications. Express.js excels at building RESTful APIs due to its simplicity and flexibility.

## Understanding REST Principles

**Problem:** How do we design web services that are scalable, maintainable, and intuitive to use?

**Theory:** REST provides a set of architectural constraints that, when followed, result in APIs that are easy to understand and use.

### Core REST Principles

1. **Resource-Based:** Everything is a resource identified by URLs
2. **HTTP Methods:** Use standard verbs for operations
3. **Stateless:** Each request contains all necessary information
4. **Uniform Interface:** Consistent resource identification and manipulation
5. **Client-Server:** Separation of concerns
6. **Cacheable:** Responses should define caching behavior

### HTTP Methods and Their Purposes

| Method | Purpose                 | Example             | Idempotent |
| ------ | ----------------------- | ------------------- | ---------- |
| GET    | Retrieve resource(s)    | `GET /users/123`    | Yes        |
| POST   | Create new resource     | `POST /users`       | No         |
| PUT    | Replace entire resource | `PUT /users/123`    | Yes        |
| PATCH  | Partial update          | `PATCH /users/123`  | No         |
| DELETE | Remove resource         | `DELETE /users/123` | Yes        |

## Building a Complete RESTful API

### Project Structure for Scale

```
book-api/
├── app.js                 # Application entry point
├── config/
│   ├── database.js        # Database configuration
│   └── environment.js     # Environment variables
├── controllers/           # Business logic
│   ├── bookController.js
│   └── authorController.js
├── models/                # Data models
│   ├── Book.js
│   └── Author.js
├── routes/                # Route definitions
│   ├── index.js
│   ├── books.js
│   └── authors.js
├── middleware/            # Custom middleware
│   ├── auth.js
│   ├── validation.js
│   └── errorHandler.js
├── utils/                 # Utility functions
│   └── apiResponse.js
└── tests/                 # Test files
    └── books.test.js
```

### Step-by-Step API Implementation

#### 1. Basic Setup

```js
// app.js
const express = require("express");
const cors = require("cors");
const morgan = require("morgan");
const helmet = require("helmet");

const app = express();

// Middleware
app.use(helmet()); // Security headers
app.use(cors()); // Enable CORS
app.use(morgan("combined")); // Logging
app.use(express.json({ limit: "10mb" })); // Parse JSON bodies
app.use(express.urlencoded({ extended: true })); // Parse URL-encoded bodies

// Routes
app.use("/api/books", require("./routes/books"));
app.use("/api/authors", require("./routes/authors"));

// Global error handler
app.use(require("./middleware/errorHandler"));

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
	console.log(`Server running on port ${PORT}`);
});
```

#### 2. Data Model (In-Memory for Learning)

```js
// models/Book.js
class Book {
	constructor(title, author, isbn, publishedYear) {
		this.id = Date.now().toString(); // Simple ID generation
		this.title = title;
		this.author = author;
		this.isbn = isbn;
		this.publishedYear = publishedYear;
		this.createdAt = new Date();
		this.updatedAt = new Date();
	}
}

// In-memory storage (use database in production)
let books = [
	new Book(
		"The Great Gatsby",
		"F. Scott Fitzgerald",
		"978-0-7432-7356-5",
		1925
	),
	new Book("To Kill a Mockingbird", "Harper Lee", "978-0-06-112008-4", 1960),
];

module.exports = { Book, books };
```

#### 3. Controller Logic

```js
// controllers/bookController.js
const { Book, books } = require("../models/Book");
const { ApiResponse } = require("../utils/apiResponse");

class BookController {
	// GET /api/books - List all books
	static getAllBooks(req, res) {
		try {
			const { page = 1, limit = 10, search } = req.query;

			let filteredBooks = books;

			// Simple search functionality
			if (search) {
				filteredBooks = books.filter(
					(book) =>
						book.title
							.toLowerCase()
							.includes(search.toLowerCase()) ||
						book.author.toLowerCase().includes(search.toLowerCase())
				);
			}

			// Pagination
			const startIndex = (page - 1) * limit;
			const endIndex = page * limit;
			const paginatedBooks = filteredBooks.slice(startIndex, endIndex);

			res.json(
				new ApiResponse(200, "Books retrieved successfully", {
					books: paginatedBooks,
					pagination: {
						page: parseInt(page),
						limit: parseInt(limit),
						total: filteredBooks.length,
						pages: Math.ceil(filteredBooks.length / limit),
					},
				})
			);
		} catch (error) {
			res.status(500).json(new ApiResponse(500, "Internal server error"));
		}
	}

	// GET /api/books/:id - Get single book
	static getBookById(req, res) {
		try {
			const book = books.find((b) => b.id === req.params.id);

			if (!book) {
				return res
					.status(404)
					.json(new ApiResponse(404, "Book not found"));
			}

			res.json(new ApiResponse(200, "Book retrieved successfully", book));
		} catch (error) {
			res.status(500).json(new ApiResponse(500, "Internal server error"));
		}
	}

	// POST /api/books - Create new book
	static createBook(req, res) {
		try {
			const { title, author, isbn, publishedYear } = req.body;

			// Check if book with same ISBN already exists
			const existingBook = books.find((b) => b.isbn === isbn);
			if (existingBook) {
				return res
					.status(409)
					.json(
						new ApiResponse(
							409,
							"Book with this ISBN already exists"
						)
					);
			}

			const newBook = new Book(title, author, isbn, publishedYear);
			books.push(newBook);

			res.status(201).json(
				new ApiResponse(201, "Book created successfully", newBook)
			);
		} catch (error) {
			res.status(500).json(new ApiResponse(500, "Internal server error"));
		}
	}

	// PUT /api/books/:id - Update entire book
	static updateBook(req, res) {
		try {
			const bookIndex = books.findIndex((b) => b.id === req.params.id);

			if (bookIndex === -1) {
				return res
					.status(404)
					.json(new ApiResponse(404, "Book not found"));
			}

			const { title, author, isbn, publishedYear } = req.body;

			// Update the book
			books[bookIndex] = {
				...books[bookIndex],
				title,
				author,
				isbn,
				publishedYear,
				updatedAt: new Date(),
			};

			res.json(
				new ApiResponse(
					200,
					"Book updated successfully",
					books[bookIndex]
				)
			);
		} catch (error) {
			res.status(500).json(new ApiResponse(500, "Internal server error"));
		}
	}

	// DELETE /api/books/:id - Delete book
	static deleteBook(req, res) {
		try {
			const bookIndex = books.findIndex((b) => b.id === req.params.id);

			if (bookIndex === -1) {
				return res
					.status(404)
					.json(new ApiResponse(404, "Book not found"));
			}

			const deletedBook = books.splice(bookIndex, 1)[0];

			res.json(
				new ApiResponse(200, "Book deleted successfully", deletedBook)
			);
		} catch (error) {
			res.status(500).json(new ApiResponse(500, "Internal server error"));
		}
	}
}

module.exports = BookController;
```

#### 4. Input Validation Middleware

```js
// middleware/validation.js
const { body, param, query, validationResult } = require("express-validator");

class ValidationMiddleware {
	// Validation rules for creating a book
	static validateBookCreation() {
		return [
			body("title")
				.trim()
				.notEmpty()
				.withMessage("Title is required")
				.isLength({ min: 1, max: 200 })
				.withMessage("Title must be between 1 and 200 characters"),

			body("author")
				.trim()
				.notEmpty()
				.withMessage("Author is required")
				.isLength({ min: 1, max: 100 })
				.withMessage("Author must be between 1 and 100 characters"),

			body("isbn")
				.trim()
				.notEmpty()
				.withMessage("ISBN is required")
				.matches(
					/^(?:ISBN(?:-1[03])?:? )?(?=[0-9X]{10}$|(?=(?:[0-9]+[- ]){3})[- 0-9X]{13}$|97[89][0-9]{10}$|(?=(?:[0-9]+[- ]){4})[- 0-9]{17}$)(?:97[89][- ]?)?[0-9]{1,5}[- ]?[0-9]+[- ]?[0-9]+[- ]?[0-9X]$/
				)
				.withMessage("Invalid ISBN format"),

			body("publishedYear")
				.isInt({ min: 1000, max: new Date().getFullYear() })
				.withMessage("Published year must be a valid year"),
		];
	}

	// Validation rules for book ID parameter
	static validateBookId() {
		return [param("id").notEmpty().withMessage("Book ID is required")];
	}

	// Validation rules for query parameters
	static validateBookQuery() {
		return [
			query("page")
				.optional()
				.isInt({ min: 1 })
				.withMessage("Page must be a positive integer"),

			query("limit")
				.optional()
				.isInt({ min: 1, max: 100 })
				.withMessage("Limit must be between 1 and 100"),

			query("search")
				.optional()
				.isLength({ min: 1, max: 50 })
				.withMessage("Search term must be between 1 and 50 characters"),
		];
	}

	// Middleware to handle validation errors
	static handleValidationErrors(req, res, next) {
		const errors = validationResult(req);

		if (!errors.isEmpty()) {
			return res.status(400).json({
				success: false,
				message: "Validation failed",
				errors: errors.array().map((error) => ({
					field: error.param,
					message: error.msg,
					value: error.value,
				})),
			});
		}

		next();
	}
}

module.exports = ValidationMiddleware;
```

#### 5. Routes Definition

```js
// routes/books.js
const express = require("express");
const router = express.Router();
const BookController = require("../controllers/bookController");
const ValidationMiddleware = require("../middleware/validation");

// GET /api/books - List all books with pagination and search
router.get(
	"/",
	ValidationMiddleware.validateBookQuery(),
	ValidationMiddleware.handleValidationErrors,
	BookController.getAllBooks
);

// GET /api/books/:id - Get single book
router.get(
	"/:id",
	ValidationMiddleware.validateBookId(),
	ValidationMiddleware.handleValidationErrors,
	BookController.getBookById
);

// POST /api/books - Create new book
router.post(
	"/",
	ValidationMiddleware.validateBookCreation(),
	ValidationMiddleware.handleValidationErrors,
	BookController.createBook
);

// PUT /api/books/:id - Update book
router.put(
	"/:id",
	ValidationMiddleware.validateBookId(),
	ValidationMiddleware.validateBookCreation(),
	ValidationMiddleware.handleValidationErrors,
	BookController.updateBook
);

// DELETE /api/books/:id - Delete book
router.delete(
	"/:id",
	ValidationMiddleware.validateBookId(),
	ValidationMiddleware.handleValidationErrors,
	BookController.deleteBook
);

module.exports = router;
```

#### 6. Utility Classes

```js
// utils/apiResponse.js
class ApiResponse {
	constructor(statusCode, message, data = null, errors = null) {
		this.success = statusCode < 400;
		this.statusCode = statusCode;
		this.message = message;

		if (data !== null) {
			this.data = data;
		}

		if (errors !== null) {
			this.errors = errors;
		}

		this.timestamp = new Date().toISOString();
	}
}

module.exports = { ApiResponse };
```

## API Usage Examples

### Creating a Book

```bash
curl -X POST http://localhost:3000/api/books \
  -H "Content-Type: application/json" \
  -d '{
    "title": "1984",
    "author": "George Orwell",
    "isbn": "978-0-452-28423-4",
    "publishedYear": 1949
  }'
```

### Getting Books with Pagination

```bash
curl "http://localhost:3000/api/books?page=1&limit=5&search=gatsby"
```

## Best Practices for RESTful APIs

1. **Use Nouns for Resources:** `/books` not `/getBooks`
2. **HTTP Status Codes:** Use appropriate codes (200, 201, 400, 404, 500)
3. **Consistent Response Format:** Standard structure for all responses
4. **Input Validation:** Validate all incoming data
5. **Error Handling:** Provide meaningful error messages
6. **Pagination:** For large datasets
7. **Versioning:** Plan for API evolution (`/api/v1/books`)
8. **Documentation:** Use tools like Swagger/OpenAPI

This comprehensive example demonstrates how Express.js simplifies RESTful API development while maintaining flexibility and scalability.
