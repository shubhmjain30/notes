# Fiber And Reconciliation Essentials

This is the short version you should revise before a senior React interview. It covers the high-yield internals and avoids the research-paper detail.

## TL;DR

React creates element trees, compares the previous tree with the next one, and updates the DOM efficiently. Fiber is the internal architecture that makes this work interruptible, priority-aware, and compatible with modern features such as transitions and Suspense.

## Why React Needed Fiber

Before Fiber, React used a synchronous stack reconciler. That model had a major limitation:

- Once rendering started, React had to finish it before giving control back.

That meant expensive renders could block user input, animation, and other urgent work. Fiber changed this by breaking rendering into units of work that React can pause, resume, or abandon.

## Reconciliation Basics

### What Reconciliation Means

Reconciliation is React's process for comparing the old element tree with the new one and deciding what changes to apply.

### Two Rules To Remember

1. Different element types usually cause React to throw away the old subtree and mount a new one.
2. Keys help React preserve identity among siblings during list reconciliation.

### Why Keys Matter

Stable keys let React preserve the right component instance. Unstable keys can cause state bugs, unnecessary remounts, and incorrect UI reuse.

### Interview One-Liner

Reconciliation is the diffing process React uses to decide the smallest practical set of UI changes, and keys are how we help React preserve identity in lists.

## Render Phase Vs Commit Phase

### Render Phase

During render, React calculates what the UI should look like next.

Key properties:

- Can be interrupted
- Can be restarted
- Does not mutate the DOM directly

### Commit Phase

During commit, React applies the prepared changes.

Key properties:

- Must run to completion
- Updates the DOM
- Runs layout and passive effects at the appropriate times

### Interview One-Liner

Render decides what should change. Commit applies those changes to the real UI.

## Lanes And Priority

Lanes are React's way of assigning priority to updates.

### Why This Exists

Not every update is equally urgent.

Examples:

- Typing into an input is urgent.
- Recomputing a large filtered list can be less urgent.

### What This Enables

- Responsive typing and clicking
- Transitions for non-urgent UI updates
- Better scheduling under heavy rendering load

### Interview One-Liner

Lanes let React prioritize urgent updates over less important rendering work so the interface stays responsive.

## How Fiber Enables Modern Features

Fiber is the foundation for:

- `useTransition`
- `useDeferredValue`
- Suspense
- Better scheduling in React 18

Without an interruptible reconciler, these features would be far harder to implement well.

## Key Identity And Forced Remounts

Changing a component's key tells React that it is a different instance.

That means:

- Old state is discarded
- Effects clean up
- A fresh instance mounts

This can be a bug if accidental, but it can also be a useful reset technique when intentional.

## Common Senior Interview Questions

### What problem did Fiber solve?

Fiber solved the problem of uninterruptible rendering by making React's reconciliation work incremental and priority-aware.

### Why are keys important?

Keys preserve identity among siblings so React can reconcile lists correctly and avoid state bugs.

### What is the difference between render and commit?

Render computes the next tree. Commit applies the changes to the DOM and finalizes the update.

### How do transitions relate to Fiber?

Transitions rely on React being able to treat some work as less urgent. Fiber and lanes make that scheduling possible.

## What To Revise Next

Use [reactjs-advanced-engineering.md](reactjs-advanced-engineering.md) for performance strategy and [reactjs-expert-architectural.md](reactjs-expert-architectural.md) for broader architecture tradeoffs.
