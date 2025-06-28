# 🌱 Stage 1 — Foundational Web & JavaScript Knowledge

Before diving into React, you need a solid foundation in web technologies and modern JavaScript. This knowledge is essential for understanding React's concepts and writing efficient code.

[[reactjs-roadmap|← Back to React.js Roadmap]]

## Semantic HTML and Accessibility

**Problem:** How do we create web interfaces that are both meaningful to browsers and accessible to all users?

**Theory:** Semantic HTML provides structure and meaning to content, helping browsers, search engines, and assistive technologies understand your page. In React, semantic HTML remains crucial as components ultimately render to HTML elements.

**When to use:** Always use semantic elements (`<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, etc.) instead of generic `<div>` elements when their meaning matches your content.

```jsx
// Poor semantics
const Header = () => (
	<div className="header">
		<div className="logo">Company</div>
		<div className="nav-links">
			<div onClick={handleClick}>Home</div>
		</div>
	</div>
);

// Better semantics
const Header = () => (
	<header>
		<h1>Company</h1>
		<nav>
			{/* In a React Router app, you'd use <Link to="/">Home</Link> */}
			<a href="/">Home</a>
		</nav>
	</header>
);
```

## Modern JavaScript Features

### Variables and Scope

**Problem:** How do we declare variables with appropriate scoping and prevent unintended mutations?

**Theory:** `let` and `const` provide block scoping, replacing the function-scoped `var`. In React, `const` is preferred for variable declarations to reinforce immutability principles.

-   Use `const` for values that won't be reassigned (including objects and arrays)
-   Use `let` only when reassignment is necessary
-   Avoid `var` entirely in modern React code

### Arrow Functions

**Problem:** How do we write concise functions while avoiding `this` binding issues?

**Theory:** Arrow functions provide shorter syntax and lexical `this` binding, making them ideal for React components and callbacks.

**When to use:**

-   For functional components
-   For event handlers and callbacks
-   When you need to preserve the surrounding context (`this`)

```jsx
// Traditional function with binding issues in classes
class Component extends React.Component {
	constructor() {
		super();
		// Binding required
		this.handleClick = this.handleClick.bind(this);
	}

	handleClick() {
		/* ... */
	}
}

// Arrow function with lexical this
class Component extends React.Component {
	// No binding required
	handleClick = () => {
		/* ... */
	};
}

// Modern functional component
// Note: Functional components with hooks are now the standard in modern React development.
const Component = () => {
	const handleClick = () => {
		/* ... */
	};
	return <button onClick={handleClick}>Click</button>;
};
```

### Destructuring

**Problem:** How do we extract values from objects and arrays cleanly?

**Theory:** Destructuring provides a concise way to extract multiple values from objects and arrays, reducing repetitive code.

**When to use:**

-   For accessing props in components
-   For working with React hooks like `useState` and `useEffect`
-   When extracting multiple values from API responses

```jsx
// Without destructuring
function Profile(props) {
	return (
		<div>
			<h1>{props.user.name}</h1>
			<p>{props.user.bio}</p>
		</div>
	);
}

// With destructuring
function Profile({ user: { name, bio } }) {
	return (
		<div>
			<h1>{name}</h1>
			<p>{bio}</p>
		</div>
	);
}

// Hook destructuring
const [count, setCount] = useState(0);
```

### Array Methods

**Problem:** How do we transform data declaratively for rendering in React?

**Theory:** Modern array methods like `map`, `filter`, and `reduce` enable declarative data transformations that align with React's declarative rendering approach.

**When to use:**

-   `map`: For rendering lists of components from arrays
-   `filter`: For conditionally including items based on criteria
-   `reduce`: For transforming arrays into different data structures

```jsx
// Rendering a list with map
function ProductList({ products }) {
	return (
		<ul>
			{products.map((product) => (
				<li key={product.id}>
					{product.name}: ${product.price}
				</li>
			))}
		</ul>
	);
}

// Filtering items
const activeUsers = users.filter((user) => user.isActive);

// Grouping data with reduce
const usersByRole = users.reduce((acc, user) => {
	acc[user.role] = [...(acc[user.role] || []), user];
	return acc;
}, {});
```

### Asynchronous JavaScript

**Problem:** How do we handle operations like API calls without blocking the UI?

**Theory:** Promises and async/await provide clean patterns for handling asynchronous operations, crucial for data fetching in React applications.

**When to use:**

-   For API calls in `useEffect` hooks
-   For event handlers that perform asynchronous operations
-   For handling multiple concurrent operations

```jsx
// Using async/await with useEffect
useEffect(() => {
	const fetchData = async () => {
		try {
			setLoading(true);
			const response = await fetch("/api/data");
			const data = await response.json();
			setData(data);
		} catch (error) {
			setError(error);
		} finally {
			setLoading(false);
		}
	};

	fetchData();
}, []);

// Promise-based approach
const handleSubmit = () => {
	setSubmitting(true);
	saveData(formData)
		.then((response) => setSuccess(true))
		.catch((error) => setError(error))
		.finally(() => setSubmitting(false));
};
```

Understanding these foundational concepts is crucial before diving into React. They form the building blocks that will make learning React more intuitive and help you write cleaner, more maintainable code.
