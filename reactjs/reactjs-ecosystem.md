# 🚀 Stage 4 — Scaling Up: The React Ecosystem

React itself is a focused UI library, not a complete framework. To build full-featured applications, you'll need to integrate with the broader React ecosystem of libraries and tools.

[[reactjs-roadmap|← Back to React.js Roadmap]]

## Navigation and Routing with React Router

**Problem:** How do we handle navigation in a single-page application without full page reloads?

**Theory:** React Router enables client-side routing, allowing navigation between different views without requesting new HTML from the server. This creates a smoother, more app-like user experience.

**When to use:**

-   For any multi-page React application
-   When you need bookmarkable URLs for different views
-   When you need to handle navigation history

### Basic Setup

```jsx
import { BrowserRouter, Routes, Route, Link } from "react-router-dom";

function App() {
	return (
		<BrowserRouter>
			<nav>
				<Link to="/">Home</Link>
				<Link to="/about">About</Link>
				<Link to="/dashboard">Dashboard</Link>
			</nav>

			<Routes>
				<Route path="/" element={<Home />} />
				<Route path="/about" element={<About />} />
				<Route path="/dashboard" element={<Dashboard />} />
			</Routes>
		</BrowserRouter>
	);
}
```

### Advanced Routing Patterns

**Problem:** How do we handle complex routing needs like nested layouts, dynamic routes, and protected routes?

**Theory:** React Router provides advanced features to handle complex routing scenarios common in real-world applications.

#### Nested Routes with Layouts

```jsx
function App() {
	return (
		<BrowserRouter>
			<Routes>
				<Route path="/" element={<Layout />}>
					<Route index element={<Home />} />
					<Route path="about" element={<About />} />
					<Route path="dashboard" element={<Dashboard />} />
				</Route>
			</Routes>
		</BrowserRouter>
	);
}

function Layout() {
	return (
		<div>
			<header>
				<nav>
					<Link to="/">Home</Link>
					<Link to="/about">About</Link>
					<Link to="/dashboard">Dashboard</Link>
				</nav>
			</header>

			<main>
				{/* This is where nested route components will render */}
				<Outlet />
			</main>

			<footer>© 2023 My App</footer>
		</div>
	);
}
```

#### Dynamic Routes

```jsx
function App() {
	return (
		<BrowserRouter>
			<Routes>
				<Route path="/" element={<ProductList />} />
				<Route
					path="/products/:productId"
					element={<ProductDetail />}
				/>
			</Routes>
		</BrowserRouter>
	);
}

function ProductDetail() {
	// Access the dynamic parameter
	const { productId } = useParams();
	const [product, setProduct] = useState(null);

	useEffect(() => {
		// Fetch product data based on the ID
		fetchProduct(productId).then((data) => setProduct(data));
	}, [productId]);

	if (!product) return <p>Loading...</p>;

	return (
		<div>
			<h1>{product.name}</h1>
			<p>{product.description}</p>
			<p>${product.price}</p>
		</div>
	);
}
```

#### Protected Routes

```jsx
function App() {
	return (
		<BrowserRouter>
			<Routes>
				<Route path="/" element={<Home />} />
				<Route path="/login" element={<Login />} />
				<Route
					path="/dashboard/*"
					element={
						<RequireAuth>
							<DashboardRoutes />
						</RequireAuth>
					}
				/>
			</Routes>
		</BrowserRouter>
	);
}

function RequireAuth({ children }) {
	const { user } = useAuth();
	const location = useLocation();

	if (!user) {
		// Redirect to login page but save the current location
		return <Navigate to="/login" state={{ from: location }} replace />;
	}

	return children;
}
```

## Advanced State Management

**Problem:** How do we manage global state that needs to be accessed by many components across the application?

**Theory:** While React's built-in state management (useState, useContext) works for simpler applications, larger apps often benefit from dedicated state management libraries.

### Redux Toolkit vs. Zustand

**Problem:** Which state management library should you choose for your React application?

**Theory:** Redux Toolkit and Zustand represent different philosophies in state management - structured vs. minimal.

#### Redux Toolkit

**When to use:**

-   For large, complex applications with many developers
-   When you need a strict, predictable state flow
-   When you need powerful debugging tools
-   When you need middleware for complex side effects

```jsx
// store.js
import { configureStore, createSlice } from "@reduxjs/toolkit";

const counterSlice = createSlice({
	name: "counter",
	initialState: { value: 0 },
	reducers: {
		increment: (state) => {
			state.value += 1;
		},
		decrement: (state) => {
			state.value -= 1;
		},
		incrementByAmount: (state, action) => {
			state.value += action.payload;
		},
	},
});

export const { increment, decrement, incrementByAmount } = counterSlice.actions;

export const store = configureStore({
	reducer: {
		counter: counterSlice.reducer,
	},
});

// App.jsx
import { Provider } from "react-redux";
import { store } from "./store";

function App() {
	return (
		<Provider store={store}>
			<Counter />
		</Provider>
	);
}

// Counter.jsx
import { useSelector, useDispatch } from "react-redux";
import { increment, decrement } from "./store";

function Counter() {
	const count = useSelector((state) => state.counter.value);
	const dispatch = useDispatch();

	return (
		<div>
			<h1>Count: {count}</h1>
			<button onClick={() => dispatch(increment())}>Increment</button>
			<button onClick={() => dispatch(decrement())}>Decrement</button>
		</div>
	);
}
```

#### Zustand

**When to use:**

-   For small to medium-sized applications
-   When you want minimal boilerplate
-   When you prefer a simpler mental model
-   For rapid development and prototyping

```jsx
// store.js
import create from "zustand";

const useBearStore = create((set) => ({
	count: 0,
	increment: () => set((state) => ({ count: state.count + 1 })),
	decrement: () => set((state) => ({ count: state.count - 1 })),
	incrementByAmount: (amount) =>
		set((state) => ({ count: state.count + amount })),
}));

// Counter.jsx
function Counter() {
	const { count, increment, decrement } = useBearStore();

	return (
		<div>
			<h1>Count: {count}</h1>
			<button onClick={increment}>Increment</button>
			<button onClick={decrement}>Decrement</button>
		</div>
	);
}
```

**Comparison:**

| Aspect             | Redux Toolkit                       | Zustand                       |
| ------------------ | ----------------------------------- | ----------------------------- |
| **Boilerplate**    | More (actions, reducers, selectors) | Minimal (direct store access) |
| **Learning Curve** | Steeper                             | Gentle                        |
| **Ecosystem**      | Extensive (middleware, dev tools)   | Smaller but growing           |
| **Performance**    | Good (with careful selectors)       | Excellent by default          |
| **Best For**       | Large teams, complex apps           | Smaller teams, simpler apps   |

## Styling Strategies

**Problem:** How do we style React components in a maintainable, scalable way?

**Theory:** React has no built-in styling solution, leading to multiple approaches each with different trade-offs.

### CSS Modules

**When to use:**

-   When you want scoped CSS without runtime overhead
-   When you prefer traditional CSS syntax
-   When you want good build tool integration

```jsx
// Button.module.css
.button {
  padding: 8px 16px;
  border-radius: 4px;
  font-weight: bold;
}

.primary {
  background-color: blue;
  color: white;
}

.secondary {
  background-color: gray;
  color: white;
}

// Button.jsx
import styles from './Button.module.css';

function Button({ children, variant = 'primary', ...props }) {
  return (
    <button
      className={`${styles.button} ${styles[variant]}`}
      {...props}
    >
      {children}
    </button>
  );
}
```

### CSS-in-JS (Styled Components)

**When to use:**

-   When you need dynamic styling based on props
-   When you want component-centric styling
-   When you prefer JavaScript for all styling logic

```jsx
import styled from "styled-components";

const StyledButton = styled.button`
	padding: 8px 16px;
	border-radius: 4px;
	font-weight: bold;
	background-color: ${(props) =>
		props.variant === "primary" ? "blue" : "gray"};
	color: white;

	&:hover {
		opacity: 0.8;
	}
`;

function Button({ children, variant = "primary", ...props }) {
	return (
		<StyledButton variant={variant} {...props}>
			{children}
		</StyledButton>
	);
}
```

### Utility-First CSS (Tailwind CSS)

**When to use:**

-   When you want rapid UI development
-   When you need consistent design constraints
-   When you want to avoid context switching between files

```jsx
// With Tailwind CSS
function Button({ children, variant = "primary", ...props }) {
	const baseClasses = "px-4 py-2 rounded font-bold";
	const variantClasses =
		variant === "primary"
			? "bg-blue-500 text-white hover:bg-blue-600"
			: "bg-gray-500 text-white hover:bg-gray-600";

	return (
		<button className={`${baseClasses} ${variantClasses}`} {...props}>
			{children}
		</button>
	);
}
```

**Comparison:**

| Aspect                   | CSS Modules        | CSS-in-JS                  | Utility-First CSS          |
| ------------------------ | ------------------ | -------------------------- | -------------------------- |
| **Scoping**              | File-scoped        | Component-scoped           | Global utilities           |
| **Performance**          | Excellent (static) | Good (some runtime cost)   | Excellent (static)         |
| **Dynamic Styling**      | Limited            | Excellent                  | Good with conditionals     |
| **Developer Experience** | Separate CSS files | Co-located with components | Inline styles              |
| **Bundle Size**          | Small              | Larger                     | Very small (with PurgeCSS) |
| **Best For**             | Traditional teams  | Component libraries        | Rapid development          |

## Data Fetching

**Problem:** How do we efficiently fetch and manage server data in React applications?

**Theory:** Modern React applications need robust data fetching solutions that handle caching, loading states, and error handling.

### React Query

**When to use:**

-   When you need smart caching and background updates
-   When you want automatic refetching
-   When you need pagination or infinite scrolling

```jsx
import { QueryClient, QueryClientProvider, useQuery } from "react-query";

const queryClient = new QueryClient();

function App() {
	return (
		<QueryClientProvider client={queryClient}>
			<UserList />
		</QueryClientProvider>
	);
}

function UserList() {
	const { data, isLoading, error } = useQuery("users", async () => {
		const response = await fetch("https://api.example.com/users");
		if (!response.ok) {
			throw new Error("Failed to fetch users");
		}
		return response.json();
	});

	if (isLoading) return <p>Loading...</p>;
	if (error) return <p>Error: {error.message}</p>;

	return (
		<ul>
			{data.map((user) => (
				<li key={user.id}>{user.name}</li>
			))}
		</ul>
	);
}
```

Understanding these ecosystem tools and libraries is crucial for building production-ready React applications. The right choices depend on your specific project requirements, team size, and development philosophy.
