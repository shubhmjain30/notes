# Stage 3: Advanced Engineering

This file is the main interview guide for mid-level and senior React discussions. It focuses on tradeoffs, architecture, and the topics that usually separate surface-level React knowledge from production-grade reasoning.

## Checklist

- Performance profiling and memoization
- React 18 concurrent features
- State management strategies
- Data fetching patterns
- Code splitting and lazy loading
- Accessibility and testing strategy
- Common senior interview questions

## Performance And Re-Render Strategy

### TL;DR

Do not optimize blindly. First identify why a component re-renders, then decide whether memoization, state colocation, or architecture changes are justified.

### Why Components Re-Render

- State changed
- Props changed
- Parent re-rendered
- Consumed context changed

### Tools To Reach For

- React DevTools Profiler
- `React.memo`
- `useMemo`
- `useCallback`
- State colocation
- Context splitting

### Practical Rules

- Move state closer to where it is used.
- Memoize only after identifying an actual problem.
- Avoid creating fresh objects and functions in hot paths unless needed.
- Prefer simpler code over speculative optimization.

### 30-Second Interview Answer

I start by profiling. If re-renders are real and expensive, I first colocate state, then stabilize props or callbacks where necessary, and only then add memoization. I do not treat `useMemo` and `useCallback` as defaults.

## React 18 Concurrent Features

### TL;DR

Concurrent rendering lets React prioritize urgent work and keep the UI responsive during heavier updates.

### What To Know

- Automatic batching groups multiple updates together.
- `useTransition` marks non-urgent UI updates.
- `useDeferredValue` lets expensive derived UI lag behind urgent input.
- Suspense coordinates loading states for code or data boundaries.

### When Interviewers Ask This

Usually the real question is: how do you keep input responsive while rendering expensive UI?

### Example Scenarios

- Search input filtering a large list
- Tab switch that triggers heavy rendering
- Chart updates under interactive filters

### Good One-Liner

Urgent updates such as typing should feel immediate. Non-urgent rendering can be deferred with transitions so the UI stays responsive.

## State Management Strategies

This is the section interviewers often probe hardest because it reveals whether you can distinguish local state, shared state, and full application architecture.

### Core Idea First

Context is a value propagation mechanism. Redux is a state container architecture. Redux Toolkit is the modern, recommended way to write Redux.

### Quick Comparison

| Option               | What it is                                         | Best for                                        | Main downside                                 |
| -------------------- | -------------------------------------------------- | ----------------------------------------------- | --------------------------------------------- |
| Local state          | Component-owned state with hooks                   | Component-specific UI state                     | Hard to share widely                          |
| Context              | Share a value through a subtree                    | Theme, auth user, locale, feature flags         | Broad consumer re-renders if misused          |
| Context + useReducer | Shared subtree state with action-based updates     | Medium complexity state inside one feature area | Still lacks Redux-level tooling and ecosystem |
| Classic Redux        | Central store with actions, reducers, middleware   | Legacy apps and interview fundamentals          | Boilerplate-heavy                             |
| Redux Toolkit        | Modern Redux with slices, Immer, thunks, RTK Query | Large apps with complex shared state            | More structure than small apps need           |

### Context Vs Redux Vs Redux Toolkit

| Question             | Context                        | Classic Redux                              | Redux Toolkit                                 |
| -------------------- | ------------------------------ | ------------------------------------------ | --------------------------------------------- |
| Main job             | Share values down the tree     | Predictable global store                   | Same Redux model, modern developer experience |
| Update model         | Provider value updates         | Actions and reducers                       | Slices, reducers, async thunks, RTK Query     |
| Boilerplate          | Low                            | High                                       | Moderate and much lower than classic Redux    |
| DevTools             | Limited by default             | Strong Redux DevTools story                | Strong Redux DevTools story                   |
| Middleware           | No standard middleware model   | Strong middleware ecosystem                | Built-in modern Redux patterns                |
| Team scalability     | Good for simpler shared state  | Strong for large teams                     | Strong and preferred for large teams          |
| Recommendation today | Great for simple shared values | Learn it for legacy and interview concepts | Use this if you choose Redux                  |

### When Context Is Enough

Use Context when:

- The data is read by many components but updated infrequently.
- The value is naturally global to a subtree.
- You do not need advanced middleware, time-travel debugging, or complex cross-feature coordination.

Good examples:

- Theme
- Auth session
- Locale
- Design system preferences

### When Context Starts To Hurt

Do not force Context into cases where:

- State updates very frequently.
- Many unrelated consumers depend on one large provider value.
- Async workflows, optimistic updates, caching, or derived selectors become complex.
- Multiple teams need consistent architecture and debugging workflows.

### When Classic Redux Shows Up In Interviews

Classic Redux still matters because:

- Many production codebases still contain it.
- Interviewers want you to understand core Redux concepts.
- RTK makes more sense only if you first understand store, action, reducer, dispatch, immutability, and middleware.

### Why RTK Is Preferred Over Old Redux

Redux Toolkit keeps the Redux mental model but removes the worst ergonomics:

- `createSlice` replaces separate action-type and reducer boilerplate.
- Immer allows safe immutable updates with simpler syntax.
- `createAsyncThunk` standardizes async flows.
- RTK Query covers fetching and caching for many use cases.

### 30-Second Interview Answers

#### Is Context a replacement for Redux?

No. Context helps distribute values through the component tree, but it does not by itself provide the architecture, middleware model, selector patterns, and tooling that Redux offers.

#### When would you use Context instead of Redux?

For simpler shared state such as theme, auth session, or locale, especially when updates are not extremely frequent and the state is naturally scoped to one subtree.

#### Why prefer Redux Toolkit over classic Redux?

RTK keeps Redux's predictability and tooling but removes most of the boilerplate, which makes teams faster and code easier to maintain.

#### Why should I still learn old Redux?

Because legacy apps still use it and interviewers often ask about the underlying concepts even if they expect RTK in modern code.

### Minimal Code Comparison

#### Context + useReducer

```jsx
const TodoContext = createContext(null);

function todoReducer(state, action) {
	switch (action.type) {
		case "add":
			return [...state, action.payload];
		default:
			return state;
	}
}

function TodoProvider({ children }) {
	const [todos, dispatch] = useReducer(todoReducer, []);
	return (
		<TodoContext.Provider value={{ todos, dispatch }}>
			{children}
		</TodoContext.Provider>
	);
}
```

#### Classic Redux

```jsx
const ADD_TODO = "ADD_TODO";

function addTodo(payload) {
	return { type: ADD_TODO, payload };
}

function todoReducer(state = [], action) {
	switch (action.type) {
		case ADD_TODO:
			return [...state, action.payload];
		default:
			return state;
	}
}
```

#### Redux Toolkit

```jsx
const todosSlice = createSlice({
	name: "todos",
	initialState: [],
	reducers: {
		addTodo(state, action) {
			state.push(action.payload);
		},
	},
});

export const { addTodo } = todosSlice.actions;
```

### Short Note On Other Stores

- Zustand is useful when you want a simpler external store with less boilerplate.
- Jotai and Recoil focus on more fine-grained atomic state models.
- These are worth knowing, but interviewers are more likely to compare Context with Redux or RTK first.

## Data Fetching Patterns

### TL;DR

The modern question is not just how to fetch data, but where caching, retries, loading states, and invalidation should live.

### What To Know

- Raw `useEffect` works, but it is low-level.
- React Query and SWR are better for caching, retries, invalidation, and request lifecycle management.
- Server Components and framework data APIs change where fetching happens in modern React stacks.

### Interview Answer

For simple component-local fetches, `useEffect` can be enough. For serious apps, I prefer a data-fetching library or framework-native data layer because caching and invalidation quickly become a real concern.

## Code Splitting And Lazy Loading

### TL;DR

Load less JavaScript up front, especially for routes or heavy optional features.

### What To Know

- Use `React.lazy` for component-level code splitting.
- Use Suspense for fallback UI.
- Route-level splitting is often the best first move.

### Example

```jsx
const SettingsPage = React.lazy(() => import("./SettingsPage"));

function App() {
	return (
		<Suspense fallback={<p>Loading...</p>}>
			<SettingsPage />
		</Suspense>
	);
}
```

## Accessibility And Testing Strategy

### Accessibility

Know these basics:

- Semantic HTML first
- Keyboard navigation
- Focus management
- Correct labels and ARIA only when necessary

### Testing

Know how to explain:

- Unit vs integration vs e2e tests
- Why React Testing Library focuses on behavior
- Why brittle implementation-detail tests are expensive

## Senior-Level Interview Prompts

### How do you decide between Context and Redux Toolkit?

I start with scope, update frequency, and workflow complexity. For simple shared values, Context is enough. For large cross-feature state with async flows, selectors, and stronger debugging needs, RTK is the better default.

### How do you improve React performance?

First profile, then reduce unnecessary renders by colocating state, splitting context, memoizing only where justified, and deferring non-urgent work when the UI becomes sluggish.

### How would you fetch data in a production React app?

I prefer a dedicated data layer such as React Query, SWR, or framework-native fetching because it handles caching, retries, and invalidation better than ad hoc `useEffect` code.

## What To Revise Next

Read [reactjs-design-patterns.md](reactjs-design-patterns.md) for pattern tradeoffs and [reactjs-fiber-structure.md](reactjs-fiber-structure.md) for React internals.
