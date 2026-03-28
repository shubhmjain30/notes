# Advanced Frontend JavaScript

This file focuses on browser-side JavaScript topics that often appear in frontend interviews after the core language basics.

## TL;DR

Strong frontend JavaScript means understanding the DOM, browser rendering, event handling, and how JavaScript decisions affect perceived performance.

## DOM And Rendering Basics

### What To Know

- The DOM is a browser object model, not part of the language itself.
- DOM updates can trigger style recalculation, layout, and paint.
- Too many read-write layout cycles can hurt performance.

## Event Delegation

### TL;DR

Instead of attaching many listeners to many child nodes, attach one listener higher in the tree and inspect the event target.

### Why It Matters

- Less memory overhead
- Works well for dynamic content
- Simpler listener management

## Browser Storage

### What To Know

- `localStorage` persists across sessions
- `sessionStorage` lasts for a tab session
- IndexedDB is better for larger structured client storage

## Network And Browser APIs

### Core APIs To Know

- `fetch`
- `AbortController`
- `IntersectionObserver`
- `ResizeObserver`
- `requestAnimationFrame`

## Rendering Performance

### Good Rules

- Use `requestAnimationFrame` for animation work.
- Avoid expensive work inside scroll and resize listeners.
- Prefer CSS for simple visual transitions when possible.

## Frontend Interview Questions

### What is event delegation?

A pattern where a parent handles events for children by relying on event bubbling.

### Why can DOM work be expensive?

Because browser rendering may need style recalculation, layout, and paint after updates.

### When would you use AbortController?

To cancel in-flight fetch requests or other abortable browser operations.
