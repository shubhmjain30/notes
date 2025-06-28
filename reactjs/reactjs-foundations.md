# 🌱 Stage 1: Foundations (Beginner)

Master these fundamental concepts before moving to Stage 2. Each topic builds the foundation for React development.

---

## ✅ Checklist: Foundations

-   [ ] **JSX & Rendering Basics** – syntax, fragments, expressions, lists/keys
-   [ ] **Component Types** – function vs. (legacy) class components
-   [ ] **Props & PropTypes** – data flow, default props, validation
-   [ ] **State & setState** – local component state patterns
-   [ ] **Event Handling** – synthetic events, binding, lifting state
-   [ ] **Conditional Rendering** – inline conditions, short-circuiting, element variables
-   [ ] **Basic Styling** – CSS Modules, inline styles, className conventions
-   [ ] **Project Bootstrapping** – Vite / CRA / Next app boilerplates
-   [ ] **Developer Tooling** – React DevTools, eslint-plugin-react, Prettier

---

## JSX & Rendering Basics

### Understanding JSX Syntax

**Problem:** How do we write HTML-like markup within JavaScript while maintaining the full power of JavaScript expressions?

**Theory:** JSX is a syntax extension for JavaScript, allowing you to write HTML-like markup inside a JavaScript file. It is not valid JavaScript and must be compiled by a tool like **Babel**, which transforms JSX into standard `React.createElement()` function calls. This allows you to build UI components with a familiar syntax while retaining the full power of JavaScript.

**Key Takeaway:** Think of JSX as syntactic sugar for calling `React.createElement(component, props, ...children)`.

**Core JSX Rules:**

1. Return a single root element (or use `<React.Fragment>` or `<>`)
2. Close all tags (including self-closing like `<img />`)
3. Use `className` instead of `class`
4. Use `htmlFor` instead of `for`
5. Embed JavaScript expressions with `{}`

```jsx
// ✅ Correct JSX
function WelcomeMessage({ user, notifications }) {
  return (
    <>
      <h1 className="heading">Welcome, {user.name}!</h1>
      <p>You have {notifications.length} new messages</p>
      <img src={user.avatar} alt={`${user.name}'s avatar`} />
    </>
  );
}

// ❌ Common mistakes
function BrokenComponent() {
  return (
    // Missing fragment wrapper
    <h1>Title</h1>
    <p>Description</p>
  );
}
```

### JSX Expressions and Dynamic Content

**When to use:** Embed JavaScript expressions for dynamic values, conditional logic, and data transformation.

```jsx
function UserDashboard({ user, isAdmin, tasks }) {
	const completedTasks = tasks.filter((task) => task.completed);
	const pendingCount = tasks.length - completedTasks.length;

	return (
		<div className={`dashboard ${isAdmin ? "admin-view" : "user-view"}`}>
			<h2>
				Hello, {user.firstName} {user.lastName}
			</h2>

			{/* Conditional rendering */}
			{isAdmin && <button>Admin Panel</button>}

			{/* Computed values */}
			<p>
				Progress: {completedTasks.length}/{tasks.length} tasks completed
			</p>

			{/* Template literals */}
			<div
				className={`progress-bar ${
					pendingCount === 0 ? "complete" : "pending"
				}`}
			>
				<div
					style={{
						width: `${
							(completedTasks.length / tasks.length) * 100
						}%`,
					}}
				/>
			</div>
		</div>
	);
}
```

### Rendering Lists and Keys

**Problem:** How do we efficiently render dynamic lists while helping React track changes?

**Theory:** The `key` prop is a special string attribute you need to include when creating lists of elements. It helps React identify which items have changed, been added, or been removed, enabling efficient DOM updates.

**Why are keys important?**
React uses keys to match children in the original tree with children in the subsequent tree. A stable and unique key for each item allows React to reuse and reorder existing components, which is much more efficient than re-rendering them from scratch.

**Why not use the array index as a key?**
Using an array index (`index`) as a key is an anti-pattern if the list can be reordered, filtered, or have items inserted/deleted from the middle. It can lead to:

-   **Incorrect component state:** If you sort a list, the component at `index=0` might now have different data, but it could retain the state of the old component that was at `index=0`.
-   **UI bugs:** Elements might not update correctly, leading to a confusing user experience.
-   **Performance issues:** React might end up doing more DOM mutations than necessary.

**Key Selection Rules:**

-   Use stable, unique identifiers (preferably from your data)
-   Avoid array indices if the list can be reordered
-   Keys must be unique among siblings

```jsx
function TaskList({ tasks, onToggle, onDelete }) {
	return (
		<ul className="task-list">
			{tasks.map((task) => (
				<li
					key={task.id}
					className={`task ${task.completed ? "completed" : ""}`}
				>
					<input
						type="checkbox"
						checked={task.completed}
						onChange={() => onToggle(task.id)}
					/>
					<span>{task.title}</span>
					<button onClick={() => onDelete(task.id)}>Delete</button>
				</li>
			))}
		</ul>
	);
}

// ❌ Bad: Using array index as key
{
	tasks.map((task, index) => (
		<li key={index}>...</li> // Problematic if list is reordered
	));
}

// ✅ Good: Using stable ID
{
	tasks.map((task) => <li key={task.id}>...</li>);
}
```

### React Fragments

**Problem:** How do we group multiple elements without adding extra DOM nodes?

```jsx
// Using React.Fragment
function UserInfo({ user }) {
	return (
		<React.Fragment>
			<dt>Name</dt>
			<dd>{user.name}</dd>
			<dt>Email</dt>
			<dd>{user.email}</dd>
		</React.Fragment>
	);
}

// Using shorthand syntax
function UserInfo({ user }) {
	return (
		<>
			<dt>Name</dt>
			<dd>{user.name}</dd>
			<dt>Email</dt>
			<dd>{user.email}</dd>
		</>
	);
}

// When you need a key on the fragment
function ItemList({ items }) {
	return (
		<dl>
			{items.map((item) => (
				<React.Fragment key={item.id}>
					<dt>{item.term}</dt>
					<dd>{item.definition}</dd>
				</React.Fragment>
			))}
		</dl>
	);
}
```

---

## Component Types

### Functional Components (Modern Standard)

**Problem:** How do we create reusable UI components that are simple, testable, and support modern React features?

**Theory:** Functional components are the standard approach in modern React. They're simple JavaScript functions that return JSX and can use Hooks for state and lifecycle logic.

```jsx
// Basic functional component
function Greeting({ name, time }) {
	return (
		<div>
			<h1>Hello, {name}!</h1>
			<p>Current time: {time}</p>
		</div>
	);
}

// Arrow function component
const Button = ({ children, onClick, variant = "primary" }) => {
	return (
		<button className={`btn btn-${variant}`} onClick={onClick}>
			{children}
		</button>
	);
};

// Component with destructured props
function UserCard({ user: { name, email, avatar }, onEdit }) {
	return (
		<div className="user-card">
			<img src={avatar} alt={`${name}'s avatar`} />
			<h3>{name}</h3>
			<p>{email}</p>
			<button onClick={onEdit}>Edit Profile</button>
		</div>
	);
}
```

### Class Components (Legacy - Understanding Required)

**Problem:** How were components written before Hooks, and how do we maintain legacy codebases?

**Theory:** Class components extend `React.Component` and use lifecycle methods for side effects. Understanding them is crucial for maintaining older codebases.

```jsx
class UserProfile extends React.Component {
	constructor(props) {
		super(props);
		this.state = {
			isEditing: false,
			userData: props.user,
		};

		// Binding methods (pre-arrow function era)
		this.handleEdit = this.handleEdit.bind(this);
	}

	// Modern class syntax with arrow functions (no binding needed)
	handleEdit = () => {
		this.setState({ isEditing: !this.state.isEditing });
	};

	handleSave = (newData) => {
		this.setState({
			userData: newData,
			isEditing: false,
		});
	};

	render() {
		const { isEditing, userData } = this.state;

		return (
			<div className="user-profile">
				{isEditing ? (
					<EditForm
						user={userData}
						onSave={this.handleSave}
						onCancel={() => this.setState({ isEditing: false })}
					/>
				) : (
					<DisplayView user={userData} onEdit={this.handleEdit} />
				)}
			</div>
		);
	}
}
```

### Migration from Class to Function Components

```jsx
// Before (Class Component)
class Counter extends React.Component {
	constructor(props) {
		super(props);
		this.state = { count: 0 };
	}

	increment = () => {
		this.setState((prevState) => ({ count: prevState.count + 1 }));
	};

	render() {
		return (
			<div>
				<p>Count: {this.state.count}</p>
				<button onClick={this.increment}>Increment</button>
			</div>
		);
	}
}

// After (Functional Component with Hooks)
function Counter() {
	const [count, setCount] = useState(0);

	const increment = () => {
		setCount((prevCount) => prevCount + 1);
	};

	return (
		<div>
			<p>Count: {count}</p>
			<button onClick={increment}>Increment</button>
		</div>
	);
}
```

---

## Props & PropTypes

### Understanding Props Flow

**Problem:** How do we pass data between components in a predictable, type-safe way?

**Theory:** Props are the mechanism for passing data from parent to child components. They are read-only and create a unidirectional data flow.

```jsx
// Parent component
function App() {
	const user = {
		id: 1,
		name: "Alice Johnson",
		email: "alice@example.com",
		role: "admin",
	};

	const handleUserUpdate = (updatedUser) => {
		console.log("User updated:", updatedUser);
	};

	return (
		<div>
			<UserProfile
				user={user}
				isEditable={true}
				theme="dark"
				onUpdate={handleUserUpdate}
			/>
		</div>
	);
}

// Child component receiving props
function UserProfile({ user, isEditable, theme, onUpdate }) {
	return (
		<div className={`user-profile theme-${theme}`}>
			<h2>{user.name}</h2>
			<p>{user.email}</p>
			<span className={`role role-${user.role}`}>{user.role}</span>

			{isEditable && (
				<button
					onClick={() => onUpdate({ ...user, name: "Updated Name" })}
				>
					Update Name
				</button>
			)}
		</div>
	);
}
```

### Default Props

**Problem:** How do we provide fallback values for optional props?

```jsx
// Method 1: ES6 default parameters (preferred)
function Button({
	children,
	variant = "primary",
	size = "medium",
	disabled = false,
	onClick = () => {},
}) {
	return (
		<button
			className={`btn btn-${variant} btn-${size}`}
			disabled={disabled}
			onClick={onClick}
		>
			{children}
		</button>
	);
}

// Method 2: defaultProps (legacy, but still used)
function Card({ title, children, elevated }) {
	return (
		<div className={`card ${elevated ? "elevated" : ""}`}>
			{title && <h3>{title}</h3>}
			<div className="card-content">{children}</div>
		</div>
	);
}

Card.defaultProps = {
	elevated: false,
};
```

### PropTypes for Runtime Validation

**Problem:** How do we catch prop-related bugs during development?

**Theory:** PropTypes, provided by the `prop-types` library, offer **runtime type checking** for your React props. During development, React will check the props passed to your components against the definitions you provide and issue a console warning if they don't match. This helps catch bugs early and serves as valuable documentation for how a component should be used.

**Interview Focus:** While PropTypes are still used, especially in legacy projects, the modern standard for type safety in the React ecosystem is **TypeScript**.

#### **PropTypes vs. TypeScript**

| Feature            | PropTypes                                        | TypeScript                                                 |
| ------------------ | ------------------------------------------------ | ---------------------------------------------------------- |
| **When it Checks** | Runtime (in development)                         | Compile-time / Static Analysis (before code runs)          |
| **Scope**          | Only checks props passed to React components     | Checks everything: variables, function args, return values |
| **Tooling**        | Console warnings                                 | Rich editor integration (autocomplete, error highlighting) |
| **Build Impact**   | Included in development builds, stripped in prod | Types are completely removed during the build process      |

**Key Takeaway:** Use TypeScript for new projects to catch errors earlier and for a more robust development experience. Understand PropTypes for maintaining existing JavaScript-based React projects.

```jsx
import PropTypes from "prop-types";

function UserList({ users, onUserSelect, loading, error }) {
	if (loading) return <div>Loading...</div>;
	if (error) return <div>Error: {error}</div>;

	return (
		<ul>
			{users.map((user) => (
				<li key={user.id} onClick={() => onUserSelect(user)}>
					{user.name} - {user.email}
				</li>
			))}
		</ul>
	);
}

UserList.propTypes = {
	users: PropTypes.arrayOf(
		PropTypes.shape({
			id: PropTypes.oneOfType([PropTypes.string, PropTypes.number])
				.isRequired,
			name: PropTypes.string.isRequired,
			email: PropTypes.string.isRequired,
			role: PropTypes.oneOf(["admin", "user", "moderator"]),
		})
	).isRequired,
	onUserSelect: PropTypes.func.isRequired,
	loading: PropTypes.bool,
	error: PropTypes.string,
};

UserList.defaultProps = {
	loading: false,
	error: null,
};

// Complex PropTypes examples
function Dashboard({ config, data, actions }) {
	// Component implementation
}

Dashboard.propTypes = {
	config: PropTypes.shape({
		theme: PropTypes.oneOf(["light", "dark"]),
		layout: PropTypes.oneOf(["grid", "list"]),
		itemsPerPage: PropTypes.number,
		showFilters: PropTypes.bool,
	}),
	data: PropTypes.arrayOf(PropTypes.object),
	actions: PropTypes.objectOf(PropTypes.func),
};
```

---

## State & setState

### Understanding Component State

**Problem:** How do we manage data that changes over time within a component?

**Theory:** State represents data that can change and trigger re-renders. In functional components, we use the `useState` Hook.

```jsx
import { useState } from "react";

function Counter() {
	// useState returns [currentValue, setterFunction]
	const [count, setCount] = useState(0);
	const [step, setStep] = useState(1);

	const increment = () => setCount(count + step);
	const decrement = () => setCount(count - step);
	const reset = () => setCount(0);

	return (
		<div>
			<h2>Count: {count}</h2>
			<div>
				<label>
					Step:
					<input
						type="number"
						value={step}
						onChange={(e) => setStep(Number(e.target.value))}
					/>
				</label>
			</div>
			<button onClick={increment}>+{step}</button>
			<button onClick={decrement}>-{step}</button>
			<button onClick={reset}>Reset</button>
		</div>
	);
}
```

### State Updates with Previous Value

**Problem:** How do we update state based on the previous state value safely?

**Theory:** State updates in React may be asynchronous and batched for performance. This means that when you call `setCount(count + 1)`, the `count` variable is not updated immediately. React may group multiple state updates into a single re-render.

Because of this, you should **never rely on the current state value to calculate the next state**. Instead, use a **functional update**, where you pass a function to the state setter. This function receives the latest, most up-to-date state (the "previous state") as its argument and returns the new state.

**Key Takeaway:** If your new state depends on the old state, always use a functional update: `setState(prevState => newState)`.

```jsx
function TodoApp() {
	const [todos, setTodos] = useState([]);
	const [inputValue, setInputValue] = useState("");

	const addTodo = () => {
		if (inputValue.trim()) {
			// ✅ Functional update - safe for async updates
			setTodos((prevTodos) => [
				...prevTodos,
				{
					id: Date.now(),
					text: inputValue.trim(),
					completed: false,
				},
			]);
			setInputValue("");
		}
	};

	const toggleTodo = (id) => {
		setTodos((prevTodos) =>
			prevTodos.map((todo) =>
				todo.id === id ? { ...todo, completed: !todo.completed } : todo
			)
		);
	};

	const deleteTodo = (id) => {
		setTodos((prevTodos) => prevTodos.filter((todo) => todo.id !== id));
	};

	return (
		<div>
			<div>
				<input
					value={inputValue}
					onChange={(e) => setInputValue(e.target.value)}
					onKeyPress={(e) => e.key === "Enter" && addTodo()}
					placeholder="Add a todo..."
				/>
				<button onClick={addTodo}>Add</button>
			</div>

			<ul>
				{todos.map((todo) => (
					<li key={todo.id}>
						<input
							type="checkbox"
							checked={todo.completed}
							onChange={() => toggleTodo(todo.id)}
						/>
						<span
							style={{
								textDecoration: todo.completed
									? "line-through"
									: "none",
							}}
						>
							{todo.text}
						</span>
						<button onClick={() => deleteTodo(todo.id)}>
							Delete
						</button>
					</li>
				))}
			</ul>
		</div>
	);
}
```

### Complex State Management

```jsx
function UserForm() {
	const [formData, setFormData] = useState({
		name: "",
		email: "",
		preferences: {
			theme: "light",
			notifications: true,
			language: "en",
		},
	});

	// Update nested state
	const updatePreference = (key, value) => {
		setFormData((prev) => ({
			...prev,
			preferences: {
				...prev.preferences,
				[key]: value,
			},
		}));
	};

	// Update top-level state
	const updateField = (field, value) => {
		setFormData((prev) => ({
			...prev,
			[field]: value,
		}));
	};

	return (
		<form>
			<input
				value={formData.name}
				onChange={(e) => updateField("name", e.target.value)}
				placeholder="Name"
			/>

			<input
				value={formData.email}
				onChange={(e) => updateField("email", e.target.value)}
				placeholder="Email"
			/>

			<select
				value={formData.preferences.theme}
				onChange={(e) => updatePreference("theme", e.target.value)}
			>
				<option value="light">Light</option>
				<option value="dark">Dark</option>
			</select>

			<label>
				<input
					type="checkbox"
					checked={formData.preferences.notifications}
					onChange={(e) =>
						updatePreference("notifications", e.target.checked)
					}
				/>
				Enable notifications
			</label>
		</form>
	);
}
```

---

## Event Handling

### Synthetic Events

**Problem:** How do we handle user interactions consistently across different browsers?

**Theory:** To ensure consistency across different browsers, React wraps the browser's native event in a `SyntheticEvent` object. This object has the same interface as the browser's native event, including methods like `stopPropagation()` and `preventDefault()`, but it works identically across all browsers.

**In modern React (17 and later):** The underlying mechanism of event pooling has been removed. The `SyntheticEvent` system is now a lighter-weight wrapper primarily focused on providing a consistent, cross-browser API for event handling. You can still access the native event via `event.nativeEvent` if needed.

```jsx
function EventDemo() {
	const handleClick = (event) => {
		console.log("Event type:", event.type);
		console.log("Target element:", event.target);
		console.log("Current target:", event.currentTarget);

		// Prevent default behavior
		event.preventDefault();

		// Stop event bubbling
		event.stopPropagation();
	};

	const handleSubmit = (event) => {
		event.preventDefault();
		const formData = new FormData(event.target);
		console.log("Form data:", Object.fromEntries(formData));
	};

	const handleKeyPress = (event) => {
		if (event.key === "Enter") {
			console.log("Enter key pressed");
		}
	};

	return (
		<div>
			<button onClick={handleClick}>Click me</button>

			<form onSubmit={handleSubmit}>
				<input
					name="username"
					onKeyPress={handleKeyPress}
					placeholder="Press Enter"
				/>
				<button type="submit">Submit</button>
			</form>
		</div>
	);
}
```

### Event Handler Patterns

```jsx
function TodoItem({ todo, onToggle, onEdit, onDelete }) {
	const [isEditing, setIsEditing] = useState(false);
	const [editText, setEditText] = useState(todo.text);

	// Inline handler for simple cases
	const handleDoubleClick = () => setIsEditing(true);

	// Handler with parameters
	const handleSave = () => {
		onEdit(todo.id, editText);
		setIsEditing(false);
	};

	// Handler with event parameter
	const handleKeyPress = (event) => {
		if (event.key === "Enter") {
			handleSave();
		} else if (event.key === "Escape") {
			setEditText(todo.text);
			setIsEditing(false);
		}
	};

	// Prevent event bubbling
	const handleDeleteClick = (event) => {
		event.stopPropagation();
		onDelete(todo.id);
	};

	return (
		<li
			className={`todo-item ${todo.completed ? "completed" : ""}`}
			onDoubleClick={handleDoubleClick}
		>
			<input
				type="checkbox"
				checked={todo.completed}
				onChange={() => onToggle(todo.id)}
			/>

			{isEditing ? (
				<input
					value={editText}
					onChange={(e) => setEditText(e.target.value)}
					onKeyPress={handleKeyPress}
					onBlur={handleSave}
					autoFocus
				/>
			) : (
				<span>{todo.text}</span>
			)}

			<button onClick={handleDeleteClick}>Delete</button>
		</li>
	);
}
```

### Lifting State Up

**Problem:** How do we share state between sibling components?

**Theory:** When multiple components need to share state, move the state to their closest common ancestor and pass it down as props along with update functions.

```jsx
function App() {
	const [todos, setTodos] = useState([]);
	const [filter, setFilter] = useState("all"); // 'all', 'active', 'completed'

	const addTodo = (text) => {
		setTodos((prev) => [
			...prev,
			{
				id: Date.now(),
				text,
				completed: false,
			},
		]);
	};

	const toggleTodo = (id) => {
		setTodos((prev) =>
			prev.map((todo) =>
				todo.id === id ? { ...todo, completed: !todo.completed } : todo
			)
		);
	};

	const deleteTodo = (id) => {
		setTodos((prev) => prev.filter((todo) => todo.id !== id));
	};

	const filteredTodos = todos.filter((todo) => {
		if (filter === "active") return !todo.completed;
		if (filter === "completed") return todo.completed;
		return true;
	});

	return (
		<div>
			<TodoInput onAdd={addTodo} />
			<FilterButtons currentFilter={filter} onFilterChange={setFilter} />
			<TodoList
				todos={filteredTodos}
				onToggle={toggleTodo}
				onDelete={deleteTodo}
			/>
		</div>
	);
}

function TodoInput({ onAdd }) {
	const [value, setValue] = useState("");

	const handleSubmit = (e) => {
		e.preventDefault();
		if (value.trim()) {
			onAdd(value.trim());
			setValue("");
		}
	};

	return (
		<form onSubmit={handleSubmit}>
			<input
				value={value}
				onChange={(e) => setValue(e.target.value)}
				placeholder="What needs to be done?"
			/>
			<button type="submit">Add</button>
		</form>
	);
}

function FilterButtons({ currentFilter, onFilterChange }) {
	const filters = [
		{ key: "all", label: "All" },
		{ key: "active", label: "Active" },
		{ key: "completed", label: "Completed" },
	];

	return (
		<div>
			{filters.map((filter) => (
				<button
					key={filter.key}
					className={currentFilter === filter.key ? "active" : ""}
					onClick={() => onFilterChange(filter.key)}
				>
					{filter.label}
				</button>
			))}
		</div>
	);
}
```

---

## Conditional Rendering

### Basic Conditional Patterns

**Problem:** How do we show different UI based on state or props?

**Theory:** React uses JavaScript's logical operators for conditional rendering. No special template syntax needed.

```jsx
function UserStatus({ user, isLoading, error }) {
	// Early returns for loading and error states
	if (isLoading) {
		return <div className="loading">Loading user data...</div>;
	}

	if (error) {
		return (
			<div className="error">
				<h3>Error loading user</h3>
				<p>{error.message}</p>
				<button onClick={() => window.location.reload()}>
					Try Again
				</button>
			</div>
		);
	}

	// No user data
	if (!user) {
		return <div className="empty">No user found</div>;
	}

	// Main content when everything is loaded
	return (
		<div className="user-status">
			<h2>Welcome, {user.name}!</h2>

			{/* Conditional rendering with logical AND */}
			{user.isAdmin && (
				<div className="admin-panel">
					<button>Admin Dashboard</button>
				</div>
			)}

			{/* Ternary operator for either/or scenarios */}
			<div className="subscription-status">
				{user.isPremium ? (
					<span className="premium">Premium Member ⭐</span>
				) : (
					<button>Upgrade to Premium</button>
				)}
			</div>

			{/* Multiple conditions */}
			{user.notifications && user.notifications.length > 0 && (
				<div className="notifications">
					<h3>Notifications ({user.notifications.length})</h3>
					<ul>
						{user.notifications.slice(0, 3).map((notification) => (
							<li key={notification.id}>
								{notification.message}
							</li>
						))}
					</ul>
				</div>
			)}
		</div>
	);
}
```

### Advanced Conditional Patterns

```jsx
function Dashboard({ user, permissions, data }) {
	// Computed values for cleaner JSX
	const canViewAnalytics = permissions.includes("analytics");
	const canManageUsers = permissions.includes("user_management");
	const hasData = data && data.length > 0;

	// Element variables for complex conditions
	let mainContent;

	if (!hasData) {
		mainContent = <EmptyState />;
	} else if (user.role === "admin") {
		mainContent = <AdminDashboard data={data} />;
	} else if (user.role === "manager") {
		mainContent = <ManagerDashboard data={data} />;
	} else {
		mainContent = <UserDashboard data={data} />;
	}

	return (
		<div className="dashboard">
			<header>
				<h1>Dashboard</h1>
				{/* Conditional navigation based on permissions */}
				<nav>
					<a href="/profile">Profile</a>
					{canViewAnalytics && <a href="/analytics">Analytics</a>}
					{canManageUsers && <a href="/users">Manage Users</a>}
				</nav>
			</header>

			<main>{mainContent}</main>

			{/* Conditional sidebar */}
			{(canViewAnalytics || canManageUsers) && (
				<aside>
					<h3>Admin Tools</h3>
					{/* ... */}
				</aside>
			)}
		</div>
	);
}

// Helper components for cleaner code
function EmptyState() {
	return (
		<div className="empty-state">
			<h2>No Data Available</h2>
			<p>Start by adding some content to your dashboard.</p>
			<button>Get Started</button>
		</div>
	);
}
```

### Avoiding Common Pitfalls

```jsx
function ConditionalRenderingPitfalls({ items, count, user }) {
	return (
		<div>
			{/* ❌ Potential bug: renders 0 when count is 0 */}
			{count && <p>You have {count} items</p>}

			{/* ✅ Correct: explicitly check for truthiness */}
			{count > 0 && <p>You have {count} items</p>}

			{/* ❌ Potential bug: renders empty string for empty array */}
			{items.length && <ItemList items={items} />}

			{/* ✅ Correct: check length explicitly */}
			{items.length > 0 && <ItemList items={items} />}

			{/* ❌ Potential error: user might be null */}
			{user.isAdmin && <AdminPanel />}

			{/* ✅ Correct: check user exists first */}
			{user && user.isAdmin && <AdminPanel />}

			{/* ✅ Even better: using optional chaining */}
			{user?.isAdmin && <AdminPanel />}
		</div>
	);
}
```

---

## Basic Styling

**Problem:** How do we apply styles to React components without creating conflicts in a large application?

**Theory:** React offers several ways to style components, each with its own trade-offs. The most common approaches are CSS Modules for scoped styles, standard CSS with BEM naming conventions, and inline styles for dynamic styling.

### CSS Modules

A CSS Module is a CSS file where all class names and animation names are scoped locally by default. This is the recommended way to handle component-level styling as it prevents class name collisions automatically.

```jsx
// 1. Create a CSS file named with `.module.css`, e.g., `Button.module.css`
/* Button.module.css */
.button {
  background-color: #61dafb;
  border: none;
  color: white;
  padding: 10px 20px;
  cursor: pointer;
  border-radius: 4px;
}

.button:hover {
  background-color: #4fa8c8;
}

// 2. Import and use it in your component
import styles from './Button.module.css';

function Button({ children }) {
  return (
    <button className={styles.button}>
      {children}
    </button>
  );
}
```

### Inline Styles

Inline styles are useful for dynamic styling that depends on component props or state. The `style` attribute accepts a JavaScript object with camelCased property names.

```jsx
function Avatar({ user, size = 50 }) {
	const avatarStyle = {
		width: `${size}px`,
		height: `${size}px`,
		borderRadius: "50%",
		backgroundColor: user.color || "#ccc",
		display: "flex",
		alignItems: "center",
		justifyContent: "center",
		color: "white",
		fontSize: `${size / 2}px`,
	};

	return <div style={avatarStyle}>{user.name.charAt(0)}</div>;
}
```

### Global CSS with BEM (Convention)

You can still use global CSS files, but it's best to follow a strict naming convention like BEM (Block, Element, Modifier) to avoid conflicts.

```css
/* styles.css */
.card {
	/* Block */
	border: 1px solid #eee;
	border-radius: 8px;
}
.card__header {
	/* Element */
	font-size: 1.5em;
	padding: 1rem;
}
.card--dark {
	/* Modifier */
	background-color: #333;
	color: white;
}
```

```jsx
function ThemedCard({ title, isDark }) {
	const cardClasses = `card ${isDark ? "card--dark" : ""}`;
	return (
		<div className={cardClasses}>
			<h2 className="card__header">{title}</h2>
		</div>
	);
}
```

---

## Project Bootstrapping

**Problem:** What is the best way to start a new React project?

**Theory:** The React ecosystem provides several powerful tools for bootstrapping a new application. The choice depends on your project's needs, such as whether you need server-side rendering (SSR) or a simple single-page application (SPA).

| Tool                 | Type       | Best For                                                                                                                                                |
| -------------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Vite**             | Build Tool | **Modern SPAs.** Extremely fast development server and optimized builds. The current recommended choice for client-side React apps.                     |
| **Next.js**          | Framework  | **Production-grade applications.** Supports SSR, SSG, API routes, and file-based routing out of the box. Ideal for SEO and performance.                 |
| **Create React App** | Build Tool | **Legacy / Learning.** The original official bootstrapping tool. Now largely superseded by Vite due to performance. Still good for learning the basics. |

**Key Takeaway:**

-   For a new client-rendered Single Page Application (SPA), **use Vite**.
-   For a new application that needs SEO, server-rendering, or a full-stack solution, **use Next.js**.

```bash
# Start a new project with Vite (Recommended for SPAs)
npm create vite@latest my-react-app -- --template react

# Start a new project with Next.js (Recommended for SSR / Production)
npx create-next-app@latest my-next-app
```

---

## Developer Tooling

**Problem:** What tools are essential for efficient and high-quality React development?

**Theory:** A good developer experience is key to productivity. The React ecosystem has a few essential tools for debugging, linting, and formatting that you should use in every project.

### React DevTools

An essential browser extension (for Chrome, Firefox, Edge) that allows you to inspect the React component hierarchy, view props and state, and profile performance.

**Key Features:**

-   **Components Inspector:** View the component tree, inspect/edit props and state.
-   **Profiler:** Record and analyze rendering performance to find bottlenecks.
-   **"Why did this render?"**: Helps diagnose unnecessary re-renders.

### ESLint (`eslint-plugin-react-hooks`)

A code linter that analyzes your code to find problems. The `eslint-plugin-react-hooks` plugin is crucial as it enforces the **Rules of Hooks**, preventing common mistakes.

**Core Rules Enforced:**

1.  Only call Hooks at the top level (not inside loops, conditions, or nested functions).
2.  Only call Hooks from React function components or custom Hooks.

### Prettier

An opinionated code formatter that automatically formats your code to ensure a consistent style across the entire codebase. This eliminates debates about code style and makes the code more readable.
