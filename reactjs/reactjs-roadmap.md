# React.js Interview Roadmap

Interview-focused notes for React fundamentals, hooks, patterns, performance, Fiber, and large-scale architecture.

This folder is designed for preparation, not passive reading. Use it to revise quickly, study in a sane order, and build strong answers around rendering, state ownership, performance, and architecture tradeoffs.

## What This Folder Covers

- Components, props, state, JSX, and rendering basics
- Hooks, context, forms, refs, and custom hooks
- Design patterns, testing basics, and state management tradeoffs
- Performance, data fetching, and React 18 behavior
- Fiber, reconciliation, server components, and large-scale architecture

## How To Use This Folder

1. Start with this roadmap.
2. Read the canonical topic notes in order.
3. Prioritize the highest-yield topics if time is limited.
4. Finish with the cheat sheet before interviews.
5. Practice giving short spoken answers for each core concept.

## File Map

| File                                                                 | Purpose                                                              | When to Read          |
| -------------------------------------------------------------------- | -------------------------------------------------------------------- | --------------------- |
| [reactjs-foundations.md](reactjs-foundations.md)                     | React basics and entry-level interview prep                          | Start here            |
| [reactjs-core-proficiency.md](reactjs-core-proficiency.md)           | Hooks, context, forms, refs, testing basics                          | After foundations     |
| [reactjs-design-patterns.md](reactjs-design-patterns.md)             | Most common React design patterns with tradeoffs                     | After core            |
| [reactjs-advanced-engineering.md](reactjs-advanced-engineering.md)   | Performance, state management, data fetching, architecture tradeoffs | Mid to senior prep    |
| [reactjs-fiber-structure.md](reactjs-fiber-structure.md)             | Fiber and reconciliation interview essentials                        | Senior prep           |
| [reactjs-expert-architectural.md](reactjs-expert-architectural.md)   | Server components, large-scale architecture, migrations              | Senior and staff prep |
| [reactjs-interview-cheat-sheet.md](reactjs-interview-cheat-sheet.md) | Rapid-fire interview questions and model answers                     | Before interviews     |

## Reading Order

1. [reactjs-foundations.md](reactjs-foundations.md)
2. [reactjs-core-proficiency.md](reactjs-core-proficiency.md)
3. [reactjs-design-patterns.md](reactjs-design-patterns.md)
4. [reactjs-advanced-engineering.md](reactjs-advanced-engineering.md)
5. [reactjs-fiber-structure.md](reactjs-fiber-structure.md)
6. [reactjs-expert-architectural.md](reactjs-expert-architectural.md)
7. [reactjs-interview-cheat-sheet.md](reactjs-interview-cheat-sheet.md)

## Highest-Yield Topics

If time is limited, prioritize these in order:

1. Props vs state
2. useState and useEffect
3. Controlled vs uncontrolled forms
4. Lists and keys
5. Context API basics
6. Custom hooks
7. Memoization and re-renders
8. Context vs Redux vs RTK
9. Error boundaries
10. Reconciliation and Fiber basics

## Interview Buckets

### Junior

- What is JSX?
- What is the difference between props and state?
- Why do we use keys in lists?
- What is lifting state up?
- What does useEffect do?

### Mid-level

- When should you create a custom hook?
- When should you use context?
- How do you avoid unnecessary re-renders?
- Controlled vs uncontrolled forms?
- How do you fetch data safely in React?

### Senior

- Is Context a replacement for Redux?
- Why is Redux Toolkit preferred over old Redux?
- How does reconciliation work?
- What problem did Fiber solve?
- How would you structure state in a large React app?

## Suggested Preparation Plans

### 2-Hour Refresh

- Read [reactjs-foundations.md](reactjs-foundations.md)
- Read [reactjs-core-proficiency.md](reactjs-core-proficiency.md)
- Review [reactjs-design-patterns.md](reactjs-design-patterns.md)
- Read performance and state sections in [reactjs-advanced-engineering.md](reactjs-advanced-engineering.md)
- Finish with [reactjs-interview-cheat-sheet.md](reactjs-interview-cheat-sheet.md)

### 1-Day Revision

- Cover foundations first: JSX, props, state, lists, event handling
- Review hooks, context, forms, refs, and custom hooks
- Revisit patterns, performance, and state management tradeoffs
- Finish with Fiber basics and architecture notes
- End with [reactjs-interview-cheat-sheet.md](reactjs-interview-cheat-sheet.md)

## Best Way To Get Interview Value From This Folder

- Do not treat React as only a hooks checklist; explain rendering and state ownership clearly
- Prefer tradeoffs over slogans when discussing context, Redux, and performance
- Use Fiber and reconciliation notes to strengthen senior-level answers
- Re-read only the topics you miss in mock interviews

## Final Advice

Start from the roadmap, then use the topic files as canonical references instead of repeating the same concept across multiple notes.
