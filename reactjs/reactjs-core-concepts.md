# Core Concepts & Tooling

This section covers the fundamental concepts of React and the modern tooling that makes React development efficient and productive.

[[reactjs-roadmap|← Back to React.js Roadmap]]

## Setting Up a Modern Development Environment

**Problem:** How do we create an efficient React development environment that handles modern JavaScript features, optimizes code, and provides a smooth developer experience?

**Theory:** Modern React development relies on build tools that handle tasks like transpilation, bundling, and hot reloading.

**Key components:**

-   **Node.js & npm/yarn:** The runtime and package managers for installing dependencies
-   **Build tools:** Vite (recommended) or Create React App (legacy)
-   **ESLint & Prettier:** For code quality and formatting
-   **Browser DevTools with React extensions:** For debugging and performance analysis

```bash
# Creating a new React project with Vite
npm create vite@latest my-app -- --template react

# Starting the development server
cd my-app
npm install
npm run dev
```

## JSX: JavaScript XML

**Problem:** How do we write UI code that combines JavaScript logic with HTML-like markup in a readable way?

**Theory:** JSX is a syntax extension that allows you to write HTML-like code in JavaScript. It gets transpiled to `React.createElement()` calls that create React elements.

**When to use:** For all React component rendering. While React can be written without JSX, it's the standard approach for its readability and expressiveness.

**JSX rules:**

-   Return a single root element (or use fragments: `<>...</>`)
-   Close all tags, including self-closing ones (`<img />`)
-   Use `className` instead of `class` for CSS classes
-   Embed JavaScript expressions with curly braces `{}`

```jsx
// JSX syntax
function Greeting({ name, isLoggedIn }) {
	return (
		<div className="greeting">
			{isLoggedIn ? (
				<h1>Welcome back, {name}!</h1>
			) : (
				<h1>Please log in</h1>
			)}
		</div>
	);
}

// What it compiles to (simplified)
function Greeting({ name, isLoggedIn }) {
	return React.createElement(
		"div",
		{ className: "greeting" },
		isLoggedIn
			? React.createElement("h1", null, "Welcome back, ", name, "!")
			: React.createElement("h1", null, "Please log in")
	);
}
```

## Components: The Building Blocks

**Problem:** How do we create reusable, self-contained UI elements that can be composed to build complex interfaces?

**Theory:** Components are the core building blocks of React applications. They encapsulate UI elements and their behavior, allowing for composition and reuse.

### Functional vs. Class Components

**Problem:** What are the different ways to define components, and when should each be used?

**Theory:** React offers two ways to define components: function-based and class-based. Functional components are now the standard with the introduction of Hooks.

**When to use functional components:**

-   For all new React code
-   When you need Hooks for state and effects
-   For simpler, more readable code

**When to use class components:**

-   When maintaining legacy code
-   When you need error boundaries (until React 18+ alternatives)

```jsx
// Functional component (preferred)
function UserProfile({ user }) {
	const [isExpanded, setIsExpanded] = useState(false);

	return (
		<div className="user-profile">
			<h2>{user.name}</h2>
			<button onClick={() => setIsExpanded(!isExpanded)}>
				{isExpanded ? "Show less" : "Show more"}
			</button>

			{isExpanded && <p>{user.bio}</p>}
		</div>
	);
}

// Class component (legacy)
class UserProfile extends React.Component {
	constructor(props) {
		super(props);
		this.state = { isExpanded: false };
	}

	toggleExpanded = () => {
		this.setState((prevState) => ({
			isExpanded: !prevState.isExpanded,
		}));
	};

	render() {
		const { user } = this.props;
		const { isExpanded } = this.state;

		return (
			<div className="user-profile">
				<h2>{user.name}</h2>
				<button onClick={this.toggleExpanded}>
					{isExpanded ? "Show less" : "Show more"}
				</button>

				{isExpanded && <p>{user.bio}</p>}
			</div>
		);
	}
}
```

## Props vs. State

**Problem:** How do we manage and pass data in a React application while maintaining a unidirectional data flow?

**Theory:** React uses two types of data: props (passed from parent to child) and state (managed within a component).

**Props:**

-   Passed from parent to child components
-   Read-only within the receiving component
-   Changes trigger re-renders
-   Used for configuration and data passing

**State:**

-   Managed within a component
-   Can be modified through setter functions
-   Changes trigger re-renders
-   Used for data that changes over time

```jsx
// Parent component passing props
function ParentComponent() {
	// State managed in parent
	const [count, setCount] = useState(0);

	return (
		<div>
			<h1>Counter: {count}</h1>
			<button onClick={() => setCount(count + 1)}>Increment</button>

			{/* Passing state as props */}
			<ChildComponent
				count={count}
				onIncrement={() => setCount(count + 1)}
			/>
		</div>
	);
}

// Child component receiving props
function ChildComponent({ count, onIncrement }) {
	return (
		<div>
			<p>Count from parent: {count}</p>
			<button onClick={onIncrement}>Increment from child</button>
		</div>
	);
}
```

## Conditional Rendering

**Problem:** How do we show or hide elements based on conditions?

**Theory:** React uses JavaScript expressions within JSX to conditionally render elements.

**Common patterns:**

-   Ternary operator (`condition ? trueElement : falseElement`)
-   Logical AND (`condition && element`)
-   Early returns or variables for complex conditions

```jsx
function ConditionalExample({ isLoggedIn, isAdmin, content }) {
	// Early return pattern
	if (!content) {
		return <p>No content available</p>;
	}

	// Variable pattern for complex logic
	let userSection;
	if (isLoggedIn) {
		userSection = isAdmin ? <AdminPanel /> : <UserDashboard />;
	} else {
		userSection = <LoginPrompt />;
	}

	return (
		<div>
			{/* Ternary operator */}
			<header>{isLoggedIn ? <LogoutButton /> : <LoginButton />}</header>

			{/* Logical AND */}
			{isAdmin && <AdminBadge />}

			{/* Variable with complex logic */}
			{userSection}

			<main>{content}</main>
		</div>
	);
}
```

## Rendering Lists

**Problem:** How do we efficiently render collections of data as UI elements?

**Theory:** React uses the `map()` method to transform arrays of data into arrays of React elements. Each element in a list should have a unique `key` prop to help React identify changes efficiently.

**Best practices:**

-   Always provide a stable, unique `key` for each item
-   Use item IDs as keys when available
-   Avoid using array indices as keys when the list can reorder
-   Extract complex list items into separate components

```jsx
function ProductList({ products }) {
	return (
		<ul className="product-list">
			{products.map((product) => (
				<ProductItem key={product.id} product={product} />
			))}
		</ul>
	);
}

function ProductItem({ product }) {
	return (
		<li className="product-item">
			<img src={product.image} alt={product.name} />
			<div>
				<h3>{product.name}</h3>
				<p>${product.price.toFixed(2)}</p>
			</div>
		</li>
	);
}
```

Understanding these core concepts provides the foundation for building React applications. As you progress, these concepts will be combined in increasingly sophisticated ways to create complex, interactive user interfaces.
