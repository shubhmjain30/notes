# Building Interactivity: State, Events, and Hooks

This section explores how React enables interactive user interfaces through state management, event handling, and the Hooks API.

[[reactjs-roadmap|← Back to React.js Roadmap]]

## The Hooks Revolution

**Problem:** How do we reuse stateful logic between components without complex patterns like higher-order components or render props?

**Theory:** Hooks are functions that let you "hook into" React state and lifecycle features from functional components. They solve several problems that existed in class components:

-   Difficult to reuse stateful logic between components
-   Complex components become hard to understand
-   Classes confuse both people and machines (this binding, etc.)

**When introduced:** React 16.8 (February 2019)

**Rules of Hooks:**

-   Only call hooks at the top level (not inside loops, conditions, or nested functions)
-   Only call hooks from React function components or custom hooks

## Essential Hooks

### useState

**Problem:** How do we add and manage state in functional components?

**Theory:** `useState` is the most basic hook that adds local state to functional components. It returns a pair: the current state value and a function to update it.

**When to use:**

-   For simple state values (strings, numbers, booleans)
-   When state updates don't depend on previous state
-   When you don't need complex state logic

```jsx
function Counter() {
	// State declaration with initial value
	const [count, setCount] = useState(0);

	// Function that uses the setter
	const increment = () => setCount(count + 1);

	// Using previous state safely
	const incrementSafe = () => setCount((prevCount) => prevCount + 1);

	return (
		<div>
			<p>Count: {count}</p>
			<button onClick={increment}>Increment</button>
			<button onClick={incrementSafe}>Safe Increment</button>
		</div>
	);
}

// Object state example
function UserForm() {
	const [user, setUser] = useState({
		name: "",
		email: "",
	});

	const handleNameChange = (e) => {
		// Important: Spread the previous state to avoid losing other fields
		setUser({
			...user,
			name: e.target.value,
		});
	};

	return (
		<form>
			<input
				value={user.name}
				onChange={handleNameChange}
				placeholder="Name"
			/>
		</form>
	);
}
```

### useEffect

**Problem:** How do we perform side effects (data fetching, subscriptions, DOM manipulation) in functional components?

**Theory:** `useEffect` lets you perform side effects after render. It serves the same purpose as `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` combined in class components.

**When to use:**

-   Data fetching
-   Setting up subscriptions or event listeners
-   Manually changing the DOM
-   Logging, analytics

**Effect timing patterns:**

```jsx
// 1. Run on every render (rarely needed)
useEffect(() => {
	document.title = `Count: ${count}`;
});

// 2. Run only on mount (empty dependency array)
useEffect(() => {
	console.log("Component mounted");
}, []);

// 3. Run on specific dependencies changing
useEffect(() => {
	console.log(`Count changed to: ${count}`);
}, [count]);

// 4. Cleanup (return function runs before next effect or unmount)
useEffect(() => {
	const subscription = someAPI.subscribe();

	// Cleanup function
	return () => {
		subscription.unsubscribe();
	};
}, [someAPI]);
```

**Data fetching example:**

```jsx
function UserProfile({ userId }) {
	const [user, setUser] = useState(null);
	const [loading, setLoading] = useState(true);
	const [error, setError] = useState(null);

	useEffect(() => {
		// Reset state when userId changes
		setLoading(true);
		setError(null);

		// Create an abort controller for cleanup
		const controller = new AbortController();

		async function fetchUser() {
			try {
				const response = await fetch(
					`https://api.example.com/users/${userId}`,
					{ signal: controller.signal }
				);

				if (!response.ok) {
					throw new Error("Failed to fetch user");
				}

				const data = await response.json();
				setUser(data);
			} catch (err) {
				// Don't update state if the request was aborted
				if (err.name !== "AbortError") {
					setError(err.message);
				}
			} finally {
				setLoading(false);
			}
		}

		fetchUser();

		// Cleanup function to abort fetch on unmount or userId change
		return () => controller.abort();
	}, [userId]);

	if (loading) return <p>Loading...</p>;
	if (error) return <p>Error: {error}</p>;
	if (!user) return null;

	return (
		<div>
			<h1>{user.name}</h1>
			<p>{user.email}</p>
		</div>
	);
}
```

### useContext

**Problem:** How do we share data across many components without "prop drilling" (passing props through many intermediate components)?

**Theory:** `useContext` provides a way to pass data through the component tree without having to pass props down manually at every level.

**When to use:**

-   For global data needed by many components (themes, user authentication, language preferences)
-   When intermediate components don't need the data being passed through them
-   For state that changes infrequently (frequent context changes can hurt performance)

```jsx
// 1. Create a context
const ThemeContext = createContext("light");

// 2. Provide the context value
function App() {
	const [theme, setTheme] = useState("light");

	return (
		<ThemeContext.Provider value={{ theme, setTheme }}>
			<Header />
			<MainContent />
			<Footer />
		</ThemeContext.Provider>
	);
}

// 3. Consume the context in a deeply nested component
function ThemedButton() {
	// Get the context value
	const { theme, setTheme } = useContext(ThemeContext);

	return (
		<button
			className={`btn-${theme}`}
			onClick={() => setTheme(theme === "light" ? "dark" : "light")}
		>
			Toggle Theme
		</button>
	);
}
```

## Form Handling

### Controlled Components

**Problem:** How do we manage form inputs in React while maintaining a "single source of truth" for data?

**Theory:** In controlled components, form elements like `<input>`, `<textarea>`, and `<select>` have their values controlled by React state. The state serves as the "single source of truth" for the input.

**When to use:**

-   When you need to validate input as the user types
-   When you need to conditionally disable the submit button
-   When you need to enforce input formatting
-   When you need to handle dynamic inputs

```jsx
function LoginForm() {
	const [formData, setFormData] = useState({
		email: "",
		password: "",
	});
	const [errors, setErrors] = useState({});

	// Handle changes for all inputs
	const handleChange = (e) => {
		const { name, value } = e.target;

		setFormData({
			...formData,
			[name]: value,
		});

		// Clear error when user starts typing
		if (errors[name]) {
			setErrors({
				...errors,
				[name]: null,
			});
		}
	};

	const validate = () => {
		const newErrors = {};

		if (!formData.email) {
			newErrors.email = "Email is required";
		} else if (!/\S+@\S+\.\S+/.test(formData.email)) {
			newErrors.email = "Email is invalid";
		}

		if (!formData.password) {
			newErrors.password = "Password is required";
		} else if (formData.password.length < 8) {
			newErrors.password = "Password must be at least 8 characters";
		}

		setErrors(newErrors);
		return Object.keys(newErrors).length === 0;
	};

	const handleSubmit = (e) => {
		e.preventDefault();

		if (validate()) {
			console.log("Form submitted:", formData);
			// Submit to server
		}
	};

	return (
		<form onSubmit={handleSubmit}>
			<div>
				<label htmlFor="email">Email</label>
				<input
					type="email"
					id="email"
					name="email"
					value={formData.email}
					onChange={handleChange}
				/>
				{errors.email && <p className="error">{errors.email}</p>}
			</div>

			<div>
				<label htmlFor="password">Password</label>
				<input
					type="password"
					id="password"
					name="password"
					value={formData.password}
					onChange={handleChange}
				/>
				{errors.password && <p className="error">{errors.password}</p>}
			</div>

			<button type="submit">Log In</button>
		</form>
	);
}
```

### Uncontrolled Components

**Problem:** How do we handle form inputs with minimal code when we don't need to control every keystroke?

**Theory:** Uncontrolled components let the DOM handle form data internally, using refs to access values when needed (typically on submit).

**When to use:**

-   For simple forms without complex validation
-   When performance is a concern (many inputs)
-   When integrating with non-React code

```jsx
function SimpleForm() {
	// Create refs for form elements
	const nameRef = useRef(null);
	const emailRef = useRef(null);

	const handleSubmit = (e) => {
		e.preventDefault();

		// Access current values from refs
		const name = nameRef.current.value;
		const email = emailRef.current.value;

		console.log({ name, email });
		// Submit to server
	};

	return (
		<form onSubmit={handleSubmit}>
			<div>
				<label htmlFor="name">Name</label>
				<input type="text" id="name" ref={nameRef} defaultValue="" />
			</div>

			<div>
				<label htmlFor="email">Email</label>
				<input type="email" id="email" ref={emailRef} defaultValue="" />
			</div>

			<button type="submit">Submit</button>
		</form>
	);
}
```

## Additional Hooks

### useReducer

**Problem:** How do we manage complex state logic involving multiple sub-values or when the next state depends on the previous one?

**Theory:** `useReducer` is an alternative to `useState` that's better for complex state logic. It takes a reducer function and an initial state, returning the current state and a dispatch function.

**When to use:**

-   When state logic is complex with multiple sub-values
-   When the next state depends on the previous state
-   When you want to centralize state update logic

```jsx
// Reducer function (pure function)
function todoReducer(state, action) {
	switch (action.type) {
		case "ADD_TODO":
			return [
				...state,
				{
					id: Date.now(),
					text: action.payload,
					completed: false,
				},
			];
		case "TOGGLE_TODO":
			return state.map((todo) =>
				todo.id === action.payload
					? { ...todo, completed: !todo.completed }
					: todo
			);
		case "DELETE_TODO":
			return state.filter((todo) => todo.id !== action.payload);
		default:
			return state;
	}
}

function TodoApp() {
	// useReducer instead of useState
	const [todos, dispatch] = useReducer(todoReducer, []);
	const [text, setText] = useState("");

	const handleSubmit = (e) => {
		e.preventDefault();
		if (!text.trim()) return;

		// Dispatch action to add todo
		dispatch({ type: "ADD_TODO", payload: text });
		setText("");
	};

	return (
		<div>
			<form onSubmit={handleSubmit}>
				<input
					value={text}
					onChange={(e) => setText(e.target.value)}
					placeholder="Add todo"
				/>
				<button type="submit">Add</button>
			</form>

			<ul>
				{todos.map((todo) => (
					<li
						key={todo.id}
						style={{
							textDecoration: todo.completed
								? "line-through"
								: "none",
						}}
					>
						<span
							onClick={() =>
								dispatch({
									type: "TOGGLE_TODO",
									payload: todo.id,
								})
							}
						>
							{todo.text}
						</span>
						<button
							onClick={() =>
								dispatch({
									type: "DELETE_TODO",
									payload: todo.id,
								})
							}
						>
							Delete
						</button>
					</li>
				))}
			</ul>
		</div>
	);
}
```

### useRef

**Problem:** How do we persist values between renders without causing re-renders or access DOM elements directly?

**Theory:** `useRef` returns a mutable ref object whose `.current` property is initialized to the passed argument. The object persists for the full lifetime of the component.

**When to use:**

-   To access DOM elements directly
-   To keep mutable values that don't trigger re-renders
-   To store previous values of props or state

```jsx
function TextInputWithFocusButton() {
	// Create a ref to store the input DOM element
	const inputRef = useRef(null);

	// Function to focus the input
	const focusInput = () => {
		inputRef.current.focus();
	};

	return (
		<div>
			<input ref={inputRef} type="text" />
			<button onClick={focusInput}>Focus Input</button>
		</div>
	);
}

// Using useRef to track previous values
function Counter() {
	const [count, setCount] = useState(0);
	const prevCountRef = useRef();

	useEffect(() => {
		// Update ref after render
		prevCountRef.current = count;
	});

	const prevCount = prevCountRef.current;

	return (
		<div>
			<h1>
				Now: {count}, Before: {prevCount}
			</h1>
			<button onClick={() => setCount(count + 1)}>Increment</button>
		</div>
	);
}
```

### useCallback and useMemo

**Problem:** How do we optimize performance by preventing unnecessary recalculations and re-renders?

**Theory:**

-   `useCallback` returns a memoized callback function that only changes if one of its dependencies changes.
-   `useMemo` returns a memoized value that only recalculates when one of its dependencies changes.

**When to use:**

-   `useCallback`: When passing callbacks to optimized child components that rely on reference equality
-   `useMemo`: For expensive calculations that shouldn't be redone on every render

```jsx
function SearchResults({ query, onItemClick }) {
	// Memoize expensive calculation
	const filteredItems = useMemo(() => {
		console.log("Filtering items...");
		return items.filter((item) =>
			item.name.toLowerCase().includes(query.toLowerCase())
		);
	}, [query]); // Only recalculate when query changes

	// Memoize callback function
	const handleItemClick = useCallback(
		(id) => {
			console.log("Item clicked:", id);
			onItemClick(id);
		},
		[onItemClick]
	); // Only recreate when onItemClick changes

	return (
		<ul>
			{filteredItems.map((item) => (
				<ResultItem
					key={item.id}
					item={item}
					onClick={handleItemClick}
				/>
			))}
		</ul>
	);
}

// Child component using React.memo to prevent unnecessary re-renders
const ResultItem = React.memo(function ResultItem({ item, onClick }) {
	console.log("Rendering item:", item.id);
	return <li onClick={() => onClick(item.id)}>{item.name}</li>;
});
```

Understanding these hooks and patterns enables you to build truly interactive React applications with clean, maintainable code. The React Hooks API has revolutionized how we write React components, making it easier to reuse logic and keep related code together.
