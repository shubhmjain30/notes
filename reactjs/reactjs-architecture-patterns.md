# Architectural Excellence: Design Patterns & Advanced Concepts

As React applications grow, architectural patterns become essential for maintaining code quality, reusability, and developer productivity. This section explores proven patterns for structuring React applications.

[[reactjs-roadmap|← Back to React.js Roadmap]]

## Container/Presentational Pattern

**Problem:** How do we separate data fetching and state management from UI rendering to improve component reusability and testability?

**Theory:** This pattern divides components into two types:

-   **Container components**: Handle data fetching, state management, and business logic
-   **Presentational components**: Focus solely on rendering UI based on props

**When to use:**

-   When you want to reuse UI components with different data sources
-   When you need to simplify testing of UI components
-   When working in teams where UI and logic developers may be different people

```jsx
// Container Component
function UserProfileContainer({ userId }) {
	const [user, setUser] = useState(null);
	const [loading, setLoading] = useState(true);
	const [error, setError] = useState(null);

	useEffect(() => {
		async function fetchUser() {
			try {
				setLoading(true);
				const data = await fetchUserById(userId);
				setUser(data);
			} catch (err) {
				setError(err.message);
			} finally {
				setLoading(false);
			}
		}

		fetchUser();
	}, [userId]);

	// Pass all data and state as props to the presentational component
	return <UserProfile user={user} loading={loading} error={error} />;
}

// Presentational Component
function UserProfile({ user, loading, error }) {
	if (loading) return <p>Loading...</p>;
	if (error) return <p>Error: {error}</p>;
	if (!user) return null;

	return (
		<div className="user-profile">
			<h1>{user.name}</h1>
			<p>{user.email}</p>
			<img src={user.avatar} alt={`${user.name}'s avatar`} />
		</div>
	);
}
```

## Compound Components

**Problem:** How do we create flexible component APIs that allow consumers to control the rendering and behavior of complex components?

**Theory:** Compound components use React's children prop and context to create a group of components that work together while giving consumers control over composition.

**When to use:**

-   For complex components with multiple related parts (tabs, accordions, menus)
-   When you want to give consumers flexibility in how they compose the component
-   When the internal state should be shared between sub-components

```jsx
// Simple implementation with React.Children
function Tabs({ children, defaultIndex = 0 }) {
	const [activeIndex, setActiveIndex] = useState(defaultIndex);

	// Clone and enhance children with additional props
	const enhancedChildren = React.Children.map(children, (child, index) => {
		return React.cloneElement(child, {
			isActive: index === activeIndex,
			onActivate: () => setActiveIndex(index),
		});
	});

	return <div className="tabs">{enhancedChildren}</div>;
}

function Tab({ isActive, onActivate, children, label }) {
	return (
		<div className="tab">
			<button className={isActive ? "active" : ""} onClick={onActivate}>
				{label}
			</button>
			{isActive && <div className="tab-content">{children}</div>}
		</div>
	);
}

// Usage
function App() {
	return (
		<Tabs>
			<Tab label="Profile">User profile content</Tab>
			<Tab label="Settings">Settings content</Tab>
			<Tab label="Notifications">Notifications content</Tab>
		</Tabs>
	);
}

// More advanced implementation with Context
const TabsContext = createContext();

function Tabs({ children, defaultIndex = 0 }) {
	const [activeIndex, setActiveIndex] = useState(defaultIndex);

	const contextValue = {
		activeIndex,
		setActiveIndex,
	};

	return (
		<TabsContext.Provider value={contextValue}>
			<div className="tabs">{children}</div>
		</TabsContext.Provider>
	);
}

function TabList({ children }) {
	return <div className="tab-list">{children}</div>;
}

function TabPanel({ children, index }) {
	const { activeIndex } = useContext(TabsContext);

	if (index !== activeIndex) return null;

	return <div className="tab-panel">{children}</div>;
}

function TabButton({ children, index }) {
	const { activeIndex, setActiveIndex } = useContext(TabsContext);

	return (
		<button
			className={index === activeIndex ? "active" : ""}
			onClick={() => setActiveIndex(index)}
		>
			{children}
		</button>
	);
}

// Expose components as properties
Tabs.List = TabList;
Tabs.Panel = TabPanel;
Tabs.Button = TabButton;

// Usage
function App() {
	return (
		<Tabs>
			<Tabs.List>
				<Tabs.Button index={0}>Profile</Tabs.Button>
				<Tabs.Button index={1}>Settings</Tabs.Button>
				<Tabs.Button index={2}>Notifications</Tabs.Button>
			</Tabs.List>

			<Tabs.Panel index={0}>User profile content</Tabs.Panel>
			<Tabs.Panel index={1}>Settings content</Tabs.Panel>
			<Tabs.Panel index={2}>Notifications content</Tabs.Panel>
		</Tabs>
	);
}
```

## Provider Pattern

**Problem:** How do we share state and functionality across many components without prop drilling?

**Theory:** The Provider Pattern uses React Context to make data and functions available to any component in the tree without passing props through intermediate components.

**When to use:**

-   For application-wide state (themes, user authentication)
-   For deeply nested component trees
-   When many components need the same data

```jsx
// Create context with a default value
const ThemeContext = createContext({
	theme: "light",
	toggleTheme: () => {},
});

// Provider component
function ThemeProvider({ children }) {
	const [theme, setTheme] = useState("light");

	const toggleTheme = () => {
		setTheme((prevTheme) => (prevTheme === "light" ? "dark" : "light"));
	};

	// Value that will be provided to consumers
	const value = {
		theme,
		toggleTheme,
	};

	return (
		<ThemeContext.Provider value={value}>{children}</ThemeContext.Provider>
	);
}

// Custom hook for consuming the context
function useTheme() {
	const context = useContext(ThemeContext);

	if (context === undefined) {
		throw new Error("useTheme must be used within a ThemeProvider");
	}

	return context;
}

// Usage in App
function App() {
	return (
		<ThemeProvider>
			<Header />
			<MainContent />
			<Footer />
		</ThemeProvider>
	);
}

// Usage in a deeply nested component
function ThemedButton() {
	const { theme, toggleTheme } = useTheme();

	return (
		<button className={`btn-${theme}`} onClick={toggleTheme}>
			Toggle Theme
		</button>
	);
}
```

## Logic Reuse: HOCs, Render Props, Custom Hooks

**Problem:** How do we share and reuse stateful logic between components?

**Theory:** React offers several patterns for sharing logic between components, each with different trade-offs.

### Higher-Order Components (HOCs)

**When to use:**

-   When you need to enhance multiple components with the same functionality
-   When the shared logic doesn't need to be customized much by the consumer

```jsx
// Higher-Order Component
function withLoading(WrappedComponent) {
	return function WithLoading(props) {
		const [loading, setLoading] = useState(true);
		const [data, setData] = useState(null);
		const [error, setError] = useState(null);

		useEffect(() => {
			async function fetchData() {
				try {
					setLoading(true);
					const result = await fetch(props.url);
					const json = await result.json();
					setData(json);
				} catch (err) {
					setError(err.message);
				} finally {
					setLoading(false);
				}
			}

			fetchData();
		}, [props.url]);

		// Pass loading state and data to wrapped component
		return (
			<WrappedComponent
				loading={loading}
				data={data}
				error={error}
				{...props}
			/>
		);
	};
}

// Usage
function UserList({ data, loading, error }) {
	if (loading) return <p>Loading...</p>;
	if (error) return <p>Error: {error}</p>;

	return (
		<ul>
			{data.map((user) => (
				<li key={user.id}>{user.name}</li>
			))}
		</ul>
	);
}

const UserListWithLoading = withLoading(UserList);

// In another component
function App() {
	return <UserListWithLoading url="/api/users" />;
}
```

### Render Props

**When to use:**

-   When consumers need more control over what is rendered
-   When the shared logic needs to interact closely with the rendered UI

```jsx
// Render Props Component
function DataFetcher({ url, render }) {
	const [loading, setLoading] = useState(true);
	const [data, setData] = useState(null);
	const [error, setError] = useState(null);

	useEffect(() => {
		async function fetchData() {
			try {
				setLoading(true);
				const result = await fetch(url);
				const json = await result.json();
				setData(json);
			} catch (err) {
				setError(err.message);
			} finally {
				setLoading(false);
			}
		}

		fetchData();
	}, [url]);

	// Call the render prop with the current state
	return render({ loading, data, error });
}

// Usage
function App() {
	return (
		<DataFetcher
			url="/api/users"
			render={({ loading, data, error }) => {
				if (loading) return <p>Loading...</p>;
				if (error) return <p>Error: {error}</p>;

				return (
					<ul>
						{data.map((user) => (
							<li key={user.id}>{user.name}</li>
						))}
					</ul>
				);
			}}
		/>
	);
}

// Alternative children as function syntax
function App() {
	return (
		<DataFetcher url="/api/users">
			{({ loading, data, error }) => {
				if (loading) return <p>Loading...</p>;
				if (error) return <p>Error: {error}</p>;

				return (
					<ul>
						{data.map((user) => (
							<li key={user.id}>{user.name}</li>
						))}
					</ul>
				);
			}}
		</DataFetcher>
	);
}
```

### Custom Hooks

**When to use:**

-   For sharing logic between functional components
-   When you want the cleanest, most idiomatic modern React code
-   When the shared logic is primarily about state management and side effects

```jsx
// Custom Hook
function useDataFetching(url) {
	const [loading, setLoading] = useState(true);
	const [data, setData] = useState(null);
	const [error, setError] = useState(null);

	useEffect(() => {
		let isMounted = true;

		async function fetchData() {
			try {
				setLoading(true);
				const result = await fetch(url);
				const json = await result.json();

				if (isMounted) {
					setData(json);
				}
			} catch (err) {
				if (isMounted) {
					setError(err.message);
				}
			} finally {
				if (isMounted) {
					setLoading(false);
				}
			}
		}

		fetchData();

		// Cleanup function to prevent state updates on unmounted components
		return () => {
			isMounted = false;
		};
	}, [url]);

	return { loading, data, error };
}

// Usage
function UserList({ url }) {
	const { loading, data, error } = useDataFetching(url);

	if (loading) return <p>Loading...</p>;
	if (error) return <p>Error: {error}</p>;

	return (
		<ul>
			{data.map((user) => (
				<li key={user.id}>{user.name}</li>
			))}
		</ul>
	);
}

// In another component
function App() {
	return <UserList url="/api/users" />;
}
```

**Comparison:**

| Pattern          | Pros                                   | Cons                                | Best For                  |
| ---------------- | -------------------------------------- | ----------------------------------- | ------------------------- |
| **HOCs**         | Established pattern                    | Wrapper hell, prop collision        | Legacy codebases          |
| **Render Props** | Flexible, clear data flow              | Callback hell, JSX indentation      | Complex rendering logic   |
| **Custom Hooks** | Clean, composable, no extra components | Only works with function components | Modern React applications |

## Thinking in React

**Problem:** How do we approach building React applications in a systematic, maintainable way?

**Theory:** "Thinking in React" is a methodology for breaking down UI into a component hierarchy and determining where state should live.

**The process:**

1. **Break the UI into a component hierarchy**

    - Draw boxes around every component and subcomponent
    - Apply the single responsibility principle
    - Identify reusable patterns

2. **Build a static version first**

    - Implement the UI with no interactivity
    - Use props, not state
    - Build top-down or bottom-up

3. **Identify the minimal state needed**

    - Is it passed from a parent via props? Not state.
    - Does it remain unchanged over time? Not state.
    - Can you compute it based on existing state or props? Not state.

4. **Identify where state should live**

    - Find the common owner component for components that need the state
    - Create a new component solely for state management if needed

5. **Add inverse data flow**
    - Pass callbacks from parent to children to update state

## Integrating with TypeScript

**Problem:** How do we add type safety to React applications to catch errors early and improve developer experience?

**Theory:** TypeScript adds static typing to JavaScript, helping catch errors during development rather than at runtime.

**When to use:**

-   For medium to large applications
-   When working in teams
-   When you want better tooling and autocompletion

```tsx
// Typing props
interface UserProps {
	name: string;
	email: string;
	age?: number; // Optional prop
	onUpdate: (id: number, newName: string) => void;
}

// Function component with TypeScript
const UserProfile: React.FC<UserProps> = ({ name, email, age, onUpdate }) => {
	return (
		<div>
			<h2>{name}</h2>
			<p>{email}</p>
			{age && <p>Age: {age}</p>}
			<button onClick={() => onUpdate(1, "New Name")}>Update</button>
		</div>
	);
};

// Typing hooks
interface User {
	id: number;
	name: string;
	email: string;
}

function useUser(userId: number): {
	user: User | null;
	loading: boolean;
	error: Error | null;
} {
	const [user, setUser] = useState<User | null>(null);
	const [loading, setLoading] = useState<boolean>(true);
	const [error, setError] = useState<Error | null>(null);

	useEffect(() => {
		async function fetchUser() {
			try {
				const response = await fetch(`/api/users/${userId}`);
				const data: User = await response.json();
				setUser(data);
			} catch (err) {
				setError(
					err instanceof Error ? err : new Error("Unknown error")
				);
			} finally {
				setLoading(false);
			}
		}

		fetchUser();
	}, [userId]);

	return { user, loading, error };
}

// Usage
function App() {
	const { user, loading, error } = useUser(1);

	if (loading) return <p>Loading...</p>;
	if (error) return <p>Error: {error.message}</p>;
	if (!user) return null;

	return (
		<UserProfile
			name={user.name}
			email={user.email}
			onUpdate={(id, newName) => console.log(id, newName)}
		/>
	);
}
```

These architectural patterns and concepts form the foundation for building scalable, maintainable React applications. By understanding when and how to apply each pattern, you can create more robust code that's easier to test, maintain, and extend.
