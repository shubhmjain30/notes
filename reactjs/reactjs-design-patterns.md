# React Design Patterns

This file is a concise pattern guide for interviews. The goal is to know what each pattern solves, when to use it, and when not to use it.

## Pattern Priority

### Tier 1

These are the patterns interviewers ask about most often:

- Custom hooks
- Provider and Context pattern
- Container and presentational components
- Compound components
- Memoization patterns

### Tier 2

These appear less often but still matter:

- Higher-order components
- Render props
- Error boundaries
- Lazy loading and code splitting

## Quick Selection Guide

| Problem                                                      | Pattern to prefer first      |
| ------------------------------------------------------------ | ---------------------------- |
| Reusing stateful logic                                       | Custom hook                  |
| Sharing low-frequency values across a subtree                | Provider and Context         |
| Separating UI from business logic                            | Container and presentational |
| Building expressive reusable UI APIs                         | Compound components          |
| Avoiding unnecessary re-renders                              | Memoization patterns         |
| Cross-cutting enhancement in legacy code                     | HOC                          |
| Need render-time flexibility from a reusable logic component | Render props                 |
| Isolating UI crashes                                         | Error boundary               |
| Reducing initial bundle size                                 | Lazy loading                 |

## Custom Hooks

### What It Is

A custom hook is a reusable function that starts with `use` and composes React hooks.

### Best Use Cases

- Repeated fetch logic
- Local storage syncing
- Form logic
- Subscriptions and browser APIs

### Pros

- Reuses logic without adding wrapper components
- Keeps components smaller and easier to read
- Fits modern React naturally

### Cons

- Easy to over-abstract
- Hidden behavior can make debugging harder if naming is poor

### Interview Trap

Do not say custom hooks share state between components automatically. They share logic, not state instances.

### Example

```jsx
function useWindowWidth() {
	const [width, setWidth] = useState(window.innerWidth);

	useEffect(() => {
		function handleResize() {
			setWidth(window.innerWidth);
		}
		window.addEventListener("resize", handleResize);
		return () => window.removeEventListener("resize", handleResize);
	}, []);

	return width;
}
```

## Provider And Context Pattern

### What It Is

A provider exposes a value to all consumers below it in the component tree.

### Best Use Cases

- Theme
- Auth session
- Locale
- Feature flags

### Pros

- Removes prop drilling
- Simple for low-frequency global values
- Built into React

### Cons

- Broad consumer re-renders if misused
- Not a complete substitute for external state architecture

### Interview Trap

Context helps distribute values. It does not automatically solve large-scale state complexity.

### Example

```jsx
const ThemeContext = createContext(null);

function ThemeProvider({ children }) {
	const [theme, setTheme] = useState("light");
	const value = { theme, setTheme };
	return (
		<ThemeContext.Provider value={value}>{children}</ThemeContext.Provider>
	);
}
```

## Container And Presentational Components

### What It Is

A container handles data and behavior. A presentational component focuses on rendering UI from props.

### Best Use Cases

- Complex screens with shared business logic
- Reusable UI components that should stay dumb
- Teams that want clearer separation of logic and display

### Pros

- Strong separation of concerns
- Easier testing
- Good stepping stone toward custom hooks

### Cons

- Can create extra files and indirection
- Can become outdated if used mechanically everywhere

### Interview Trap

In modern React, many teams replace explicit container components with custom hooks plus presentational components.

### Example

```jsx
function UserListContainer() {
	const { users, isLoading } = useUsers();
	return <UserList users={users} isLoading={isLoading} />;
}

function UserList({ users, isLoading }) {
	if (isLoading) return <p>Loading...</p>;
	return users.map((user) => <div key={user.id}>{user.name}</div>);
}
```

## Compound Components

### What It Is

A parent component and related child components work together through shared internal state and context.

### Best Use Cases

- Tabs
- Accordions
- Modals
- Dropdown menus

### Pros

- Flexible API
- Natural composition
- Great for component libraries

### Cons

- Internals are more complex
- Usually requires context

### Interview Trap

This pattern is excellent for reusable UI primitives, not for every ordinary feature component.

### Example

```jsx
<Tabs>
	<Tabs.List>
		<Tabs.Tab id="profile">Profile</Tabs.Tab>
		<Tabs.Tab id="billing">Billing</Tabs.Tab>
	</Tabs.List>
	<Tabs.Panels>
		<Tabs.Panel id="profile">Profile content</Tabs.Panel>
		<Tabs.Panel id="billing">Billing content</Tabs.Panel>
	</Tabs.Panels>
</Tabs>
```

## Memoization Patterns

### What It Is

Memoization keeps React from recomputing values or re-rendering components unnecessarily.

### Tools

- `React.memo`
- `useMemo`
- `useCallback`

### Best Use Cases

- Expensive derived values
- Expensive child components
- Callback props passed to memoized children

### Pros

- Can improve performance in hot paths
- Helps control render churn

### Cons

- Easy to overuse
- Adds mental overhead
- Can make code harder to reason about

### Interview Trap

Memoization is not a default best practice. It is a targeted optimization.

## Higher-Order Components

### What It Is

A function takes a component and returns an enhanced component.

### Best Use Cases

- Legacy codebases
- Cross-cutting concerns such as auth wrappers or analytics wrappers
- Library APIs that still expose HOCs

### Pros

- Reuse behavior across components
- Encapsulate cross-cutting concerns

### Cons

- Wrapper nesting
- Prop collision risk
- Harder debugging

### Interview Trap

In modern React, custom hooks are usually preferred for reusing logic inside function components.

### Example

```jsx
function withAuth(Component) {
	return function AuthenticatedComponent(props) {
		const user = useAuth();
		return user ? <Component {...props} user={user} /> : <LoginPage />;
	};
}
```

## Render Props

### What It Is

A component receives a function prop and calls it to decide what UI to render.

### Best Use Cases

- Highly flexible reusable behavior components
- Libraries that want to expose behavior without dictating markup

### Pros

- Very flexible
- Separates logic from rendering clearly

### Cons

- Can create nested function-heavy JSX
- Less common in modern app code

### Interview Trap

Render props are still valid, but many cases that used them are now simpler with custom hooks.

### Example

```jsx
<MouseTracker>
	{({ x, y }) => (
		<p>
			{x}, {y}
		</p>
	)}
</MouseTracker>
```

## Error Boundaries

### What It Is

An error boundary catches render-time errors in a subtree and shows fallback UI.

### Best Use Cases

- Route shells
- Dashboard panels
- Third-party widget boundaries

### Pros

- Prevents one broken subtree from crashing the whole app
- Improves resilience and recovery

### Cons

- Does not catch event handler or arbitrary async errors
- Still class-based unless using a helper library

### Interview Trap

Error boundaries are not the same as `try/catch` around event handlers.

## Lazy Loading And Code Splitting

### What It Is

Load code only when the user reaches the relevant route or feature.

### Best Use Cases

- Route-level bundles
- Admin-only areas
- Heavy dashboards or editors

### Pros

- Smaller initial bundle
- Faster first load

### Cons

- More loading states to manage
- Bad boundary placement can hurt UX

### Interview Trap

Lazy loading improves initial load, not every performance problem. It helps bundle size more than CPU-heavy rendering.

## What To Prefer In Modern React

In most production React code:

1. Prefer composition over inheritance.
2. Prefer custom hooks over HOCs or render props for reusable logic.
3. Prefer Context for simple shared values, not as a default global store.
4. Prefer compound components for reusable UI primitives.
5. Prefer memoization only when profiling shows it helps.

## What To Revise Next

Pair this file with [reactjs-advanced-engineering.md](reactjs-advanced-engineering.md) so you can explain both pattern choice and architectural tradeoffs.
