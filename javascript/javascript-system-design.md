[[javascript-roadmap.md]]

# Approaching Frontend System Design Interviews

## Introduction

This section provides a structured approach to frontend system design interviews, focusing on requirements exploration, trade-offs, and communication of design decisions for complex applications.

### Knowledge Points

#### Requirements Exploration

Start by clarifying the scope and requirements of the system:

```javascript
// Example: Designing a social media news feed
// Questions to ask:

// 1. Functional Requirements
// - What types of content should the feed display? (text, images, videos)
// - Should it support infinite scrolling or pagination?
// - Do we need real-time updates?
// - Are comments, likes, and shares required?

// 2. Non-Functional Requirements
// - What's the expected user base size?
// - What devices/browsers need to be supported?
// - What are the performance expectations? (e.g., load time < 2s)
// - Do we need to consider accessibility (a11y)?
// - Internationalization (i18n) requirements?

// 3. Technical Constraints
// - Are there specific technologies we must use?
// - What APIs are available for data fetching?
// - Are there bandwidth or payload size limitations?
```

#### System Design Frameworks

Use a structured approach to design frontend systems:

```javascript
// 1. Component Hierarchy
// Break down the UI into components
const newsFeedComponents = {
	layout: {
		header: { logo, searchBar, navigationMenu },
		sidebar: { userProfile, trending, suggestions },
		main: { postComposer, feedList },
		footer: { links, copyright },
	},
	feedList: {
		post: { header, content, actions, comments },
	},
};

// 2. Data Model
// Define the core data structures
const dataModels = {
	user: {
		id: "string",
		name: "string",
		profilePicture: "string",
		// ...other properties
	},
	post: {
		id: "string",
		author: "User",
		content: "string",
		media: "Array<Media>",
		likes: "number",
		comments: "Array<Comment>",
		createdAt: "Date",
	},
	// ...other models
};

// 3. State Management
// Choose appropriate state management based on complexity
const stateManagementOptions = {
	simple: "React useState/useReducer for component-level state",
	medium: "React Context API for sharing state across components",
	complex: "Redux/MobX for global state with complex interactions",
	realTime: "Consider state sync libraries like SWR or React Query",
};

// 4. API Design
// Define the contract between frontend and backend
const apiEndpoints = {
	getFeed: {
		url: "/api/feed",
		method: "GET",
		params: { page: "number", limit: "number" },
		response: "Array<Post>",
	},
	createPost: {
		url: "/api/posts",
		method: "POST",
		body: { content: "string", mediaIds: "Array<string>" },
		response: "Post",
	},
	// ...other endpoints
};

// 5. Data Flow
// Diagram how data flows through the system
const dataFlow = `
  User Interaction → Component Event Handler → 
  State Update → API Call (if needed) → 
  Update Local State → Re-render Components
`;
```

#### Performance Optimization in System Design

Address performance concerns in your design:

```javascript
// 1. Initial Load Performance
const initialLoadStrategies = {
	// Code splitting
	lazyLoadComponents: `
    const ProfilePage = React.lazy(() => import('./ProfilePage'));
  `,

	// Server-side rendering
	ssr: `
    // Next.js example
    export async function getServerSideProps() {
      const posts = await fetchInitialPosts();
      return { props: { posts } };
    }
  `,

	// Critical CSS
	criticalCSS: "Inline critical styles in <head> for fast rendering",
};

// 2. Runtime Performance
const runtimeOptimizations = {
	// Virtualized lists for large datasets
	virtualization: `
    import { FixedSizeList } from 'react-window';
    
    function VirtualizedFeed({ items }) {
      return (
        <FixedSizeList
          height={500}
          width="100%"
          itemCount={items.length}
          itemSize={120}
        >
          {({ index, style }) => (
            <div style={style}>
              <Post post={items[index]} />
            </div>
          )}
        </FixedSizeList>
      );
    }
  `,

	// Memoization for expensive calculations
	memoization: `
    const MemoizedComponent = React.memo(ExpensiveComponent);
    const cachedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
  `,

	// Debouncing user input
	debounce: `
    const debouncedSearch = debounce(searchAPI, 300);
  `,
};

// 3. Network Optimization
const networkStrategies = {
	// Data fetching strategies
	dataFetching: {
		pagination: "Fetch limited data sets (e.g., 20 items per page)",
		infiniteScroll: "Load more data as user scrolls near the bottom",
		prefetching: "Anticipate user actions and fetch data in advance",
	},

	// Caching
	caching: {
		httpCache: "Set proper Cache-Control headers",
		serviceWorker: "Cache assets and API responses for offline use",
		stateCache: "Use SWR or React Query for client-side cache",
	},
};

// 4. Perceived Performance
const perceivedPerformance = {
	// Show content progressively
	skeleton: "Display skeleton screens instead of spinners",
	optimisticUI: "Update UI immediately before server confirmation",
	progressiveLoading: "Load and display critical content first",
};
```
