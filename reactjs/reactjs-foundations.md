# Stage 1: Foundations

This file covers the React topics that show up in beginner interviews again and again. The goal is fast recall, not exhaustive theory.

## Checklist

- JSX and rendering basics
- Functional vs class components
- Props and state
- Event handling
- Conditional rendering
- Lists and keys
- Basic styling
- Bootstrapping and tooling

## JSX And Rendering Basics

### TL;DR

JSX is syntax sugar for React element creation. React components return JSX, and React turns it into UI.

### What You Must Know

- JSX is not HTML, even if it looks similar.
- A component returns one root element.
- Use `className` instead of `class`.
- Use `htmlFor` instead of `for`.
- JavaScript expressions go inside `{}`.

### Common Traps

- Returning sibling elements without a wrapper.
- Forgetting to close self-closing tags.
- Trying to use statements like `if` directly inside JSX.

### 30-Second Interview Answer

JSX is a syntax extension that lets us describe UI using HTML-like syntax inside JavaScript. During compilation, it becomes React element creation calls. It improves readability, but it still follows JavaScript rules.

### Example

```jsx
function WelcomeMessage({ user, notifications }) {
	return (
		<>
			<h1>Welcome, {user.name}</h1>
			<p>You have {notifications.length} notifications</p>
		</>
	);
}
```

## Components

### TL;DR

Modern React uses functional components. Class components still matter for legacy code and older interview questions.

### Functional Vs Class Components

| Topic                  | Functional Component | Class Component   |
| ---------------------- | -------------------- | ----------------- |
| Syntax                 | Plain function       | ES6 class         |
| State                  | Hooks                | `this.state`      |
| Side effects           | Hooks                | Lifecycle methods |
| Current recommendation | Preferred            | Legacy knowledge  |

### Common Traps

- Thinking class components are gone. They still exist in older codebases.
- Forgetting that functional components are re-run on every render.

### 30-Second Interview Answer

Functional components are the modern default because Hooks give them state and lifecycle capabilities without class syntax. Class components still matter in legacy apps and for understanding lifecycle-based code.

## Props And State

### TL;DR

Props are external inputs. State is internal component memory.

### Core Distinction

| Topic      | Props                 | State               |
| ---------- | --------------------- | ------------------- |
| Ownership  | Parent component      | Current component   |
| Mutability | Read-only to child    | Updated via setter  |
| Purpose    | Configure a component | Track changing data |

### Common Traps

- Mutating props.
- Mutating state objects directly.
- Copying props into state without a good reason.

### 30-Second Interview Answer

Props flow from parent to child and are read-only from the child perspective. State is owned by the component and changes over time, usually because of user interaction or async updates.

### Example

```jsx
function Counter({ initialValue = 0 }) {
	const [count, setCount] = useState(initialValue);

	return (
		<button onClick={() => setCount((prev) => prev + 1)}>{count}</button>
	);
}
```

## Event Handling

### TL;DR

React uses synthetic events, but the API feels close to browser events.

### What Matters In Interviews

- Event handlers are passed as functions, not strings.
- React normalizes browser differences with synthetic events.
- State is often lifted up when siblings need shared behavior.

### Common Traps

- Calling the handler during render instead of passing a function.
- Keeping shared state too low in the tree.

### Example

```jsx
function SearchBox({ onSearch }) {
	const handleChange = (event) => {
		onSearch(event.target.value);
	};

	return <input onChange={handleChange} placeholder="Search" />;
}
```

## Conditional Rendering

### TL;DR

React uses plain JavaScript for conditions. The main tools are `if`, ternary expressions, and `&&`.

### Common Traps

- Rendering `0` by accident with `count && <Badge />`.
- Making JSX unreadable with large nested ternaries.

### Example

```jsx
function UserStatus({ user, isLoading, error }) {
	if (isLoading) return <p>Loading...</p>;
	if (error) return <p>Error: {error}</p>;

	return user ? <p>Welcome, {user.name}</p> : <p>Please sign in</p>;
}
```

## Lists And Keys

### TL;DR

Use `.map()` to render lists and stable keys to help React preserve identity correctly.

### Why Keys Matter

- Keys help React match old and new children during reconciliation.
- Stable keys preserve component state correctly.
- Index keys are risky when the list can be reordered, filtered, or inserted into.

### Common Traps

- Using array index as key for dynamic lists.
- Using non-unique keys.
- Forgetting that changing a key forces remount.

### 30-Second Interview Answer

Keys give each list item a stable identity so React can reconcile efficiently. If keys are unstable, React may reuse the wrong component instance and create UI or state bugs.

### Example

```jsx
function TaskList({ tasks }) {
	return (
		<ul>
			{tasks.map((task) => (
				<li key={task.id}>{task.title}</li>
			))}
		</ul>
	);
}
```

## Basic Styling

### TL;DR

React does not force a styling strategy. For interviews, know CSS Modules, inline styles, and standard CSS conventions.

### What To Say

- CSS Modules help avoid class-name collisions.
- Inline styles are useful for dynamic, one-off values.
- Global CSS works fine if naming is disciplined.

### Common Traps

- Putting too much styling logic into inline objects.
- Recreating large style objects on every render when not necessary.

## Project Bootstrapping And Tooling

### TL;DR

For a standard SPA, the modern default is Vite. For SSR or full-stack React, use Next.js.

### Interview Points

- Vite is fast and is the modern default for client-rendered React apps.
- CRA is legacy knowledge, not the recommended default.
- React DevTools, ESLint, and Prettier are basic workflow tools.

## Rapid-Fire Questions

### What is JSX?

JSX is a syntax extension that describes UI in JavaScript and compiles to React element creation.

### Props vs state?

Props are inputs from the parent. State is internal mutable data owned by the component.

### Why do we use keys?

To preserve identity during reconciliation and avoid incorrect remounts or state bugs.

### Function vs class component?

Function components are the modern default. Class components are mostly legacy.

### What is lifting state up?

Move shared state to the nearest common ancestor so multiple children can read and update it.

## What To Revise Next

Move to [reactjs-core-proficiency.md](reactjs-core-proficiency.md) for hooks, context, forms, and error handling.
