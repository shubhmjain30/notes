# React Interview Questions And Model Answers

This file is the fast-answer layer for the React notes. Use it after you finish the stage files or when you need a quick revision loop before an interview.

## How To Use This Cheat Sheet

- Read the question.
- Say the short answer out loud.
- Add one example from your own project experience.
- If the interviewer goes deeper, use the follow-up direction.

## Foundations

### What is React?

**Model answer:**
React is a JavaScript library for building user interfaces using a component-based, declarative model. You describe what the UI should look like for a given state, and React handles updating the DOM efficiently.

**Follow-up direction:**
Declarative UI, component tree, reconciliation.

### What is JSX?

**Model answer:**
JSX is a syntax extension that lets us write HTML-like UI syntax inside JavaScript. It compiles to React element creation calls, so it is not HTML and not a template language.

**Follow-up direction:**
Expressions in `{}`, `className`, single root element.

### Props vs state?

**Model answer:**
Props are read-only inputs passed from parent to child. State is internal data owned by a component and updated over time, usually from user interaction or async work.

**Follow-up direction:**
Unidirectional data flow, lifting state up.

### Why are keys important in lists?

**Model answer:**
Keys give list items stable identity so React can reconcile them correctly. Unstable keys can cause incorrect remounts, stale state, and unnecessary DOM work.

**Follow-up direction:**
Why index keys are risky for dynamic lists.

### Function components vs class components?

**Model answer:**
Function components are the modern default because Hooks give them state and side-effect capabilities without class syntax. Class components still matter in legacy codebases and for error boundaries.

**Follow-up direction:**
Hooks, lifecycle methods, maintenance of older apps.

## Hooks

### What problem do Hooks solve?

**Model answer:**
Hooks let function components use state, effects, refs, and reusable stateful logic without class components. They also make logic reuse easier through custom hooks.

**Follow-up direction:**
Custom hooks vs HOCs and render props.

### Why do Hooks have rules?

**Model answer:**
Hooks rely on consistent call order so React can associate each hook call with the right internal state slot. That is why hooks must run at the top level and only inside React components or custom hooks.

**Follow-up direction:**
What breaks when hooks are called conditionally.

### What is useEffect used for?

**Model answer:**
`useEffect` is used to synchronize a component with systems outside React, such as network requests, event listeners, timers, or browser APIs.

**Follow-up direction:**
Dependency arrays, cleanup, stale closures.

### What is the difference between useEffect and useLayoutEffect?

**Model answer:**
`useEffect` runs after the browser paints. `useLayoutEffect` runs after DOM mutations but before paint, so it is used when layout measurement or visual consistency matters.

**Follow-up direction:**
Why `useLayoutEffect` should be used sparingly.

### useState vs useReducer?

**Model answer:**
`useState` is better for simple local state. `useReducer` is better when updates are related, more complex, or easier to reason about as named actions.

**Follow-up direction:**
Action-driven state transitions, testability.

### When would you create a custom hook?

**Model answer:**
I create a custom hook when stateful logic repeats across components or a component becomes hard to read because business logic and UI are mixed together.

**Follow-up direction:**
Hooks reuse logic, not shared state instances.

## Rendering And Performance

### Why does a React component re-render?

**Model answer:**
A component re-renders when its state changes, its parent re-renders, its props change, or a context it consumes changes.

**Follow-up direction:**
Why parent renders can cascade through children.

### What is reconciliation?

**Model answer:**
Reconciliation is React's process for comparing the previous element tree with the next one and deciding what should change in the UI.

**Follow-up direction:**
Keys, element types, render vs commit phase.

### What is the difference between render phase and commit phase?

**Model answer:**
The render phase calculates what the next UI should look like. The commit phase applies those changes to the DOM and finalizes the update.

**Follow-up direction:**
Why render can be interrupted but commit cannot.

### useMemo vs useCallback?

**Model answer:**
`useMemo` memoizes a computed value. `useCallback` memoizes a function reference. I use them only when profiling or component behavior shows they solve a real problem.

**Follow-up direction:**
Overuse of memoization and mental overhead.

### What is React.memo?

**Model answer:**
`React.memo` memoizes a component so React can skip re-rendering it when its props are shallowly equal.

**Follow-up direction:**
Prop identity, stable callbacks, when memo is not worth it.

### What problem does Fiber solve?

**Model answer:**
Fiber makes React rendering interruptible and priority-aware, which helps React keep urgent UI interactions responsive even when rendering work is heavy.

**Follow-up direction:**
Lanes, transitions, Suspense.

## State Management

### Is Context a replacement for Redux?

**Model answer:**
No. Context is a way to distribute values through a subtree. Redux is a full state container architecture with predictable updates, tooling, middleware, and broader scaling patterns.

**Follow-up direction:**
Context plus useReducer vs RTK.

### When is Context enough?

**Model answer:**
Context is enough for lower-frequency shared values such as theme, auth session, locale, or feature flags, especially when the state is naturally scoped to one subtree.

**Follow-up direction:**
Why broad, frequently changing context values can hurt performance.

### Why Redux Toolkit over classic Redux?

**Model answer:**
Redux Toolkit keeps Redux's predictability and tooling but removes most of the boilerplate by using slices, Immer, async helpers, and better defaults.

**Follow-up direction:**
Why old Redux still matters in legacy apps and interviews.

### When would you use Zustand instead of Redux Toolkit?

**Model answer:**
I would consider Zustand when I want an external store with less boilerplate and simpler ergonomics, but I still need more structure than Context. If the app needs strong team-wide patterns, middleware-heavy workflows, or a very standardized architecture, RTK is usually the safer choice.

## Data Fetching

### How do you fetch data in React?

**Model answer:**
For simple cases I can fetch inside `useEffect`, but for production apps I prefer a dedicated data layer such as React Query, SWR, or framework-native fetching because caching, retries, loading states, and invalidation matter quickly.

**Follow-up direction:**
Server state vs client UI state.

### What is Suspense?

**Model answer:**
Suspense is a React mechanism for coordinating loading boundaries. It lets React show fallback UI while waiting for code or certain data integrations to become ready.

**Follow-up direction:**
Lazy loading, streaming, framework support.

## Forms And Events

### Controlled vs uncontrolled components?

**Model answer:**
Controlled components keep input state in React, which is better for validation and dynamic UI. Uncontrolled components let the DOM hold the current value, which can be simpler for basic forms.

**Follow-up direction:**
Tradeoff between control and boilerplate.

### What is synthetic event in React?

**Model answer:**
A synthetic event is React's cross-browser wrapper around native browser events. It gives a consistent event API across browsers.

**Follow-up direction:**
Event delegation and current React behavior.

## Reliability And Architecture

### What is an error boundary?

**Model answer:**
An error boundary catches render-time errors in a subtree and shows fallback UI instead of letting the whole app crash.

**Follow-up direction:**
What it does not catch: event handlers and arbitrary async errors.

### What are the most common React performance mistakes?

**Model answer:**
The biggest mistakes are over-centralizing state, overusing Context for frequently changing values, memoizing blindly, and skipping profiling before optimization.

**Follow-up direction:**
State colocation, profiler, context splitting.

### How would you structure a large React app?

**Model answer:**
I start with clear feature boundaries, keep transient UI state local, use Context selectively for shared low-frequency values, add an external store only when justified, and standardize data fetching, testing, and observability patterns early.

**Follow-up direction:**
Feature-based folders, design system, ownership boundaries.

## Lightning Round

### What does lifting state up mean?

Move shared state to the nearest common ancestor.

### Why can changing a key remount a component?

Because React treats a changed key as a different component identity.

### What does useRef do?

It stores a mutable value across renders without causing re-renders.

### Why can missing useEffect dependencies cause bugs?

Because the effect may read stale values from an older render.

### Why is React declarative?

Because you describe the desired UI for a state instead of imperatively mutating the DOM step by step.

## Best Practice For Practice

Take 10 questions from this file and answer each in:

1. One sentence.
2. Thirty seconds.
3. Two minutes with one real example.

That is usually enough to expose where the understanding is shallow.
