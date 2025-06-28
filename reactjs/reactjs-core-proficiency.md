# 🔧 Stage 2: Core Proficiency (Intermediate)

← [Back to React.js Interview Prep Checklist](README.md)

Master these intermediate concepts to build dynamic, interactive React applications. This stage focuses on Hooks, advanced patterns, and essential tooling.

---

## ✅ Checklist: Core Proficiency

-   [ ] **Hooks Fundamentals** – useState, useEffect, rules of hooks
-   [ ] **Derived & Calculated State** – memoization with useMemo
-   [ ] **Refs & DOM Access** – useRef, forwarding refs
-   [ ] **Custom Hooks** – abstraction, composition, naming conventions
-   [ ] **Context API** – provider patterns, avoiding re-renders
-   [ ] **Error Boundaries** – lifecycle methods, fallback UIs
-   [ ] **Forms** – controlled vs. uncontrolled, libraries (React Hook Form/Formik)
-   [ ] **Routing** – React Router v6 dynamic routing, nested routes
-   [ ] **Basic Testing** – React Testing Library, Jest, act(), mock functions
-   [ ] **TypeScript with React** – typing props, generics, utility types

---

## Hooks Fundamentals

### The Rules of Hooks

**Problem:** Why do Hooks have specific rules, and how do they work internally?

**Theory:** Hooks are a revolutionary addition to React that allow you to use state and other React features without writing a class component. However, they rely on a specific implementation detail - **the order in which they are called**.

React maintains an internal "memory cell" list for each component that uses Hooks. Each time a Hook is called during rendering, React moves to the next memory cell. This is why Hooks must be called in the exact same order on every render - so React can correctly preserve state between multiple `useState` and `useEffect` calls.

**How Hooks Work Internally:**

1. React associates Hook calls with components using the call stack
2. Each component has a "memory cells" array where state values are stored
3. A "cursor" keeps track of which Hook is currently being processed
4. The call order must remain stable between renders for this system to work

**The Two Rules:**

1. **Only call Hooks at the top level** - Never inside loops, conditions, or nested functions
2. **Only call Hooks from React functions** - React function components or custom Hooks

```jsx
// ❌ Violating Rules of Hooks
function BadComponent({ showUser }) {
	if (showUser) {
		const [user, setUser] = useState(null); // ❌ Conditional Hook call
	}

	const [count, setCount] = useState(0);

	for (let i = 0; i < 3; i++) {
		const [value, setValue] = useState(i); // ❌ Hook in loop
	}

	function helper() {
		const [data, setData] = useState([]); // ❌ Hook in nested function
	}

	return <div>{count}</div>;
}

// ✅ Following Rules of Hooks
function GoodComponent({ showUser }) {
	const [user, setUser] = useState(null);
	const [count, setCount] = useState(0);
	const [values, setValues] = useState([0, 1, 2]);

	// Conditional logic using the state
	const userDisplay = showUser ? user : null;

	return (
		<div>
			{userDisplay && <div>{userDisplay.name}</div>}
			<div>{count}</div>
		</div>
	);
}
```

### useState Deep Dive

**Problem:** How do we manage complex state updates efficiently?

**Theory:** Unlike class components where all state is stored in a single object, the `useState` Hook encourages splitting state into multiple, independent pieces. This helps keep state management clean and focused. When dealing with complex state:

1. **State immutability** is crucial - never mutate state objects directly
2. Use the **functional update form** when new state depends on previous state
3. For complex objects, consider splitting state into multiple `useState` calls or use `useReducer` for related state transitions
4. Remember that state updates may be batched for better performance

```jsx
function ShoppingCart() {
	const [cart, setCart] = useState({
		items: [],
		total: 0,
		discountCode: "",
		shippingInfo: {
			address: "",
			city: "",
			zipCode: "",
		},
	});

	// ❌ Direct mutation - DON'T DO THIS
	const addItemBad = (item) => {
		cart.items.push(item); // Mutating state directly
		setCart(cart); // React won't detect the change
	};

	// ✅ Proper immutable update
	const addItem = (item) => {
		setCart((prevCart) => ({
			...prevCart,
			items: [...prevCart.items, { ...item, id: Date.now() }],
			total: prevCart.total + item.price,
		}));
	};

	// ✅ Updating nested objects
	const updateShippingAddress = (address) => {
		setCart((prevCart) => ({
			...prevCart,
			shippingInfo: {
				...prevCart.shippingInfo,
				address: address,
			},
		}));
	};

	// ✅ Functional state update for complex logic
	const removeItem = (itemId) => {
		setCart((prevCart) => {
			const itemToRemove = prevCart.items.find(
				(item) => item.id === itemId
			);
			const updatedItems = prevCart.items.filter(
				(item) => item.id !== itemId
			);

			return {
				...prevCart,
				items: updatedItems,
				total: prevCart.total - (itemToRemove?.price || 0),
			};
		});
	};

	// ✅ Multiple state updates in single function
	const applyDiscount = (code) => {
		setCart((prevCart) => {
			const discount = calculateDiscount(code, prevCart.total);
			return {
				...prevCart,
				discountCode: code,
				total: prevCart.total - discount,
			};
		});
	};

	return (
		<div>
			<h2>Shopping Cart ({cart.items.length} items)</h2>
			<div>Total: ${cart.total.toFixed(2)}</div>

			{cart.items.map((item) => (
				<div key={item.id}>
					{item.name} - ${item.price}
					<button onClick={() => removeItem(item.id)}>Remove</button>
				</div>
			))}

			<input
				placeholder="Discount code"
				value={cart.discountCode}
				onChange={(e) =>
					setCart((prev) => ({
						...prev,
						discountCode: e.target.value,
					}))
				}
			/>
			<button onClick={() => applyDiscount(cart.discountCode)}>
				Apply Discount
			</button>
		</div>
	);
}
```

### useEffect Mastery

**Problem:** How do we manage side effects, lifecycle events, and cleanup properly?

```jsx
function UserProfile({ userId }) {
	const [user, setUser] = useState(null);
	const [loading, setLoading] = useState(true);
	const [error, setError] = useState(null);

	// Effect with dependency array - runs when userId changes
	useEffect(() => {
		let cancelled = false; // Cleanup flag

		const fetchUser = async () => {
			try {
				setLoading(true);
				setError(null);

				const response = await fetch(`/api/users/${userId}`);
				if (!response.ok) throw new Error("User not found");

				const userData = await response.json();

				// Only update state if component is still mounted
				if (!cancelled) {
					setUser(userData);
				}
			} catch (err) {
				if (!cancelled) {
					setError(err.message);
				}
			} finally {
				if (!cancelled) {
					setLoading(false);
				}
			}
		};

		fetchUser();

		// Cleanup function
		return () => {
			cancelled = true;
		};
	}, [userId]); // Dependency array

	// Effect for document title - runs on every render where user changes
	useEffect(() => {
		if (user) {
			document.title = `Profile - ${user.name}`;
		}

		// Cleanup when component unmounts
		return () => {
			document.title = "React App";
		};
	}, [user]);

	// Effect with no dependencies - runs only on mount
	useEffect(() => {
		console.log("Component mounted");

		// Setup event listener
		const handleKeyPress = (e) => {
			if (e.key === "Escape") {
				// Handle escape key
			}
		};

		window.addEventListener("keydown", handleKeyPress);

		// Cleanup event listener
		return () => {
			window.removeEventListener("keydown", handleKeyPress);
			console.log("Component unmounted");
		};
	}, []); // Empty dependency array

	if (loading) return <div>Loading...</div>;
	if (error) return <div>Error: {error}</div>;
	if (!user) return <div>User not found</div>;

	return (
		<div>
			<h1>{user.name}</h1>
			<p>{user.email}</p>
		</div>
	);
}
```

### useEffect Common Patterns

```jsx
// Pattern 1: Data fetching with AbortController
function DataFetcher({ endpoint }) {
	const [data, setData] = useState(null);
	const [loading, setLoading] = useState(true);

	useEffect(() => {
		const controller = new AbortController();

		const fetchData = async () => {
			try {
				setLoading(true);
				const response = await fetch(endpoint, {
					signal: controller.signal,
				});
				const result = await response.json();
				setData(result);
			} catch (error) {
				if (error.name !== "AbortError") {
					console.error("Fetch error:", error);
				}
			} finally {
				setLoading(false);
			}
		};

		fetchData();

		return () => controller.abort();
	}, [endpoint]);

	return loading ? <div>Loading...</div> : <div>{JSON.stringify(data)}</div>;
}

// Pattern 2: Timer/Interval cleanup
function Timer() {
	const [seconds, setSeconds] = useState(0);
	const [isRunning, setIsRunning] = useState(false);

	useEffect(() => {
		let intervalId;

		if (isRunning) {
			intervalId = setInterval(() => {
				setSeconds((prev) => prev + 1);
			}, 1000);
		}

		return () => {
			if (intervalId) {
				clearInterval(intervalId);
			}
		};
	}, [isRunning]);

	return (
		<div>
			<div>Time: {seconds}s</div>
			<button onClick={() => setIsRunning(!isRunning)}>
				{isRunning ? "Pause" : "Start"}
			</button>
			<button
				onClick={() => {
					setSeconds(0);
					setIsRunning(false);
				}}
			>
				Reset
			</button>
		</div>
	);
}

// Pattern 3: Window size tracking
function useWindowSize() {
	const [windowSize, setWindowSize] = useState({
		width: undefined,
		height: undefined,
	});

	useEffect(() => {
		function handleResize() {
			setWindowSize({
				width: window.innerWidth,
				height: window.innerHeight,
			});
		}

		window.addEventListener("resize", handleResize);
		handleResize(); // Call handler right away so state gets updated with initial window size

		return () => window.removeEventListener("resize", handleResize);
	}, []);

	return windowSize;
}
```

---

## Derived & Calculated State

### useMemo for Expensive Calculations

**Problem:** How do we avoid recalculating expensive values on every render?

**Theory:** Every time a component renders in React, all the code inside the component runs again. This means any complex calculations or transformations inside your component will be performed on each render, even if the inputs haven't changed. This can significantly impact performance.

The `useMemo` Hook addresses this issue by:

1. **Memoizing computationally expensive values** - Storing the result of calculations and returning the cached result when the same inputs occur again
2. **Recalculating only when dependencies change** - Similar to `useEffect`, `useMemo` takes a dependency array that determines when the value should be recomputed
3. **Avoiding unnecessary rendering in child components** - When passing objects or arrays to child components, memoization prevents unnecessary re-renders

**Key Principles:**

-   Only use `useMemo` for computationally expensive operations
-   Include all values used in the calculation in the dependency array
-   Don't overuse - there's overhead to memoization itself
-   For simple calculations, regular variables are more efficient

```jsx
function ExpensiveList({ items, searchTerm, sortBy, filterCategory }) {
	// ❌ Expensive calculation on every render
	const processedItemsBad = items
		.filter(
			(item) =>
				item.name.toLowerCase().includes(searchTerm.toLowerCase()) &&
				(filterCategory === "all" || item.category === filterCategory)
		)
		.sort((a, b) => {
			if (sortBy === "name") return a.name.localeCompare(b.name);
			if (sortBy === "price") return a.price - b.price;
			if (sortBy === "date")
				return new Date(b.createdAt) - new Date(a.createdAt);
			return 0;
		})
		.map((item) => ({
			...item,
			formattedPrice: formatCurrency(item.price),
			timeAgo: formatTimeAgo(item.createdAt),
		}));

	// ✅ Memoized expensive calculation
	const processedItems = useMemo(() => {
		console.log("Recalculating processed items..."); // Will only log when dependencies change

		return items
			.filter(
				(item) =>
					item.name
						.toLowerCase()
						.includes(searchTerm.toLowerCase()) &&
					(filterCategory === "all" ||
						item.category === filterCategory)
			)
			.sort((a, b) => {
				if (sortBy === "name") return a.name.localeCompare(b.name);
				if (sortBy === "price") return a.price - b.price;
				if (sortBy === "date")
					return new Date(b.createdAt) - new Date(a.createdAt);
				return 0;
			})
			.map((item) => ({
				...item,
				formattedPrice: formatCurrency(item.price),
				timeAgo: formatTimeAgo(item.createdAt),
			}));
	}, [items, searchTerm, sortBy, filterCategory]);

	// Derived values that don't need memoization (cheap calculations)
	const totalItems = processedItems.length;
	const totalValue = processedItems.reduce(
		(sum, item) => sum + item.price,
		0
	);

	return (
		<div>
			<div className="summary">
				<p>Showing {totalItems} items</p>
				<p>Total value: {formatCurrency(totalValue)}</p>
			</div>

			<div className="items">
				{processedItems.map((item) => (
					<ItemCard key={item.id} item={item} />
				))}
			</div>
		</div>
	);
}

// Helper functions
function formatCurrency(amount) {
	return new Intl.NumberFormat("en-US", {
		style: "currency",
		currency: "USD",
	}).format(amount);
}

function formatTimeAgo(date) {
	const now = new Date();
	const diffInMs = now - new Date(date);
	const diffInDays = Math.floor(diffInMs / (1000 * 60 * 60 * 24));

	if (diffInDays === 0) return "Today";
	if (diffInDays === 1) return "Yesterday";
	return `${diffInDays} days ago`;
}
```

### useMemo vs Regular Variables

```jsx
function ComponentExample({ data, filters }) {
	// ❌ This will recalculate on EVERY render, even if data/filters haven't changed
	const expensiveCalculation = data
		.filter(
			(item) =>
				filters.category === "all" || item.category === filters.category
		)
		.map((item) => performExpensiveTransformation(item))
		.sort((a, b) => a.score - b.score);

	// ✅ This will only recalculate when data or filters change
	const memoizedCalculation = useMemo(() => {
		return data
			.filter(
				(item) =>
					filters.category === "all" ||
					item.category === filters.category
			)
			.map((item) => performExpensiveTransformation(item))
			.sort((a, b) => a.score - b.score);
	}, [data, filters]);

	// ✅ For simple/cheap calculations, regular variables are fine
	const count = data.length;
	const hasData = count > 0;
	const isEmpty = !hasData;

	return (
		<div>
			{isEmpty ? (
				<p>No data available</p>
			) : (
				<div>
					<p>Found {count} items</p>
					{memoizedCalculation.map((item) => (
						<div key={item.id}>{item.name}</div>
					))}
				</div>
			)}
		</div>
	);
}
```

### Advanced useMemo Patterns

```jsx
// Pattern 1: Memoizing object/array creation
function UserList({ users, selectedIds }) {
	// ❌ Creates new object on every render, causing child re-renders
	const userStats = {
		total: users.length,
		selected: selectedIds.length,
		active: users.filter((u) => u.isActive).length,
	};

	// ✅ Memoized object creation
	const memoizedUserStats = useMemo(
		() => ({
			total: users.length,
			selected: selectedIds.length,
			active: users.filter((u) => u.isActive).length,
		}),
		[users, selectedIds]
	);

	return <UserStatsDisplay stats={memoizedUserStats} />;
}

// Pattern 2: Memoizing with complex dependencies
function DataProcessor({ rawData, config, userPreferences }) {
	const processedData = useMemo(() => {
		// Only recalculate when specific config properties change
		const relevantConfig = {
			algorithm: config.algorithm,
			threshold: config.threshold,
			includeMetadata: config.includeMetadata,
		};

		return processData(rawData, relevantConfig, userPreferences);
	}, [
		rawData,
		config.algorithm,
		config.threshold,
		config.includeMetadata,
		userPreferences,
	]);

	return <DataVisualization data={processedData} />;
}
```

---

## Refs & DOM Access

### useRef for DOM Manipulation

**Problem:** How do we access DOM elements directly when React's declarative approach isn't sufficient?

**Theory:** While React's declarative approach normally handles DOM updates for you, sometimes you need direct access to DOM elements. Common use cases include:

1. **Managing focus, text selection, or media playback**
2. **Triggering imperative animations**
3. **Integrating with third-party DOM libraries**

`useRef` creates a mutable reference object with a `.current` property that persists across renders. Unlike state, updating a ref doesn't trigger a re-render.

**Key Concepts:**

-   A ref is like a "box" that can hold a mutable value in its `.current` property
-   Changes to `.current` don't trigger re-renders
-   Refs give you a way to access DOM nodes created in the render method
-   You should minimize direct DOM manipulation and use React's declarative approach when possible

```jsx
function FocusableInput() {
	const inputRef = useRef(null);
	const [isVisible, setIsVisible] = useState(false);

	// Focus input when component mounts
	useEffect(() => {
		if (inputRef.current) {
			inputRef.current.focus();
		}
	}, []);

	// Focus input when it becomes visible
	useEffect(() => {
		if (isVisible && inputRef.current) {
			inputRef.current.focus();
		}
	}, [isVisible]);

	const handleClearClick = () => {
		if (inputRef.current) {
			inputRef.current.value = "";
			inputRef.current.focus();
		}
	};

	return (
		<div>
			<button onClick={() => setIsVisible(!isVisible)}>
				{isVisible ? "Hide" : "Show"} Input
			</button>

			{isVisible && (
				<div>
					<input
						ref={inputRef}
						type="text"
						placeholder="This input will auto-focus"
					/>
					<button onClick={handleClearClick}>Clear & Focus</button>
				</div>
			)}
		</div>
	);
}
```

### useRef for Storing Mutable Values

**Problem:** How do we store values that persist across renders but don't trigger re-renders when changed?

**Theory:** Besides DOM access, `useRef` solves another important problem: storing mutable values that should persist between renders without causing re-renders. Unlike state:

1. Updating a ref doesn't trigger component re-rendering
2. Refs persist throughout the component's lifecycle
3. Refs can hold any value, not just DOM elements
4. Changes to refs are synchronous, unlike state updates which may be batched

Common use cases include:

-   Storing previous state values
-   Keeping track of timers and intervals
-   Storing instances of external libraries
-   Solving the "stale closure" problem in callbacks

```jsx
function Timer() {
	const [seconds, setSeconds] = useState(0);
	const [isRunning, setIsRunning] = useState(false);

	// useRef to store interval ID - doesn't cause re-renders when changed
	const intervalRef = useRef(null);

	// useRef to store the latest callback - solves stale closure problem
	const savedCallback = useRef();

	// Update saved callback
	useEffect(() => {
		savedCallback.current = () => {
			setSeconds((prev) => prev + 1);
		};
	});

	// Setup/cleanup interval
	useEffect(() => {
		if (isRunning) {
			intervalRef.current = setInterval(() => {
				savedCallback.current();
			}, 1000);
		} else if (intervalRef.current) {
			clearInterval(intervalRef.current);
			intervalRef.current = null;
		}

		return () => {
			if (intervalRef.current) {
				clearInterval(intervalRef.current);
			}
		};
	}, [isRunning]);

	const handleReset = () => {
		setSeconds(0);
		setIsRunning(false);
	};

	return (
		<div>
			<div>Seconds: {seconds}</div>
			<button onClick={() => setIsRunning(!isRunning)}>
				{isRunning ? "Pause" : "Start"}
			</button>
			<button onClick={handleReset}>Reset</button>
		</div>
	);
}
```

## Custom Hooks

### Creating Reusable Logic

**Problem:** How do we extract and reuse stateful logic across multiple components?

**Theory:** Custom Hooks are JavaScript functions that start with "use" and may call other Hooks. They represent a powerful pattern for:

1. **Extracting component logic into reusable functions**
2. **Sharing stateful logic between components without render props or higher-order components**
3. **Creating a clean separation between UI and stateful logic**
4. **Building component libraries with consistent behavior**

Custom Hooks follow the same rules as built-in Hooks - they must be called at the top level and only from React function components or custom Hooks.

**Benefits of Custom Hooks:**

-   Cleaner component code with logic extracted into focused functions
-   Reusing the same logic across multiple components
-   Easier testing of complex stateful logic
-   Building your own Hook library for team conventions

```jsx
// Custom hook for managing local storage
function useLocalStorage(key, initialValue) {
	// Get initial value from localStorage or use provided initial value
	const [storedValue, setStoredValue] = useState(() => {
		try {
			const item = window.localStorage.getItem(key);
			return item ? JSON.parse(item) : initialValue;
		} catch (error) {
			console.error(`Error reading localStorage key "${key}":`, error);
			return initialValue;
		}
	});

	// Return a wrapped version of useState's setter function that persists to localStorage
	const setValue = (value) => {
		try {
			// Allow value to be a function so we have the same API as useState
			const valueToStore =
				value instanceof Function ? value(storedValue) : value;
			setStoredValue(valueToStore);
			window.localStorage.setItem(key, JSON.stringify(valueToStore));
		} catch (error) {
			console.error(`Error setting localStorage key "${key}":`, error);
		}
	};

	// Listen for storage events (when localStorage is changed in another tab)
	useEffect(() => {
		const handleStorageChange = (e) => {
			if (e.key === key && e.newValue !== null) {
				try {
					setStoredValue(JSON.parse(e.newValue));
				} catch (error) {
					console.error(
						`Error parsing localStorage value for key "${key}":`,
						error
					);
				}
			}
		};

		window.addEventListener("storage", handleStorageChange);
		return () => window.removeEventListener("storage", handleStorageChange);
	}, [key]);

	return [storedValue, setValue];
}

// Using the custom hook
function Settings() {
	const [theme, setTheme] = useLocalStorage("theme", "light");
	const [language, setLanguage] = useLocalStorage("language", "en");
	const [notifications, setNotifications] = useLocalStorage(
		"notifications",
		true
	);

	return (
		<div>
			<h2>Settings</h2>

			<div>
				<label>
					Theme:
					<select
						value={theme}
						onChange={(e) => setTheme(e.target.value)}
					>
						<option value="light">Light</option>
						<option value="dark">Dark</option>
					</select>
				</label>
			</div>

			<div>
				<label>
					Language:
					<select
						value={language}
						onChange={(e) => setLanguage(e.target.value)}
					>
						<option value="en">English</option>
						<option value="es">Spanish</option>
						<option value="fr">French</option>
					</select>
				</label>
			</div>

			<div>
				<label>
					<input
						type="checkbox"
						checked={notifications}
						onChange={(e) => setNotifications(e.target.checked)}
					/>
					Enable notifications
				</label>
			</div>
		</div>
	);
}
```

---

## Context API

### Provider & Consumer Pattern

**Problem:** How do we share state across many components without prop drilling?

**Theory:** React's Context API provides a way to share values like themes, user data, or preferences across components without explicitly passing props through every level of the component tree. It solves the "prop drilling" problem where data is passed through multiple layers of components that don't actually need the data.

**The Context API consists of three main parts:**

1. `React.createContext()` - Creates a Context object with an optional default value
2. `Context.Provider` - A component that allows consuming components to subscribe to context changes
3. `Context.Consumer` or `useContext` Hook - Ways to consume the context data

**When to use Context:**

-   For global state that is needed by many components (themes, user data, preferences)
-   For deeply nested component trees where prop drilling becomes unwieldy
-   When data needs to be accessible by many components at different nesting levels

```jsx
// Create a context with a default value
const ThemeContext = React.createContext({
	theme: "light",
	toggleTheme: () => {},
});

// Provider component - makes the context available
function ThemeProvider({ children }) {
	const [theme, setTheme] = useState("light");

	const toggleTheme = () => {
		setTheme((prevTheme) => (prevTheme === "light" ? "dark" : "light"));
	};

	return (
		<ThemeContext.Provider value={{ theme, toggleTheme }}>
			{children}
		</ThemeContext.Provider>
	);
}

// Consumer component using useContext Hook
function ThemedButton() {
	const { theme, toggleTheme } = useContext(ThemeContext);

	return (
		<button
			onClick={toggleTheme}
			style={{
				backgroundColor: theme === "light" ? "#fff" : "#333",
				color: theme === "light" ? "#333" : "#fff",
				border: `1px solid ${theme === "light" ? "#333" : "#fff"}`,
			}}
		>
			Toggle Theme
		</button>
	);
}

// App with context
function App() {
	return (
		<ThemeProvider>
			<Header />
			<MainContent />
			<Footer />
		</ThemeProvider>
	);
}
```

### Context Performance Considerations

**Problem:** How do we prevent unnecessary re-renders when using Context?

**Theory:** When a context value changes, all components that consume that context will re-render, even if they only use a portion of the context value. This can lead to performance issues if:

1. Your context value is a complex object
2. The context changes frequently
3. Many components consume the same context

**Best practices for optimizing Context performance:**

1. **Split contexts by purpose** - Create separate contexts for values that change at different frequencies
2. **Memoize context values** - Use `useMemo` to prevent unnecessary context provider re-renders
3. **Use selective consumption** - Consume only the specific parts of context that a component needs
4. **Consider context alternatives** for performance-critical sections (component composition, prop drilling for shallow hierarchies)

```jsx
// PROBLEM: Every consumer re-renders when ANY part of value changes
function BadContextProvider({ children }) {
	const [user, setUser] = useState(null);
	const [theme, setTheme] = useState("light");
	const [notifications, setNotifications] = useState([]);

	// ❌ New object created on every render, causing all consumers to re-render
	const value = {
		user,
		setUser,
		theme,
		setTheme,
		notifications,
		setNotifications,
	};

	return <AppContext.Provider value={value}>{children}</AppContext.Provider>;
}

// SOLUTION: Split contexts by concern and memoize values
function OptimizedApp({ children }) {
	// Separate contexts by update frequency
	const [user, setUser] = useState(null);
	const [theme, setTheme] = useState("light");
	const [notifications, setNotifications] = useState([]);

	// Memoize context values
	const userContextValue = useMemo(() => ({ user, setUser }), [user]);
	const themeContextValue = useMemo(() => ({ theme, setTheme }), [theme]);
	const notificationContextValue = useMemo(
		() => ({ notifications, setNotifications }),
		[notifications]
	);

	return (
		<UserContext.Provider value={userContextValue}>
			<ThemeContext.Provider value={themeContextValue}>
				<NotificationContext.Provider value={notificationContextValue}>
					{children}
				</NotificationContext.Provider>
			</ThemeContext.Provider>
		</UserContext.Provider>
	);
}
```

---

## Error Boundaries

### Graceful Error Handling

**Problem:** How do we prevent a JavaScript error in one component from breaking the entire application?

**Theory:** Error Boundaries are special React components that:

1. **Catch JavaScript errors** anywhere in their child component tree
2. **Display fallback UI** instead of the component tree that crashed
3. **Log error information** to error reporting services

Error boundaries work like a JavaScript `catch {}` block, but for components. They catch errors during rendering, in lifecycle methods, and in constructors of components below them in the tree.

**Important limitations:**

-   Error boundaries do NOT catch errors in:
    -   Event handlers
    -   Asynchronous code (e.g., `setTimeout` or `requestAnimationFrame`)
    -   Server-side rendering
    -   Errors thrown in the error boundary itself

**Note:** Error Boundaries must be implemented using class components (not functional components with Hooks).

```jsx
// Creating an Error Boundary component
class ErrorBoundary extends React.Component {
	constructor(props) {
		super(props);
		this.state = { hasError: false, error: null, errorInfo: null };
	}

	// This lifecycle is invoked after an error is thrown
	static getDerivedStateFromError(error) {
		// Update state to show fallback UI
		return { hasError: true, error };
	}

	// This lifecycle lets you log the error
	componentDidCatch(error, errorInfo) {
		// Log the error to an error reporting service
		console.error("Error caught by boundary:", error, errorInfo);
		this.setState({ errorInfo });

		// In production, you would send to your error tracking service:
		// logErrorToService(error, errorInfo);
	}

	render() {
		if (this.state.hasError) {
			// You can render any custom fallback UI
			return (
				<div className="error-boundary">
					<h2>Something went wrong.</h2>
					<details style={{ whiteSpace: "pre-wrap" }}>
						<summary>Show error details</summary>
						{this.state.error && this.state.error.toString()}
						<br />
						{this.state.errorInfo &&
							this.state.errorInfo.componentStack}
					</details>
					<button onClick={() => this.setState({ hasError: false })}>
						Try again
					</button>
				</div>
			);
		}

		// If no error occurred, render children normally
		return this.props.children;
	}
}

// Using the Error Boundary
function App() {
	return (
		<div className="app">
			<Header />

			{/* Each section gets its own error boundary */}
			<ErrorBoundary>
				<UserProfile userId="123" />
			</ErrorBoundary>

			<ErrorBoundary>
				<Dashboard />
			</ErrorBoundary>

			<ErrorBoundary>
				<Footer />
			</ErrorBoundary>
		</div>
	);
}
```

---

## Forms

### Controlled vs. Uncontrolled Components

**Problem:** What are the different approaches to handling form inputs in React?

**Theory:** React offers two main ways to handle form inputs:

**1. Controlled Components:**

-   Form data is handled by React state
-   Every state mutation has an associated handler function
-   React is the "single source of truth" for input values
-   More code but offers more control and immediate validation

**2. Uncontrolled Components:**

-   Form data is handled by the DOM itself
-   Use refs to get values when needed (e.g., on form submission)
-   Less code but offers less direct control
-   More similar to traditional HTML forms

**When to use each:**

-   **Use controlled components** when you need: immediate field validation, conditional disabling of submit button, enforcing input format, or dynamic inputs
-   **Use uncontrolled components** for: simple forms without much validation, integrating with non-React code, or when performance is critical (rare inputs with many changes)

```jsx
// Controlled component example
function ControlledForm() {
	const [values, setValues] = useState({
		name: "",
		email: "",
		message: "",
	});
	const [errors, setErrors] = useState({});

	const handleChange = (e) => {
		const { name, value } = e.target;
		setValues({
			...values,
			[name]: value,
		});

		// Live validation
		if (name === "email" && value && !/\S+@\S+\.\S+/.test(value)) {
			setErrors((prev) => ({
				...prev,
				email: "Please enter a valid email address",
			}));
		} else {
			setErrors((prev) => ({
				...prev,
				[name]: undefined,
			}));
		}
	};

	const handleSubmit = (e) => {
		e.preventDefault();
		// Form is already validated during input
		console.log("Submitting:", values);
	};

	return (
		<form onSubmit={handleSubmit}>
			<div>
				<label htmlFor="name">Name:</label>
				<input
					id="name"
					name="name"
					value={values.name}
					onChange={handleChange}
					required
				/>
			</div>

			<div>
				<label htmlFor="email">Email:</label>
				<input
					id="email"
					name="email"
					type="email"
					value={values.email}
					onChange={handleChange}
					required
				/>
				{errors.email && <p className="error">{errors.email}</p>}
			</div>

			<div>
				<label htmlFor="message">Message:</label>
				<textarea
					id="message"
					name="message"
					value={values.message}
					onChange={handleChange}
					required
				/>
			</div>

			<button type="submit">Submit</button>
		</form>
	);
}

// Uncontrolled component example
function UncontrolledForm() {
	const nameRef = useRef();
	const emailRef = useRef();
	const messageRef = useRef();

	const handleSubmit = (e) => {
		e.preventDefault();

		// Access values via refs when form is submitted
		const formData = {
			name: nameRef.current.value,
			email: emailRef.current.value,
			message: messageRef.current.value,
		};

		console.log("Submitting:", formData);
	};

	return (
		<form onSubmit={handleSubmit}>
			<div>
				<label htmlFor="name">Name:</label>
				<input
					id="name"
					name="name"
					ref={nameRef}
					defaultValue=""
					required
				/>
			</div>

			<div>
				<label htmlFor="email">Email:</label>
				<input
					id="email"
					name="email"
					type="email"
					ref={emailRef}
					defaultValue=""
					required
				/>
			</div>

			<div>
				<label htmlFor="message">Message:</label>
				<textarea
					id="message"
					name="message"
					ref={messageRef}
					defaultValue=""
					required
				/>
			</div>

			<button type="submit">Submit</button>
		</form>
	);
}
```

### Form Libraries: React Hook Form & Formik

**Problem:** How do we manage complex forms in React without writing repetitive boilerplate code?

**Theory:** As forms get more complex with many fields, validations, and submit logic, managing them becomes challenging. Form libraries solve common issues like:

1. Field validation (including complex validation rules)
2. Handling form submission
3. Tracking form state (dirty, touched, errors)
4. Reducing unnecessary re-renders
5. Managing complex nested form structures

**React Hook Form** and **Formik** are the two most popular form libraries in the React ecosystem:

**React Hook Form:**

-   Focuses on performance with minimal re-renders
-   Uses uncontrolled components with refs by default
-   Lightweight with minimal boilerplate
-   Built for hooks from the ground up

**Formik:**

-   More mature with longer history
-   Uses controlled components by default
-   Highly configurable with rich API
-   Good for complex validation scenarios

```jsx
// Using React Hook Form
import { useForm } from "react-hook-form";

function ReactHookFormExample() {
	const {
		register,
		handleSubmit,
		formState: { errors, isSubmitting },
		reset,
	} = useForm();

	const onSubmit = async (data) => {
		try {
			// Simulate API call
			await new Promise((r) => setTimeout(r, 1000));
			console.log("Form submitted:", data);
			reset();
		} catch (error) {
			console.error("Submission error:", error);
		}
	};

	return (
		<form onSubmit={handleSubmit(onSubmit)}>
			<div>
				<label htmlFor="name">Name:</label>
				<input
					id="name"
					{...register("name", {
						required: "Name is required",
						minLength: {
							value: 2,
							message: "Name must be at least 2 characters",
						},
					})}
				/>
				{errors.name && <p className="error">{errors.name.message}</p>}
			</div>

			<div>
				<label htmlFor="email">Email:</label>
				<input
					id="email"
					type="email"
					{...register("email", {
						required: "Email is required",
						pattern: {
							value: /\S+@\S+\.\S+/,
							message: "Please enter a valid email",
						},
					})}
				/>
				{errors.email && (
					<p className="error">{errors.email.message}</p>
				)}
			</div>

			<button type="submit" disabled={isSubmitting}>
				{isSubmitting ? "Submitting..." : "Submit"}
			</button>
		</form>
	);
}
```

---

## Routing

### React Router v6 Concepts

**Problem:** How do we create multi-page experiences in a single-page React application?

**Theory:** React Router enables "client-side routing" in React applications, allowing users to navigate between different "pages" without full page reloads. This provides a smoother user experience while maintaining the performance advantages of a single-page application.

**Core Concepts in React Router v6:**

1. **BrowserRouter** - Provides routing functionality using HTML5 history API
2. **Routes & Route** - Components that define the route configuration
3. **Link & NavLink** - Components to create navigation without page reloads
4. **Outlet** - Component for rendering nested routes
5. **useParams, useSearchParams, useNavigate** - Hooks for accessing route parameters, query strings, and programmatic navigation

**Key Changes in v6:**

-   Routes are now relative and nested by default (simplified nesting)
-   `Switch` has been replaced by `Routes`
-   `useNavigate` replaces `useHistory`
-   Route elements are specified directly with an `element` prop rather than as children

```jsx
import {
	BrowserRouter,
	Routes,
	Route,
	Link,
	NavLink,
	Outlet,
	useParams,
	useNavigate,
	useSearchParams,
} from "react-router-dom";

// Basic routing setup
function App() {
	return (
		<BrowserRouter>
			<Navigation />

			<Routes>
				<Route path="/" element={<Home />} />
				<Route path="/about" element={<About />} />

				{/* Nested routes */}
				<Route path="/products" element={<ProductLayout />}>
					<Route index element={<ProductList />} />
					<Route path=":id" element={<ProductDetail />} />
				</Route>

				{/* Protected route */}
				<Route
					path="/admin"
					element={
						<ProtectedRoute>
							<Admin />
						</ProtectedRoute>
					}
				/>

				{/* Catch-all for 404s */}
				<Route path="*" element={<NotFound />} />
			</Routes>

			<Footer />
		</BrowserRouter>
	);
}

// Navigation with active styles
function Navigation() {
	return (
		<nav>
			<NavLink
				to="/"
				className={({ isActive }) => (isActive ? "active-link" : "")}
			>
				Home
			</NavLink>
			<NavLink
				to="/about"
				className={({ isActive }) => (isActive ? "active-link" : "")}
			>
				About
			</NavLink>
			<NavLink
				to="/products"
				className={({ isActive }) => (isActive ? "active-link" : "")}
			>
				Products
			</NavLink>
		</nav>
	);
}

// Layout component with outlet for nested routes
function ProductLayout() {
	return (
		<div className="product-section">
			<h1>Products</h1>
			<aside className="sidebar">
				<ProductFilter />
			</aside>
			<main className="content">
				{/* Nested routes render here */}
				<Outlet />
			</main>
		</div>
	);
}

// Using route parameters
function ProductDetail() {
	const { id } = useParams();
	const [product, setProduct] = useState(null);
	const navigate = useNavigate();

	useEffect(() => {
		async function fetchProduct() {
			try {
				const response = await fetch(`/api/products/${id}`);
				if (!response.ok) throw new Error("Product not found");
				const data = await response.json();
				setProduct(data);
			} catch (error) {
				navigate("/products", { replace: true });
			}
		}

		fetchProduct();
	}, [id, navigate]);

	if (!product) return <div>Loading...</div>;

	return (
		<div>
			<h2>{product.name}</h2>
			<p>{product.description}</p>
			<p>Price: ${product.price}</p>
			<button onClick={() => navigate(-1)}>Go Back</button>
		</div>
	);
}

// Using query parameters
function ProductList() {
	const [searchParams, setSearchParams] = useSearchParams();
	const category = searchParams.get("category") || "all";

	const handleCategoryChange = (newCategory) => {
		setSearchParams({ category: newCategory });
	};

	return (
		<div>
			<div className="filters">
				<button
					onClick={() => handleCategoryChange("all")}
					className={category === "all" ? "active" : ""}
				>
					All
				</button>
				<button
					onClick={() => handleCategoryChange("electronics")}
					className={category === "electronics" ? "active" : ""}
				>
					Electronics
				</button>
			</div>

			<div className="product-grid">
				{/* Products filtered by category */}
			</div>
		</div>
	);
}
```
