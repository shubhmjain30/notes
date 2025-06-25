# A Deep Dive into React's Internals

Understanding how React works under the hood helps you write more efficient code and debug complex issues. This section explores React's internal mechanisms and rendering process.

[[reactjs-roadmap|← Back to React.js Roadmap]]

## JSX and React.createElement

**Problem:** How does JSX get transformed into actual JavaScript that the browser can understand?

**Theory:** JSX is syntactic sugar that gets transpiled to `React.createElement()` calls during the build process.

```jsx
// JSX code you write
function Welcome() {
	return (
		<div className="greeting">
			<h1>Hello, world!</h1>
			<p>Welcome to React</p>
		</div>
	);
}

// What it compiles to after transpilation
function Welcome() {
	return React.createElement(
		"div",
		{ className: "greeting" },
		React.createElement("h1", null, "Hello, world!"),
		React.createElement("p", null, "Welcome to React")
	);
}
```

**The createElement function:**

```jsx
// Simplified version of React.createElement
function createElement(type, props, ...children) {
	return {
		type,
		props: {
			...props,
			children: children.map((child) =>
				typeof child === "object" ? child : createTextElement(child)
			),
		},
	};
}

function createTextElement(text) {
	return {
		type: "TEXT_ELEMENT",
		props: {
			nodeValue: text,
			children: [],
		},
	};
}
```

## The Virtual DOM

**Problem:** How can we update the UI efficiently without manually manipulating the DOM, which is slow and error-prone?

**Theory:** The Virtual DOM is a lightweight copy of the real DOM kept in memory. React uses it to determine what changes need to be made to the actual DOM.

**Key concepts:**

-   React elements are plain JavaScript objects that represent DOM elements
-   The Virtual DOM is a tree of these objects
-   React can compare two Virtual DOM trees quickly to find differences
-   Only the necessary changes are applied to the real DOM

```jsx
// A React element (Virtual DOM node) looks something like this
const element = {
	type: "div",
	props: {
		className: "container",
		children: [
			{
				type: "h1",
				props: {
					children: "Hello World",
				},
			},
			{
				type: "p",
				props: {
					children: "This is a paragraph.",
				},
			},
		],
	},
};
```

## Reconciliation and Diffing

**Problem:** How does React determine what has changed between renders and update the DOM efficiently?

**Theory:** Reconciliation is the process of comparing the previous Virtual DOM tree with the new one to determine what changes need to be made to the real DOM.

**Key algorithms and heuristics:**

1. **Different element types:** If the element type changes (e.g., from `<div>` to `<span>`), React tears down the old tree and builds a new one.

2. **Same element type:** If the element type is the same, React keeps the same DOM node and only updates the changed attributes.

3. **List elements:** When rendering lists, React uses the `key` prop to identify which items have changed, been added, or been removed.

```jsx
// Without keys, inefficient re-rendering
function ListWithoutKeys() {
	const [items, setItems] = useState(["Apple", "Banana", "Cherry"]);

	// Adding an item to the beginning forces React to re-render all items
	// because it can't identify which items stayed the same
	const addItem = () => {
		setItems(["Date", ...items]);
	};

	return (
		<ul>
			{items.map((item, index) => (
				<li key={index}>{item}</li> // Using index as key is problematic when list order changes
			))}
		</ul>
	);
}

// With stable keys, efficient re-rendering
function ListWithKeys() {
	const [items, setItems] = useState([
		{ id: 1, name: "Apple" },
		{ id: 2, name: "Banana" },
		{ id: 3, name: "Cherry" },
	]);

	// React can identify that existing items didn't change
	// and only needs to add the new item
	const addItem = () => {
		setItems([{ id: Date.now(), name: "Date" }, ...items]);
	};

	return (
		<ul>
			{items.map((item) => (
				<li key={item.id}>{item.name}</li> // Stable, unique key
			))}
		</ul>
	);
}
```

## The Fiber Architecture

**Problem:** How can React break up the rendering work into small chunks to avoid blocking the main thread during large updates?

**Theory:** React Fiber is a reimplementation of React's core algorithm introduced in React 16. It enables incremental rendering: the ability to split rendering work into chunks and spread it out over multiple frames.

**Key concepts:**

1. **Fiber nodes:** Each React element has a corresponding Fiber node that tracks:

    - The element type
    - Its DOM node
    - Its relationships to other Fibers (child, sibling, parent)
    - Work that needs to be done

2. **Work phases:**

    - **Render/Reconciliation phase:** Can be interrupted, determines what changes need to be made
    - **Commit phase:** Uninterruptible, applies changes to the DOM

3. **Priorities:** Different updates can have different priorities:
    - User interactions (high priority)
    - Data fetching (medium priority)
    - Background work (low priority)

```jsx
// Simplified representation of a Fiber node
const fiber = {
	// Instance
	type: "div",
	stateNode: domNode, // Reference to the actual DOM node

	// Fiber relationships
	child: childFiber,
	sibling: siblingFiber,
	return: parentFiber,

	// Effects
	effectTag: "UPDATE",
	nextEffect: nextFiberWithEffect,

	// State
	memoizedState: previousState,
	updateQueue: pendingUpdates,

	// Additional metadata
	alternate: previousFiberTree,
	key: "unique-key",
	lanes: priorityLevel,
};
```

## How State and Hooks Work Internally

**Problem:** How does React keep track of state and hook calls between renders?

**Theory:** React maintains a linked list of hooks for each component, which is how it associates hook calls with their state between renders.

**Key concepts:**

1. **Hook call order:** Hooks must be called in the same order on every render, which is why they can't be inside conditions or loops.

2. **Current fiber:** React keeps track of the currently rendering component's Fiber.

3. **Hook linked list:** Each Fiber has a linked list of hooks, with each node containing:
    - The hook's state
    - A queue of pending updates
    - A reference to the next hook

```jsx
// Simplified implementation of useState
function useState(initialState) {
	// Get the current hook from the current fiber
	const hook = getCurrentHook();

	// Initialize state if this is the first render
	if (isFirstRender) {
		hook.memoizedState =
			typeof initialState === "function" ? initialState() : initialState;
	}

	// Create a state setter function that's bound to this hook
	const setState = (newState) => {
		// Queue the update
		hook.queue.push(
			typeof newState === "function" ? newState : () => newState
		);

		// Schedule a re-render
		scheduleUpdate();
	};

	// Return the current state and the setter
	return [hook.memoizedState, setState];
}

// How hooks are stored in a component's fiber
const fiber = {
	// ...other fiber properties

	// Linked list of hooks
	memoizedState: {
		// First hook (useState)
		memoizedState: 0, // The state value
		queue: [], // Pending updates
		next: {
			// Second hook (useEffect)
			memoizedState: {
				deps: [prop1, prop2],
				cleanup: previousEffectCleanup,
			},
			next: {
				// Third hook (useRef)
				memoizedState: { current: refValue },
				next: null,
			},
		},
	},
};
```

**Hook execution flow:**

1. Component renders, calling hooks in sequence
2. Each hook call retrieves the corresponding hook from the linked list
3. After render, React processes effects in the commit phase
4. When state updates, React schedules a re-render
5. On next render, the hook list is traversed again in the same order

Understanding React's internals helps you:

-   Debug complex issues
-   Optimize performance
-   Make better architectural decisions
-   Appreciate the trade-offs in React's design

While you don't need to understand all these details to use React effectively, this knowledge can be valuable when working on complex applications or when you need to optimize performance.
