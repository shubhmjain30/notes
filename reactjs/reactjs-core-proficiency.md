# Stage 2: Core Proficiency

This is the highest-yield React interview file in the folder. Most practical React interviews are built on the topics here.

## Checklist

- Rules of Hooks
- useState and useReducer
- useEffect and cleanup
- useMemo and useCallback basics
- useRef and DOM access
- Custom hooks
- Context API basics
- Error boundaries
- Forms
- Routing, testing, and TypeScript basics

## Rules Of Hooks

### TL;DR

Hooks depend on call order. That is why they must be called at the top level of React components or other hooks.

### The Two Rules

1. Only call hooks at the top level.
2. Only call hooks from React components or custom hooks.

### Common Traps

- Calling a hook inside `if`, `for`, or nested functions.
- Treating a regular utility function like a custom hook.

### 30-Second Interview Answer

Hooks rely on consistent call order so React can associate each hook call with the correct internal state slot. If the order changes between renders, React can no longer match state correctly.

## useState And useReducer

### useState

Use it for simple, isolated state.

### useReducer

Use it when:

- State transitions are complex.
- Multiple fields change together.
- You want predictable action-based updates.

### Common Traps

- Mutating objects or arrays directly.
- Forgetting functional updates when next state depends on previous state.

### Example

```jsx
function Counter() {
	const [count, setCount] = useState(0);

	return (
		<button onClick={() => setCount((prev) => prev + 1)}>{count}</button>
	);
}
```

```jsx
function formReducer(state, action) {
	switch (action.type) {
		case "change":
			return { ...state, [action.field]: action.value };
		case "reset":
			return action.initialState;
		default:
			return state;
	}
}
```

### 30-Second Interview Answer

I use `useState` for simple local values and `useReducer` when updates are related, more complex, or easier to reason about as actions.

## useEffect

### TL;DR

`useEffect` synchronizes your component with something outside React, such as network requests, subscriptions, timers, or browser APIs.

### Dependency Array Rules

- No dependency array: runs after every render.
- Empty dependency array: runs after mount.
- Values in dependency array: runs when those values change.

### Common Traps

- Missing dependencies and accidentally reading stale values.
- Causing infinite loops by updating a dependency from inside the effect.
- Forgetting cleanup for subscriptions, timers, or listeners.

### Good Mental Model

Do not think of `useEffect` as "componentDidMount for functions." Think of it as "keep external systems in sync with current render state."

### Example

```jsx
function UserProfile({ userId }) {
	const [user, setUser] = useState(null);

	useEffect(() => {
		const controller = new AbortController();

		async function loadUser() {
			const response = await fetch(`/api/users/${userId}`, {
				signal: controller.signal,
			});
			const data = await response.json();
			setUser(data);
		}

		loadUser().catch((error) => {
			if (error.name !== "AbortError") {
				throw error;
			}
		});

		return () => controller.abort();
	}, [userId]);

	return <div>{user ? user.name : "Loading..."}</div>;
}
```

### 30-Second Interview Answer

I use `useEffect` for side effects, not for normal render logic. The dependency array defines when the effect should re-run, and the cleanup function prevents leaks or stale subscriptions.

## useMemo And useCallback

### TL;DR

Use these only when you have an actual re-render or expensive computation problem.

### Difference

| Hook          | Caches               | Use when                                    |
| ------------- | -------------------- | ------------------------------------------- |
| `useMemo`     | A computed value     | A calculation is expensive                  |
| `useCallback` | A function reference | A child depends on stable callback identity |

### Common Traps

- Using them everywhere by default.
- Forgetting that they also add complexity.
- Trying to fix performance without profiling first.

## useRef

### TL;DR

`useRef` stores a mutable value that survives renders without causing re-renders.

### Common Uses

- DOM access
- Focus management
- Interval or timeout IDs
- Previous values

### Example

```jsx
function FocusInput() {
	const inputRef = useRef(null);

	return (
		<>
			<input ref={inputRef} />
			<button onClick={() => inputRef.current?.focus()}>Focus</button>
		</>
	);
}
```

## Custom Hooks

### TL;DR

Custom hooks let you reuse stateful logic without changing the component tree.

### When To Create One

- The same effect or state logic appears in multiple components.
- A component is getting noisy because business logic and UI are mixed.
- You want a more readable API around repeated behavior.

### Common Traps

- Creating a custom hook too early for one small use case.
- Hiding too much behavior and making flow hard to follow.

### Example

```jsx
function useLocalStorage(key, initialValue) {
	const [value, setValue] = useState(() => {
		const storedValue = window.localStorage.getItem(key);
		return storedValue ? JSON.parse(storedValue) : initialValue;
	});

	useEffect(() => {
		window.localStorage.setItem(key, JSON.stringify(value));
	}, [key, value]);

	return [value, setValue];
}
```

## Context API Basics

### TL;DR

Context helps avoid prop drilling by making a value available to many components in a subtree.

### Best Use Cases

- Theme
- Auth user
- Locale
- UI preferences

### Caveat

Context is not automatically a full state-management solution for large apps. If many consumers depend on frequently changing values, re-render pressure can become a problem.

### Common Traps

- Putting unrelated values into one big context object.
- Updating a frequently changing context value and forcing too many consumers to re-render.

### Optimization Rules

- Split contexts by concern.
- Memoize provider values when appropriate.
- Keep high-frequency state local when possible.

### Interview Answer

Context is best for sharing low-to-medium frequency global values across a subtree. For larger, more complex state flows, evaluate external stores such as Redux Toolkit.

For the architecture comparison, see [reactjs-advanced-engineering.md](reactjs-advanced-engineering.md#state-management-strategies).

## Error Boundaries

### TL;DR

Error boundaries catch render-time errors in a subtree and show fallback UI instead of crashing the entire app.

### Important Limitation

They do not catch:

- Event handler errors
- Async errors outside render flow
- Server-side errors

### Interview Answer

Use error boundaries around unstable or important UI regions so one crash does not take down the whole app.

## Forms

### Controlled Vs Uncontrolled

| Type         | Source of truth | Best when                              |
| ------------ | --------------- | -------------------------------------- |
| Controlled   | React state     | Validation, dynamic UI, business rules |
| Uncontrolled | DOM             | Simple forms, quick integration        |

### Common Traps

- Using controlled inputs everywhere without a reason in large forms.
- Letting form state spread across too many sibling components.

### Interview Answer

Controlled components give React full control over input state, which helps with validation and derived UI. Uncontrolled inputs are simpler when you only need the final value.

## Routing

### What To Know

- `BrowserRouter` provides client-side routing.
- `Routes` and `Route` define route matching.
- `useParams` reads dynamic route params.
- `useNavigate` handles programmatic navigation.
- `Outlet` renders nested routes.

## Testing Basics

### What Interviewers Expect

- Prefer testing behavior over implementation details.
- React Testing Library is the current default choice.
- Use `screen`, user interactions, and async queries correctly.

### Good One-Liner

I test what the user can see and do, not internal component methods or hook implementation details.

## TypeScript Basics With React

### What To Know

- How to type props.
- How to type event handlers.
- How to type reusable generic components.
- When to use discriminated unions for component APIs.

## Rapid-Fire Questions

### Why do hooks need rules?

Because React relies on stable call order to preserve hook state correctly.

### When do you use useReducer over useState?

When state transitions are complex or multiple fields change together.

### What is useEffect used for?

Synchronizing with systems outside React, such as APIs, timers, subscriptions, or DOM APIs.

### When would you create a custom hook?

When the same stateful logic is repeated or a component becomes hard to read because logic and UI are mixed.

### Is Context a replacement for Redux?

Not by itself. Context shares values; Redux-style stores add stronger structure, tooling, and update patterns.

## What To Revise Next

Read [reactjs-design-patterns.md](reactjs-design-patterns.md) and [reactjs-advanced-engineering.md](reactjs-advanced-engineering.md).
