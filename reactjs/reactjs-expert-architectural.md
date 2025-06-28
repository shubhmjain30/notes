# 🏗 Stage 4: Expert & Architectural (Pro)

[[README|← Back to React.js Interview Prep Checklist]]

Master advanced React internals, architectural patterns, and lead-level engineering practices. This stage prepares you for technical leadership and complex system design.

---

## ✅ Checklist: Expert & Architectural

-   [ ] **React Internals** – reconciliation, Fiber, scheduling, lanes, batching
-   [ ] **Concurrent Rendering Deep Dive** – time-slicing, transition tracing, offscreen API
-   [ ] **React Server Components (RSC)** – client/server boundaries, streaming, cache
-   [ ] **Edge Rendering & CDN Strategy** – Vercel edge functions, Cloudflare Pages
-   [ ] **Micro-frontends & Module Federation** – splitting teams & deployments
-   [ ] **Monorepos & Tooling** – Nx, Turborepo, pnpm workspaces, CI pipelines
-   [ ] **Design Patterns** – render props, HOCs vs. hooks, compound components
-   [ ] **Advanced TypeScript** – discriminated unions, mapped & conditional types in components
-   [ ] **Security Hardening** – XSS in JSX, content-security-policy, secrets management
-   [ ] **Performance Budgets & Monitoring** – Core Web Vitals, RUM, Sentry/Datadog tracing
-   [ ] **Testing for Reliability** – contract tests, visual regression, test flakiness mitigation
-   [ ] **Migration Strategies** – class-to-hooks, legacy React <=16.x, large-scale refactors
-   [ ] **Team Leadership Skills** – code reviews, mentoring, architecting shared libraries
-   [ ] **Cross-Platform React** – React Native, Expo, React VR/360, Electron integration
-   [ ] **Future Roadmap Awareness** – offscreen API, React Compiler, incremental adoption plans

---

## React Internals

### Reconciliation & Fiber Architecture

**Problem:** How does React efficiently update the DOM and maintain responsive UIs during complex state changes?

**Theory:** React uses a "Fiber" architecture that enables incremental rendering, allowing React to pause, resume, or abort work to maintain responsiveness.

```jsx
// Understanding Fiber nodes and reconciliation
function FiberDemo() {
  const [count, setCount] = useState(0);
  const [items, setItems] = useState([]);

  // This component will create multiple Fiber nodes
  return (
    <div> {/* Fiber node: HostComponent */}
      <h1>Count: {count}</h1> {/* Fiber node: HostComponent */}
      <button onClick={() => setCount(c => c + 1)}>
        Increment {/* Fiber node: HostText */}
      </button>

      {/* Each item creates a Fiber node */}
      {items.map(item => (
        <ItemComponent key={item.id} item={item} /> {/* Fiber node: FunctionComponent */}
      ))}

      {/* Conditional rendering affects Fiber tree structure */}
      {count > 5 && <ExpensiveComponent />}
    </div>
  );
}

// Component that demonstrates reconciliation keys
function ReconciliationExample() {
  const [users, setUsers] = useState([
    { id: 1, name: 'Alice', active: true },
    { id: 2, name: 'Bob', active: false },
    { id: 3, name: 'Charlie', active: true }
  ]);

  const shuffleUsers = () => {
    setUsers(prev => [...prev].sort(() => Math.random() - 0.5));
  };

  const toggleUser = (id) => {
    setUsers(prev => prev.map(user =>
      user.id === id ? { ...user, active: !user.active } : user
    ));
  };

  return (
    <div>
      <button onClick={shuffleUsers}>Shuffle Users</button>

      {/* With proper keys, React can efficiently reconcile */}
      {users.map(user => (
        <UserCard
          key={user.id} // Stable key enables efficient reconciliation
          user={user}
          onToggle={() => toggleUser(user.id)}
        />
      ))}

      {/* Example of what NOT to do */}
      {/*
      {users.map((user, index) => (
        <UserCard
          key={index} // ❌ Index as key causes unnecessary re-renders
          user={user}
          onToggle={() => toggleUser(user.id)}
        />
      ))}
      */}
    </div>
  );
}
```

### Lanes and Priority Scheduling

**Problem:** How does React prioritize different types of updates to maintain responsiveness?

```jsx
// React 18 scheduling priorities (conceptual understanding)
/*
Lanes in React 18:
- SyncLane: Synchronous updates (user input, clicks)
- InputContinuousLane: Continuous input (mouseover, scroll)
- DefaultLane: Normal updates (useEffect, setState)
- TransitionLane: Transition updates (useTransition)
- RetryLane: Error boundary retries
- OffscreenLane: Offscreen components
*/

function PriorityDemo() {
	const [urgent, setUrgent] = useState("");
	const [nonUrgent, setNonUrgent] = useState("");
	const [isPending, startTransition] = useTransition();

	const handleUrgentChange = (e) => {
		// High priority - synchronous update
		setUrgent(e.target.value);
	};

	const handleNonUrgentChange = (e) => {
		const value = e.target.value;

		// High priority - immediate UI feedback
		setUrgent(value);

		// Low priority - can be interrupted
		startTransition(() => {
			setNonUrgent(value);
			// Expensive operations here won't block urgent updates
			performExpensiveCalculation(value);
		});
	};

	return (
		<div>
			<input
				value={urgent}
				onChange={handleUrgentChange}
				placeholder="High priority input"
			/>

			<input
				onChange={handleNonUrgentChange}
				placeholder="Input with transition"
			/>

			{isPending && <div>Updating...</div>}

			{/* This will update with lower priority */}
			<ExpensiveList query={nonUrgent} />
		</div>
	);
}

// Custom hook to understand batching
function useBatchingDemo() {
	const [count, setCount] = useState(0);
	const [flag, setFlag] = useState(false);

	const handleClick = () => {
		console.log("Before updates:", { count, flag });

		// In React 18, these are automatically batched
		setCount((c) => c + 1);
		setFlag((f) => !f);

		// Only one re-render will occur
		console.log("After updates:", { count, flag }); // Will show old values
	};

	const handleAsyncClick = async () => {
		// In React 18, even async updates are batched
		await fetch("/api/data");

		setCount((c) => c + 1); // Batched
		setFlag((f) => !f); // Batched with above
	};

	const handleFlushSyncClick = () => {
		// Force synchronous update (escape batching)
		flushSync(() => {
			setCount((c) => c + 1);
		});

		// This will be in a separate batch
		setFlag((f) => !f);
	};

	return {
		count,
		flag,
		handleClick,
		handleAsyncClick,
		handleFlushSyncClick,
	};
}
```

### Virtual DOM and Diffing Algorithm

```jsx
// Understanding the diffing algorithm
function DiffingExample() {
	const [showOptimized, setShowOptimized] = useState(true);

	// Example 1: Element type changes (expensive)
	const renderDifferentTypes = () =>
		showOptimized ? <div>Content</div> : <span>Content</span>; // Different type = full re-mount

	// Example 2: Same type, different props (efficient)
	const renderSameType = () => (
		<div className={showOptimized ? "optimized" : "normal"}>
			Content {/* Only className changes, content preserved */}
		</div>
	);

	// Example 3: List reconciliation with keys
	const items = ["apple", "banana", "cherry"];

	return (
		<div>
			<button onClick={() => setShowOptimized(!showOptimized)}>
				Toggle
			</button>

			{/* Efficient list rendering */}
			<ul>
				{items.map((item) => (
					<li key={item}>
						{" "}
						{/* Stable key */}
						{item}
					</li>
				))}
			</ul>

			{/* Inefficient - will re-render all items when order changes */}
			<ul>
				{items.map((item, index) => (
					<li key={index}>
						{" "}
						{/* Index as key */}
						{item}
					</li>
				))}
			</ul>
		</div>
	);
}

// Understanding component lifecycle in Fiber
function FiberLifecycleDemo() {
	const [mounted, setMounted] = useState(true);

	return (
		<div>
			<button onClick={() => setMounted(!mounted)}>
				{mounted ? "Unmount" : "Mount"} Component
			</button>

			{mounted && <LifecycleComponent />}
		</div>
	);
}

function LifecycleComponent() {
	const [count, setCount] = useState(0);

	useEffect(() => {
		console.log("Effect: Component mounted");

		return () => {
			console.log("Effect: Component will unmount");
		};
	}, []);

	useEffect(() => {
		console.log("Effect: Count changed to", count);
	}, [count]);

	console.log("Render: Component rendering with count", count);

	return (
		<div>
			<p>Count: {count}</p>
			<button onClick={() => setCount((c) => c + 1)}>Increment</button>
		</div>
	);
}
```

---

## Concurrent Rendering Deep Dive

### Time-Slicing and Interruptible Rendering

**Problem:** How do we maintain 60fps while rendering large component trees?

```jsx
// Demonstrating time-slicing benefits
function TimeSlicingDemo() {
	const [count, setCount] = useState(0);
	const [items, setItems] = useState(generateItems(1000));
	const [isPending, startTransition] = useTransition();

	const handleExpensiveUpdate = () => {
		startTransition(() => {
			// This expensive operation can be interrupted
			setItems(generateItems(5000));
		});
	};

	const handleUrgentUpdate = () => {
		// This will interrupt any pending transition
		setCount((c) => c + 1);
	};

	return (
		<div>
			<div>
				<button onClick={handleUrgentUpdate}>
					Urgent Update (Count: {count})
				</button>
				<button onClick={handleExpensiveUpdate}>
					Expensive Update {isPending && "(Pending...)"}
				</button>
			</div>

			<div className={isPending ? "updating" : ""}>
				<ExpensiveList items={items} />
			</div>
		</div>
	);
}

// Component that benefits from time-slicing
function ExpensiveList({ items }) {
	return (
		<div>
			{items.map((item) => (
				<ExpensiveItem key={item.id} item={item} />
			))}
		</div>
	);
}

function ExpensiveItem({ item }) {
	// Simulate expensive rendering
	const start = performance.now();
	while (performance.now() - start < 1) {
		// Burn 1ms per item
	}

	return (
		<div className="expensive-item">
			{item.name} - {item.value}
		</div>
	);
}
```

### Transition Tracing and Performance Monitoring

```jsx
// Advanced performance monitoring with React 18
function PerformanceMonitoringDemo() {
	const [data, setData] = useState([]);
	const [isPending, startTransition] = useTransition();

	const handleDataUpdate = () => {
		// Trace this transition for performance monitoring
		startTransition(() => {
			// Mark the start of expensive operation
			performance.mark("expensive-update-start");

			setData(generateLargeDataset());

			// This will be called after the transition completes
			setTimeout(() => {
				performance.mark("expensive-update-end");
				performance.measure(
					"expensive-update",
					"expensive-update-start",
					"expensive-update-end"
				);

				const measure =
					performance.getEntriesByName("expensive-update")[0];
				console.log(`Transition took ${measure.duration}ms`);
			}, 0);
		});
	};

	// Monitor concurrent features
	useEffect(() => {
		if ("PerformanceObserver" in window) {
			const observer = new PerformanceObserver((list) => {
				for (const entry of list.getEntries()) {
					if (entry.name === "expensive-update") {
						// Send to analytics
						console.log("Transition performance:", entry);
					}
				}
			});

			observer.observe({ entryTypes: ["measure"] });

			return () => observer.disconnect();
		}
	}, []);

	return (
		<div>
			<button onClick={handleDataUpdate}>
				Update Data {isPending && "(Processing...)"}
			</button>

			<DataVisualization data={data} />
		</div>
	);
}
```

### Offscreen API and Background Rendering

```jsx
// Future React feature - Offscreen API (experimental)
function OffscreenDemo() {
	const [activeTab, setActiveTab] = useState("tab1");
	const [showTab2, setShowTab2] = useState(false);

	return (
		<div>
			<div className="tabs">
				<button
					onClick={() => setActiveTab("tab1")}
					className={activeTab === "tab1" ? "active" : ""}
				>
					Tab 1
				</button>
				<button
					onClick={() => setActiveTab("tab2")}
					className={activeTab === "tab2" ? "active" : ""}
				>
					Tab 2
				</button>
			</div>

			<div className="tab-content">
				{activeTab === "tab1" && <Tab1Content />}

				{/* Future: Offscreen component continues to render in background */}
				{/*
        <Offscreen mode={activeTab === 'tab2' ? 'visible' : 'hidden'}>
          <Tab2Content />
        </Offscreen>
        */}

				{activeTab === "tab2" && <Tab2Content />}
			</div>
		</div>
	);
}
```

---

## React Server Components (RSC)

### Client/Server Boundaries

**Problem:** How do we optimize data fetching and reduce bundle size with server-side rendering?

```jsx
// Server Component (runs on server)
// app/users/page.js
async function UsersPage() {
	// This runs on the server
	const users = await fetchUsers();

	return (
		<div>
			<h1>Users</h1>
			<UsersList users={users} />

			{/* Client component for interactivity */}
			<UserSearch />
		</div>
	);
}

// Client Component (runs on client)
("use client");

import { useState } from "react";

function UserSearch() {
	const [query, setQuery] = useState("");
	const [results, setResults] = useState([]);

	const handleSearch = async (searchQuery) => {
		// This runs on the client
		const response = await fetch(`/api/users/search?q=${searchQuery}`);
		const data = await response.json();
		setResults(data);
	};

	return (
		<div>
			<input
				value={query}
				onChange={(e) => setQuery(e.target.value)}
				placeholder="Search users..."
			/>
			<button onClick={() => handleSearch(query)}>Search</button>

			<SearchResults results={results} />
		</div>
	);
}

// Shared Component (can be server or client)
function UserCard({ user }) {
	return (
		<div className="user-card">
			<h3>{user.name}</h3>
			<p>{user.email}</p>
		</div>
	);
}
```

### Streaming and Progressive Hydration

```jsx
// app/dashboard/page.js - Server Component with Suspense
import { Suspense } from "react";

async function DashboardPage() {
	return (
		<div>
			<h1>Dashboard</h1>

			{/* Immediate content */}
			<QuickStats />

			{/* Streamed content */}
			<Suspense fallback={<ChartSkeleton />}>
				<RevenueChart />
			</Suspense>

			<Suspense fallback={<TableSkeleton />}>
				<RecentOrders />
			</Suspense>
		</div>
	);
}

// Server Component that fetches data
async function RevenueChart() {
	// This can take time, but won't block the page
	const chartData = await fetchRevenueData();

	return (
		<div className="chart-container">
			<ChartComponent data={chartData} />
		</div>
	);
}

// Server Component with caching
async function RecentOrders() {
	// RSC can use caching strategies
	const orders = await fetchRecentOrders();

	return (
		<div>
			<h2>Recent Orders</h2>
			{orders.map((order) => (
				<OrderCard key={order.id} order={order} />
			))}
		</div>
	);
}

// app/layout.js - Root layout with streaming
export default function RootLayout({ children }) {
	return (
		<html>
			<body>
				<header>
					<Navigation />
				</header>

				<main>
					<Suspense fallback={<PageSkeleton />}>{children}</Suspense>
				</main>

				<footer>
					<Footer />
				</footer>
			</body>
		</html>
	);
}
```

### Server Actions and Data Mutations

```jsx
// app/actions.js - Server Actions
"use server";

export async function createUser(formData) {
	const userData = {
		name: formData.get("name"),
		email: formData.get("email"),
	};

	// Validate data
	if (!userData.name || !userData.email) {
		return { error: "Name and email are required" };
	}

	// Save to database
	const user = await saveUser(userData);

	// Revalidate cache
	revalidatePath("/users");

	return { success: true, user };
}

export async function updateUserStatus(userId, status) {
	await updateUser(userId, { status });
	revalidatePath("/users");
	return { success: true };
}

// app/users/new/page.js - Form using Server Actions
import { createUser } from "../actions";

function NewUserPage() {
	return (
		<form action={createUser}>
			<input name="name" placeholder="Name" required />
			<input name="email" type="email" placeholder="Email" required />
			<button type="submit">Create User</button>
		</form>
	);
}

// Client Component using Server Actions
("use client");

import { updateUserStatus } from "./actions";
import { useTransition } from "react";

function UserStatusToggle({ user }) {
	const [isPending, startTransition] = useTransition();

	const handleStatusChange = () => {
		startTransition(async () => {
			await updateUserStatus(
				user.id,
				user.active ? "inactive" : "active"
			);
		});
	};

	return (
		<button onClick={handleStatusChange} disabled={isPending}>
			{isPending
				? "Updating..."
				: `Mark ${user.active ? "Inactive" : "Active"}`}
		</button>
	);
}
```

---

## Micro-frontends & Module Federation

### Webpack Module Federation Setup

**Problem:** How do we scale large applications across multiple teams while maintaining independence?

```javascript
// Host Application - webpack.config.js
const ModuleFederationPlugin = require("@module-federation/webpack");

module.exports = {
	mode: "development",
	devServer: {
		port: 3000,
	},
	plugins: [
		new ModuleFederationPlugin({
			name: "host_app",
			remotes: {
				userMicroFrontend:
					"user_mf@http://localhost:3001/remoteEntry.js",
				productMicroFrontend:
					"product_mf@http://localhost:3002/remoteEntry.js",
				sharedComponents: "shared@http://localhost:3003/remoteEntry.js",
			},
			shared: {
				react: { singleton: true },
				"react-dom": { singleton: true },
				"@emotion/react": { singleton: true },
			},
		}),
	],
};

// Remote Application - webpack.config.js (User Microfrontend)
module.exports = {
	mode: "development",
	devServer: {
		port: 3001,
	},
	plugins: [
		new ModuleFederationPlugin({
			name: "user_mf",
			filename: "remoteEntry.js",
			exposes: {
				"./UserApp": "./src/UserApp",
				"./UserProfile": "./src/components/UserProfile",
				"./UserList": "./src/components/UserList",
			},
			shared: {
				react: { singleton: true },
				"react-dom": { singleton: true },
			},
		}),
	],
};
```

### Micro-frontend Communication Patterns

```jsx
// Host Application - App.js
import React, { Suspense, useState } from "react";

// Dynamic imports for micro-frontends
const UserApp = React.lazy(() => import("userMicroFrontend/UserApp"));
const ProductApp = React.lazy(() => import("productMicroFrontend/ProductApp"));

// Event bus for micro-frontend communication
class EventBus {
	constructor() {
		this.events = {};
	}

	on(event, callback) {
		if (!this.events[event]) {
			this.events[event] = [];
		}
		this.events[event].push(callback);
	}

	emit(event, data) {
		if (this.events[event]) {
			this.events[event].forEach((callback) => callback(data));
		}
	}

	off(event, callback) {
		if (this.events[event]) {
			this.events[event] = this.events[event].filter(
				(cb) => cb !== callback
			);
		}
	}
}

const eventBus = new EventBus();

function HostApp() {
	const [selectedUser, setSelectedUser] = useState(null);
	const [activeTab, setActiveTab] = useState("users");

	// Listen for events from micro-frontends
	useEffect(() => {
		const handleUserSelected = (user) => {
			setSelectedUser(user);
			setActiveTab("products");
		};

		eventBus.on("user:selected", handleUserSelected);

		return () => {
			eventBus.off("user:selected", handleUserSelected);
		};
	}, []);

	return (
		<div className="app">
			<nav>
				<button
					onClick={() => setActiveTab("users")}
					className={activeTab === "users" ? "active" : ""}
				>
					Users
				</button>
				<button
					onClick={() => setActiveTab("products")}
					className={activeTab === "products" ? "active" : ""}
				>
					Products
				</button>
			</nav>

			<main>
				<Suspense fallback={<div>Loading...</div>}>
					{activeTab === "users" && (
						<UserApp
							eventBus={eventBus}
							onUserSelect={(user) => setSelectedUser(user)}
						/>
					)}

					{activeTab === "products" && (
						<ProductApp
							eventBus={eventBus}
							selectedUser={selectedUser}
						/>
					)}
				</Suspense>
			</main>
		</div>
	);
}
```

### Shared State Management Across Micro-frontends

```jsx
// Shared state store that works across micro-frontends
class SharedStore {
	constructor() {
		this.state = {
			user: null,
			theme: "light",
			notifications: [],
		};
		this.subscribers = new Set();
	}

	getState() {
		return this.state;
	}

	setState(updates) {
		this.state = { ...this.state, ...updates };
		this.notify();
	}

	subscribe(callback) {
		this.subscribers.add(callback);
		return () => this.subscribers.delete(callback);
	}

	notify() {
		this.subscribers.forEach((callback) => callback(this.state));
	}
}

// Create shared store instance
const sharedStore = new SharedStore();

// Custom hook for using shared store
function useSharedStore() {
	const [state, setState] = useState(sharedStore.getState());

	useEffect(() => {
		return sharedStore.subscribe(setState);
	}, []);

	const updateStore = (updates) => {
		sharedStore.setState(updates);
	};

	return [state, updateStore];
}

// User Micro-frontend using shared store
function UserApp({ eventBus }) {
	const [sharedState, updateSharedState] = useSharedStore();
	const [users, setUsers] = useState([]);

	const handleUserLogin = (user) => {
		updateSharedState({ user });
		eventBus.emit("user:login", user);
	};

	return (
		<div>
			<h2>User Management</h2>
			{sharedState.user && <div>Welcome, {sharedState.user.name}</div>}
			<UserList
				users={users}
				onUserSelect={(user) => eventBus.emit("user:selected", user)}
			/>
		</div>
	);
}

// Product Micro-frontend using shared store
function ProductApp({ selectedUser }) {
	const [sharedState] = useSharedStore();

	return (
		<div>
			<h2>Products</h2>
			{selectedUser && (
				<div>Showing products for: {selectedUser.name}</div>
			)}
			{sharedState.user && (
				<div>Logged in as: {sharedState.user.name}</div>
			)}
			<ProductList userId={selectedUser?.id} />
		</div>
	);
}
```

---

## Advanced TypeScript Patterns

### Discriminated Unions for Component Props

**Problem:** How do we create type-safe component APIs that enforce correct prop combinations?

```tsx
// Discriminated union for different button variants
type ButtonProps =
	| {
			variant: "primary";
			size: "small" | "medium" | "large";
			onClick: () => void;
			children: React.ReactNode;
	  }
	| {
			variant: "link";
			href: string;
			target?: "_blank" | "_self";
			children: React.ReactNode;
	  }
	| {
			variant: "icon";
			icon: React.ComponentType;
			"aria-label": string;
			onClick: () => void;
	  };

function Button(props: ButtonProps) {
	if (props.variant === "primary") {
		return (
			<button
				className={`btn btn-primary btn-${props.size}`}
				onClick={props.onClick}
			>
				{props.children}
			</button>
		);
	}

	if (props.variant === "link") {
		return (
			<a href={props.href} target={props.target} className="btn btn-link">
				{props.children}
			</a>
		);
	}

	if (props.variant === "icon") {
		const Icon = props.icon;
		return (
			<button
				className="btn btn-icon"
				onClick={props.onClick}
				aria-label={props["aria-label"]}
			>
				<Icon />
			</button>
		);
	}
}

// Usage with full type safety
function Example() {
	return (
		<div>
			{/* ✅ Valid - all required props provided */}
			<Button variant="primary" size="large" onClick={() => {}}>
				Click me
			</Button>

			{/* ✅ Valid - correct props for link variant */}
			<Button variant="link" href="/about">
				About
			</Button>

			{/* ✅ Valid - icon variant with aria-label */}
			<Button
				variant="icon"
				icon={CloseIcon}
				aria-label="Close dialog"
				onClick={() => {}}
			/>

			{/* ❌ TypeScript error - missing href for link variant */}
			{/* <Button variant="link">Invalid</Button> */}

			{/* ❌ TypeScript error - size not valid for icon variant */}
			{/* <Button variant="icon" size="large" icon={CloseIcon} /> */}
		</div>
	);
}
```

### Mapped Types and Conditional Types

```tsx
// Advanced form handling with TypeScript
type FormSchema = {
	email: string;
	password: string;
	confirmPassword: string;
	age: number;
	terms: boolean;
};

// Mapped type to create validation rules
type ValidationRules<T> = {
	[K in keyof T]: Array<(value: T[K], allValues: T) => string | undefined>;
};

// Conditional type for form field components
type FieldComponent<T> = T extends string
	? "input" | "textarea" | "select"
	: T extends number
	? "input"
	: T extends boolean
	? "checkbox"
	: never;

// Generic form hook with full type safety
function useTypedForm<T extends Record<string, any>>(
	initialValues: T,
	validationRules: ValidationRules<T>
) {
	const [values, setValues] = useState<T>(initialValues);
	const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});
	const [touched, setTouched] = useState<Partial<Record<keyof T, boolean>>>(
		{}
	);

	const setValue = <K extends keyof T>(field: K, value: T[K]) => {
		setValues((prev) => ({ ...prev, [field]: value }));

		if (touched[field]) {
			validateField(field, value);
		}
	};

	const validateField = <K extends keyof T>(field: K, value: T[K]) => {
		const rules = validationRules[field];
		let error: string | undefined;

		for (const rule of rules) {
			error = rule(value, values);
			if (error) break;
		}

		setErrors((prev) => ({ ...prev, [field]: error }));
		return !error;
	};

	const validateForm = (): boolean => {
		const newErrors: Partial<Record<keyof T, string>> = {};
		let isValid = true;

		(Object.keys(validationRules) as Array<keyof T>).forEach((field) => {
			const rules = validationRules[field];
			for (const rule of rules) {
				const error = rule(values[field], values);
				if (error) {
					newErrors[field] = error;
					isValid = false;
					break;
				}
			}
		});

		setErrors(newErrors);
		return isValid;
	};

	return {
		values,
		errors,
		touched,
		setValue,
		validateField,
		validateForm,
		setTouched,
	};
}

// Usage with complete type safety
function RegistrationForm() {
	const validationRules: ValidationRules<FormSchema> = {
		email: [
			(value) => (!value ? "Email is required" : undefined),
			(value) =>
				!/\S+@\S+\.\S+/.test(value) ? "Invalid email" : undefined,
		],
		password: [
			(value) => (!value ? "Password is required" : undefined),
			(value) =>
				value.length < 8
					? "Password must be at least 8 characters"
					: undefined,
		],
		confirmPassword: [
			(value) => (!value ? "Please confirm password" : undefined),
			(value, allValues) =>
				value !== allValues.password
					? "Passwords do not match"
					: undefined,
		],
		age: [
			(value) =>
				value < 18 ? "Must be at least 18 years old" : undefined,
		],
		terms: [(value) => (!value ? "You must accept the terms" : undefined)],
	};

	const form = useTypedForm(
		{
			email: "",
			password: "",
			confirmPassword: "",
			age: 0,
			terms: false,
		},
		validationRules
	);

	return (
		<form
			onSubmit={(e) => {
				e.preventDefault();
				if (form.validateForm()) {
					console.log("Valid form:", form.values);
				}
			}}
		>
			<input
				type="email"
				value={form.values.email}
				onChange={(e) => form.setValue("email", e.target.value)}
				onBlur={() =>
					form.setTouched((prev) => ({ ...prev, email: true }))
				}
			/>
			{form.touched.email && form.errors.email && (
				<span className="error">{form.errors.email}</span>
			)}

			{/* Other form fields... */}
		</form>
	);
}
```

### Generic Component Patterns

```tsx
// Generic data table component
interface Column<T> {
	key: keyof T;
	label: string;
	render?: (value: T[keyof T], item: T) => React.ReactNode;
	sortable?: boolean;
}

interface DataTableProps<T> {
	data: T[];
	columns: Column<T>[];
	onRowClick?: (item: T) => void;
	keyExtractor: (item: T) => string | number;
}

function DataTable<T>({
	data,
	columns,
	onRowClick,
	keyExtractor,
}: DataTableProps<T>) {
	const [sortConfig, setSortConfig] = useState<{
		key: keyof T;
		direction: "asc" | "desc";
	} | null>(null);

	const sortedData = useMemo(() => {
		if (!sortConfig) return data;

		return [...data].sort((a, b) => {
			const aValue = a[sortConfig.key];
			const bValue = b[sortConfig.key];

			if (aValue < bValue) return sortConfig.direction === "asc" ? -1 : 1;
			if (aValue > bValue) return sortConfig.direction === "asc" ? 1 : -1;
			return 0;
		});
	}, [data, sortConfig]);

	return (
		<table>
			<thead>
				<tr>
					{columns.map((column) => (
						<th key={String(column.key)}>
							{column.sortable ? (
								<button
									onClick={() =>
										setSortConfig({
											key: column.key,
											direction:
												sortConfig?.key ===
													column.key &&
												sortConfig.direction === "asc"
													? "desc"
													: "asc",
										})
									}
								>
									{column.label}
									{sortConfig?.key === column.key && (
										<span>
											{sortConfig.direction === "asc"
												? " ↑"
												: " ↓"}
										</span>
									)}
								</button>
							) : (
								column.label
							)}
						</th>
					))}
				</tr>
			</thead>

			<tbody>
				{sortedData.map((item) => (
					<tr
						key={keyExtractor(item)}
						onClick={() => onRowClick?.(item)}
						style={{ cursor: onRowClick ? "pointer" : "default" }}
					>
						{columns.map((column) => (
							<td key={String(column.key)}>
								{column.render
									? column.render(item[column.key], item)
									: String(item[column.key])}
							</td>
						))}
					</tr>
				))}
			</tbody>
		</table>
	);
}

// Usage with complete type inference
interface User {
	id: number;
	name: string;
	email: string;
	isActive: boolean;
	createdAt: Date;
}

function UserTable({ users }: { users: User[] }) {
	const columns: Column<User>[] = [
		{
			key: "name",
			label: "Name",
			sortable: true,
		},
		{
			key: "email",
			label: "Email",
			sortable: true,
		},
		{
			key: "isActive",
			label: "Status",
			render: (value) => (
				<span className={`status ${value ? "active" : "inactive"}`}>
					{value ? "Active" : "Inactive"}
				</span>
			),
		},
		{
			key: "createdAt",
			label: "Created",
			render: (value) => value.toLocaleDateString(),
			sortable: true,
		},
	];

	return (
		<DataTable
			data={users}
			columns={columns}
			keyExtractor={(user) => user.id}
			onRowClick={(user) => console.log("Clicked user:", user)}
		/>
	);
}
```

---

## Performance Budgets & Monitoring

### Core Web Vitals Implementation

**Problem:** How do we monitor and maintain performance standards in production?

```jsx
// Core Web Vitals monitoring
import { getCLS, getFID, getFCP, getLCP, getTTFB } from "web-vitals";

class PerformanceMonitor {
	constructor(config = {}) {
		this.config = {
			reportUrl: "/api/analytics/vitals",
			sampleRate: 1,
			debug: false,
			...config,
		};

		this.metrics = new Map();
		this.init();
	}

	init() {
		// Only run on client side
		if (typeof window === "undefined") return;

		// Measure Core Web Vitals
		getCLS(this.handleMetric.bind(this));
		getFID(this.handleMetric.bind(this));
		getFCP(this.handleMetric.bind(this));
		getLCP(this.handleMetric.bind(this));
		getTTFB(this.handleMetric.bind(this));

		// Custom React-specific metrics
		this.measureReactMetrics();

		// Send metrics before page unload
		window.addEventListener("beforeunload", () => {
			this.sendMetrics(true);
		});
	}

	handleMetric(metric) {
		if (this.config.debug) {
			console.log("Performance metric:", metric);
		}

		this.metrics.set(metric.name, metric);

		// Send critical metrics immediately
		if (["FID", "CLS"].includes(metric.name)) {
			this.sendMetrics();
		}
	}

	measureReactMetrics() {
		// Measure React hydration time
		const hydrationStart = performance.mark("react-hydration-start");

		// This would be called after React hydration
		const markHydrationEnd = () => {
			performance.mark("react-hydration-end");
			performance.measure(
				"react-hydration",
				"react-hydration-start",
				"react-hydration-end"
			);

			const measure = performance.getEntriesByName("react-hydration")[0];
			if (measure) {
				this.handleMetric({
					name: "React-Hydration",
					value: measure.duration,
					id: "react-hydration",
				});
			}
		};

		// Observe React DevTools for hydration completion
		if (window.__REACT_DEVTOOLS_GLOBAL_HOOK__) {
			window.__REACT_DEVTOOLS_GLOBAL_HOOK__.onCommitFiberRoot = () => {
				markHydrationEnd();
			};
		}
	}

	async sendMetrics(isBeacon = false) {
		if (Math.random() > this.config.sampleRate) return;

		const metricsData = {
			url: window.location.href,
			userAgent: navigator.userAgent,
			connection: navigator.connection?.effectiveType,
			metrics: Array.from(this.metrics.entries()).map(
				([name, metric]) => ({
					name,
					value: metric.value,
					id: metric.id,
					delta: metric.delta,
				})
			),
			timestamp: Date.now(),
		};

		try {
			if (isBeacon && navigator.sendBeacon) {
				navigator.sendBeacon(
					this.config.reportUrl,
					JSON.stringify(metricsData)
				);
			} else {
				await fetch(this.config.reportUrl, {
					method: "POST",
					headers: { "Content-Type": "application/json" },
					body: JSON.stringify(metricsData),
				});
			}
		} catch (error) {
			console.error("Failed to send performance metrics:", error);
		}
	}
}

// React component for performance monitoring
function PerformanceProvider({ children }) {
	useEffect(() => {
		const monitor = new PerformanceMonitor({
			reportUrl: process.env.NEXT_PUBLIC_ANALYTICS_URL,
			sampleRate: process.env.NODE_ENV === "production" ? 0.1 : 1,
			debug: process.env.NODE_ENV === "development",
		});

		return () => {
			// Cleanup if needed
		};
	}, []);

	return children;
}
```

### Real User Monitoring (RUM)

```jsx
// Advanced RUM implementation
class RUMCollector {
	constructor() {
		this.sessionId = this.generateSessionId();
		this.pageLoadStart = performance.now();
		this.interactions = [];
		this.errors = [];

		this.setupObservers();
	}

	setupObservers() {
		// Performance Observer for navigation timing
		if ("PerformanceObserver" in window) {
			const perfObserver = new PerformanceObserver((list) => {
				for (const entry of list.getEntries()) {
					if (entry.entryType === "navigation") {
						this.collectNavigationTiming(entry);
					} else if (entry.entryType === "largest-contentful-paint") {
						this.collectLCP(entry);
					} else if (entry.entryType === "first-input") {
						this.collectFID(entry);
					}
				}
			});

			perfObserver.observe({
				entryTypes: [
					"navigation",
					"largest-contentful-paint",
					"first-input",
				],
			});
		}

		// Error tracking
		window.addEventListener("error", (event) => {
			this.collectError({
				type: "javascript",
				message: event.message,
				filename: event.filename,
				lineno: event.lineno,
				colno: event.colno,
				stack: event.error?.stack,
			});
		});

		// Unhandled promise rejections
		window.addEventListener("unhandledrejection", (event) => {
			this.collectError({
				type: "promise",
				message: event.reason?.message || "Unhandled promise rejection",
				stack: event.reason?.stack,
			});
		});

		// User interactions
		["click", "keypress", "scroll"].forEach((eventType) => {
			document.addEventListener(
				eventType,
				(event) => {
					this.collectInteraction(eventType, event);
				},
				{ passive: true }
			);
		});
	}

	collectNavigationTiming(entry) {
		const timing = {
			dns: entry.domainLookupEnd - entry.domainLookupStart,
			tcp: entry.connectEnd - entry.connectStart,
			ssl:
				entry.secureConnectionStart > 0
					? entry.connectEnd - entry.secureConnectionStart
					: 0,
			ttfb: entry.responseStart - entry.requestStart,
			download: entry.responseEnd - entry.responseStart,
			domParse: entry.domContentLoadedEventStart - entry.responseEnd,
			domReady:
				entry.domContentLoadedEventEnd -
				entry.domContentLoadedEventStart,
			pageLoad: entry.loadEventEnd - entry.loadEventStart,
		};

		this.sendMetric("navigation-timing", timing);
	}

	collectLCP(entry) {
		this.sendMetric("largest-contentful-paint", {
			value: entry.startTime,
			size: entry.size,
			element: entry.element?.tagName,
		});
	}

	collectFID(entry) {
		this.sendMetric("first-input-delay", {
			value: entry.processingStart - entry.startTime,
			eventType: entry.name,
		});
	}

	collectError(error) {
		this.errors.push({
			...error,
			timestamp: Date.now(),
			url: window.location.href,
			sessionId: this.sessionId,
		});

		// Send critical errors immediately
		this.sendMetric("error", error);
	}

	collectInteraction(type, event) {
		this.interactions.push({
			type,
			timestamp: Date.now(),
			target: event.target?.tagName,
			x: event.clientX,
			y: event.clientY,
		});
	}

	sendMetric(name, data) {
		const metric = {
			name,
			data,
			sessionId: this.sessionId,
			timestamp: Date.now(),
			url: window.location.href,
			userAgent: navigator.userAgent,
			viewport: {
				width: window.innerWidth,
				height: window.innerHeight,
			},
		};

		// Send to analytics service
		fetch("/api/rum", {
			method: "POST",
			headers: { "Content-Type": "application/json" },
			body: JSON.stringify(metric),
			keepalive: true,
		}).catch(() => {
			// Fallback to beacon API
			navigator.sendBeacon("/api/rum", JSON.stringify(metric));
		});
	}

	generateSessionId() {
		return Date.now().toString(36) + Math.random().toString(36).substr(2);
	}
}

// React hook for RUM
function useRUM() {
	useEffect(() => {
		const rum = new RUMCollector();

		// Track React-specific metrics
		const trackRender = (phase, actualDuration, baseDuration) => {
			rum.sendMetric("react-render", {
				phase,
				actualDuration,
				baseDuration,
				component: "App",
			});
		};

		return () => {
			// Cleanup
		};
	}, []);
}
```

---

## Testing for Reliability

### Visual Regression Testing

**Problem:** How do we prevent UI regressions while maintaining development velocity?

```jsx
// Visual regression testing setup with Storybook and Chromatic
// .storybook/main.js
module.exports = {
	stories: ["../src/**/*.stories.@(js|jsx|ts|tsx)"],
	addons: [
		"@storybook/addon-essentials",
		"@storybook/addon-a11y",
		"@chromatic-com/storybook",
	],
};

// Component story for visual testing
// Button.stories.jsx
export default {
	title: "Components/Button",
	component: Button,
	parameters: {
		chromatic: {
			delay: 300,
			pauseAnimationAtEnd: true,
		},
	},
};

export const Primary = {
	args: {
		variant: "primary",
		children: "Click me",
	},
};

export const AllVariants = () => (
	<div style={{ display: "flex", gap: "1rem", flexWrap: "wrap" }}>
		<Button variant="primary">Primary</Button>
		<Button variant="secondary">Secondary</Button>
		<Button variant="danger">Danger</Button>
		<Button variant="primary" disabled>
			Disabled
		</Button>
		<Button variant="primary" loading>
			Loading
		</Button>
	</div>
);

export const DarkMode = {
	...Primary,
	parameters: {
		backgrounds: { default: "dark" },
		chromatic: { modes: { dark: { disable: false } } },
	},
};

// Mobile responsive variants
export const MobileVariants = () => (
	<div>
		<Button variant="primary" fullWidth>
			Full Width Mobile
		</Button>
		<Button variant="primary" size="small">
			Small Mobile
		</Button>
	</div>
);

MobileVariants.parameters = {
	viewport: { defaultViewport: "mobile1" },
	chromatic: { viewports: [320, 375, 414] },
};

// Interaction testing
export const WithInteractions = {
	args: Primary.args,
	play: async ({ canvasElement }) => {
		const canvas = within(canvasElement);
		const button = canvas.getByRole("button");

		await userEvent.hover(button);
		await expect(button).toHaveClass("btn-hover");

		await userEvent.click(button);
		await expect(button).toHaveClass("btn-active");
	},
};
```

### Contract Testing for API Integration

```jsx
// Contract testing setup with MSW (Mock Service Worker)
// tests/mocks/handlers.js
import { rest } from "msw";

export const handlers = [
	// User API contract
	rest.get("/api/users/:id", (req, res, ctx) => {
		const { id } = req.params;

		// Contract validation
		if (!id || isNaN(Number(id))) {
			return res(ctx.status(400), ctx.json({ error: "Invalid user ID" }));
		}

		return res(
			ctx.status(200),
			ctx.json({
				id: Number(id),
				name: "John Doe",
				email: "john@example.com",
				createdAt: "2023-01-01T00:00:00Z",
			})
		);
	}),

	// Create user contract
	rest.post("/api/users", async (req, res, ctx) => {
		const userData = await req.json();

		// Validate required fields
		if (!userData.name || !userData.email) {
			return res(
				ctx.status(400),
				ctx.json({
					error: "Name and email are required",
					fields: {
						name: !userData.name ? "Name is required" : null,
						email: !userData.email ? "Email is required" : null,
					},
				})
			);
		}

		// Validate email format
		if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(userData.email)) {
			return res(
				ctx.status(400),
				ctx.json({
					error: "Invalid email format",
					fields: { email: "Invalid email format" },
				})
			);
		}

		return res(
			ctx.status(201),
			ctx.json({
				id: Math.floor(Math.random() * 1000),
				...userData,
				createdAt: new Date().toISOString(),
			})
		);
	}),
];

// Contract test suite
// tests/contracts/userApi.test.js
import { render, screen, waitFor } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { server } from "../mocks/server";
import { rest } from "msw";
import UserForm from "../components/UserForm";

describe("User API Contract Tests", () => {
	test("should handle successful user creation", async () => {
		render(<UserForm />);

		const nameInput = screen.getByLabelText(/name/i);
		const emailInput = screen.getByLabelText(/email/i);
		const submitButton = screen.getByRole("button", {
			name: /create user/i,
		});

		await userEvent.type(nameInput, "Jane Doe");
		await userEvent.type(emailInput, "jane@example.com");
		await userEvent.click(submitButton);

		await waitFor(() => {
			expect(
				screen.getByText(/user created successfully/i)
			).toBeInTheDocument();
		});
	});

	test("should handle validation errors correctly", async () => {
		render(<UserForm />);

		const submitButton = screen.getByRole("button", {
			name: /create user/i,
		});
		await userEvent.click(submitButton);

		await waitFor(() => {
			expect(screen.getByText(/name is required/i)).toBeInTheDocument();
			expect(screen.getByText(/email is required/i)).toBeInTheDocument();
		});
	});

	test("should handle invalid email format", async () => {
		render(<UserForm />);

		const nameInput = screen.getByLabelText(/name/i);
		const emailInput = screen.getByLabelText(/email/i);
		const submitButton = screen.getByRole("button", {
			name: /create user/i,
		});

		await userEvent.type(nameInput, "Jane Doe");
		await userEvent.type(emailInput, "invalid-email");
		await userEvent.click(submitButton);

		await waitFor(() => {
			expect(
				screen.getByText(/invalid email format/i)
			).toBeInTheDocument();
		});
	});

	test("should handle server errors gracefully", async () => {
		// Override handler for this test
		server.use(
			rest.post("/api/users", (req, res, ctx) => {
				return res(
					ctx.status(500),
					ctx.json({ error: "Internal server error" })
				);
			})
		);

		render(<UserForm />);

		const nameInput = screen.getByLabelText(/name/i);
		const emailInput = screen.getByLabelText(/email/i);
		const submitButton = screen.getByRole("button", {
			name: /create user/i,
		});

		await userEvent.type(nameInput, "Jane Doe");
		await userEvent.type(emailInput, "jane@example.com");
		await userEvent.click(submitButton);

		await waitFor(() => {
			expect(
				screen.getByText(/something went wrong/i)
			).toBeInTheDocument();
		});
	});
});
```

### Test Flakiness Mitigation

```jsx
// Utilities to reduce test flakiness
// tests/utils/testUtils.js

// Robust waiting utilities
export const waitForElementToBeRemoved = async (element, options = {}) => {
	const { timeout = 5000, interval = 50 } = options;

	return new Promise((resolve, reject) => {
		const startTime = Date.now();

		const checkElement = () => {
			if (!document.contains(element)) {
				resolve();
				return;
			}

			if (Date.now() - startTime > timeout) {
				reject(
					new Error(`Element was not removed within ${timeout}ms`)
				);
				return;
			}

			setTimeout(checkElement, interval);
		};

		checkElement();
	});
};

// Stable test data factories
export const createTestUser = (overrides = {}) => ({
	id: Math.floor(Math.random() * 1000),
	name: "Test User",
	email: "test@example.com",
	createdAt: "2023-01-01T00:00:00Z",
	...overrides,
});

export const createTestUsers = (count = 3) => {
	return Array.from({ length: count }, (_, index) =>
		createTestUser({
			id: index + 1,
			name: `Test User ${index + 1}`,
			email: `test${index + 1}@example.com`,
		})
	);
};

// Network request mocking with delays
export const createDelayedHandler = (handler, delay = 100) => {
	return async (req, res, ctx) => {
		await new Promise((resolve) => setTimeout(resolve, delay));
		return handler(req, res, ctx);
	};
};

// Component testing utilities
export const renderWithProviders = (ui, options = {}) => {
	const { initialState = {}, route = "/", ...renderOptions } = options;

	const AllProviders = ({ children }) => (
		<BrowserRouter>
			<QueryClient>
				<ThemeProvider theme="light">{children}</ThemeProvider>
			</QueryClient>
		</BrowserRouter>
	);

	window.history.pushState({}, "Test page", route);

	return render(ui, { wrapper: AllProviders, ...renderOptions });
};

// Deterministic async testing
export const waitForStableRender = async (element) => {
	let previousHTML = element.innerHTML;
	let attempts = 0;
	const maxAttempts = 10;

	while (attempts < maxAttempts) {
		await waitFor(() => {}, { timeout: 100 });

		if (element.innerHTML === previousHTML) {
			return; // Stable
		}

		previousHTML = element.innerHTML;
		attempts++;
	}

	throw new Error("Component did not stabilize after multiple attempts");
};

// Usage in tests
describe("Stable Component Tests", () => {
	test("user list renders correctly", async () => {
		const users = createTestUsers(5);

		server.use(
			rest.get(
				"/api/users",
				createDelayedHandler((req, res, ctx) => {
					return res(ctx.json({ users }));
				}, 200)
			)
		);

		const { container } = renderWithProviders(<UserList />);

		// Wait for loading to finish
		await waitForElementToBeRemoved(screen.getByText(/loading/i));

		// Ensure render is stable
		await waitForStableRender(container);

		// Now assert on stable state
		expect(screen.getAllByTestId("user-item")).toHaveLength(5);

		users.forEach((user) => {
			expect(screen.getByText(user.name)).toBeInTheDocument();
		});
	});
});
```

---

**Final Stage Complete!**

You've mastered React from Beginner to Pro level. Continue practicing these concepts and stay updated with the React ecosystem.

---

## Expert & Architectural Mastery Checklist

Congratulations! If you can confidently work with all these concepts, you're ready for:

-   [ ] Senior/Lead React Developer positions
-   [ ] Technical architecture decisions
-   [ ] Team mentoring and code reviews
-   [ ] Performance optimization initiatives
-   [ ] Large-scale application design
-   [ ] Cross-team collaboration and API design
-   [ ] Modern React ecosystem leadership

**Keep Learning:** The React ecosystem evolves rapidly. Stay updated with RFC discussions, experimental features, and emerging patterns in the community.
