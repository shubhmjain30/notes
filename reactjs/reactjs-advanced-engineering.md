# 🚀 Stage 3: Advanced Engineering (Upper-Intermediate)

[[README|← Back to React.js Interview Prep Checklist]]

Master advanced React patterns, performance optimization, and production-ready techniques. This stage prepares you for senior-level development.

---

## ✅ Checklist: Advanced Engineering

- [ ] **Performance Optimization** – React 18 concurrent features, useTransition, useDeferredValue
- [ ] **Memoization & Re-render Profiling** – React.memo, useCallback, DevTools Profiler
- [ ] **State Management Strategies** – Redux Toolkit, Zustand, Jotai, Recoil, Context vs. external stores
- [ ] **Server-Side Rendering (SSR) & Static Generation** – Next 13/14 App Router, streaming, partial hydrations
- [ ] **Data Fetching Patterns** – React Query / SWR, suspense for data, error & loading states
- [ ] **Code-Splitting & Lazy Loading** – dynamic imports, route-level splits, webpack vs. Vite nuances
- [ ] **Accessibility (a11y)** – ARIA roles, focus management, keyboard navigation
- [ ] **Internationalization (i18n)** – next-i18next, message extraction, locale routing
- [ ] **Theming & Design Systems** – styled-components, Emotion, Tailwind/Chakra, Storybook
- [ ] **Testing at Scale** – component vs. integration vs. e2e (Cypress/Playwright)

---

## Performance Optimization

### React 18 Concurrent Features

**Problem:** How do we keep the UI responsive during heavy computational work or when handling large datasets?

**Theory:** React 18 introduces concurrent rendering, which allows React to pause, resume, or abandon work to keep the UI responsive.

#### useTransition for Non-Urgent Updates

```jsx
import { useState, useTransition, useDeferredValue } from 'react';

function SearchableList({ items }) {
  const [query, setQuery] = useState('');
  const [isPending, startTransition] = useTransition();
  
  // Deferred value for expensive filtering
  const deferredQuery = useDeferredValue(query);
  
  // Filter items based on deferred query (non-urgent)
  const filteredItems = useMemo(() => {
    if (!deferredQuery) return items;
    
    return items.filter(item =>
      item.name.toLowerCase().includes(deferredQuery.toLowerCase()) ||
      item.description.toLowerCase().includes(deferredQuery.toLowerCase())
    );
  }, [items, deferredQuery]);

  // Handle input change with transition
  const handleSearchChange = (e) => {
    const value = e.target.value;
    
    // Update input immediately (urgent)
    setQuery(value);
    
    // Defer the expensive filtering operation (non-urgent)
    startTransition(() => {
      // Any state updates here will be marked as non-urgent
      // React can interrupt this work to handle more urgent updates
    });
  };

  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={handleSearchChange}
        placeholder="Search items..."
        className={isPending ? 'searching' : ''}
      />
      
      {isPending && <div className="loading-indicator">Searching...</div>}
      
      <div className="results">
        {filteredItems.map(item => (
          <ItemCard key={item.id} item={item} />
        ))}
      </div>
    </div>
  );
}
```

#### Concurrent Features Deep Dive

```jsx
// Advanced concurrent patterns
function DataDashboard() {
  const [activeTab, setActiveTab] = useState('overview');
  const [isPending, startTransition] = useTransition();
  
  // Different data for each tab
  const [overviewData, setOverviewData] = useState(null);
  const [analyticsData, setAnalyticsData] = useState(null);
  const [reportsData, setReportsData] = useState(null);

  // Switch tabs with transition to avoid blocking
  const handleTabChange = (newTab) => {
    startTransition(() => {
      setActiveTab(newTab);
      
      // Load data for the new tab if not already loaded
      if (newTab === 'analytics' && !analyticsData) {
        loadAnalyticsData().then(setAnalyticsData);
      } else if (newTab === 'reports' && !reportsData) {
        loadReportsData().then(setReportsData);
      }
    });
  };

  return (
    <div className="dashboard">
      <nav className="tabs">
        {['overview', 'analytics', 'reports'].map(tab => (
          <button
            key={tab}
            onClick={() => handleTabChange(tab)}
            className={`tab ${activeTab === tab ? 'active' : ''} ${isPending ? 'loading' : ''}`}
          >
            {tab.charAt(0).toUpperCase() + tab.slice(1)}
          </button>
        ))}
      </nav>

      <div className="tab-content">
        <Suspense fallback={<TabSkeleton />}>
          {activeTab === 'overview' && <OverviewTab data={overviewData} />}
          {activeTab === 'analytics' && <AnalyticsTab data={analyticsData} />}
          {activeTab === 'reports' && <ReportsTab data={reportsData} />}
        </Suspense>
      </div>
    </div>
  );
}

// Component that uses useDeferredValue for responsive updates
function LiveChart({ data, filters }) {
  const deferredFilters = useDeferredValue(filters);
  
  // Expensive chart calculation
  const processedData = useMemo(() => {
    console.log('Processing chart data...');
    return data
      .filter(point => {
        return (!deferredFilters.dateRange || 
               isDateInRange(point.date, deferredFilters.dateRange)) &&
               (!deferredFilters.category || 
               point.category === deferredFilters.category);
      })
      .map(point => ({
        ...point,
        normalizedValue: normalizeValue(point.value, data)
      }));
  }, [data, deferredFilters]);

  return (
    <div className="chart-container">
      {/* Chart renders with deferred data, allowing UI to stay responsive */}
      <Chart data={processedData} />
      
      {/* Show loading state when filters are ahead of deferred filters */}
      {filters !== deferredFilters && (
        <div className="chart-overlay">
          <div className="updating-indicator">Updating chart...</div>
        </div>
      )}
    </div>
  );
}
```

### Memoization & Re-render Profiling

#### React.memo and Component Optimization

**Problem:** How do we prevent unnecessary re-renders in component trees?

```jsx
// ❌ Component that re-renders unnecessarily
function ExpensiveComponent({ data, onUpdate, config }) {
  console.log('ExpensiveComponent rendering');
  
  // Expensive computation on every render
  const processedData = data.map(item => ({
    ...item,
    computed: performExpensiveCalculation(item, config)
  }));

  return (
    <div>
      {processedData.map(item => (
        <div key={item.id}>{item.computed}</div>
      ))}
    </div>
  );
}

// ✅ Optimized with React.memo and proper memoization
const OptimizedComponent = React.memo(function OptimizedComponent({ 
  data, 
  onUpdate, 
  config 
}) {
  console.log('OptimizedComponent rendering');
  
  // Memoize expensive computation
  const processedData = useMemo(() => {
    console.log('Computing processed data...');
    return data.map(item => ({
      ...item,
      computed: performExpensiveCalculation(item, config)
    }));
  }, [data, config]);

  // Memoize callback to prevent child re-renders
  const handleItemUpdate = useCallback((itemId, updates) => {
    onUpdate(itemId, updates);
  }, [onUpdate]);

  return (
    <div>
      {processedData.map(item => (
        <OptimizedItem
          key={item.id}
          item={item}
          onUpdate={handleItemUpdate}
        />
      ))}
    </div>
  );
});

// Child component with React.memo
const OptimizedItem = React.memo(function OptimizedItem({ item, onUpdate }) {
  console.log(`Rendering item ${item.id}`);
  
  const handleClick = useCallback(() => {
    onUpdate(item.id, { lastClicked: Date.now() });
  }, [item.id, onUpdate]);

  return (
    <div onClick={handleClick}>
      {item.computed}
    </div>
  );
});
```

#### useCallback Deep Dive

```jsx
function ParentComponent() {
  const [count, setCount] = useState(0);
  const [items, setItems] = useState([]);
  const [filter, setFilter] = useState('');

  // ❌ Function recreated on every render
  const handleItemClick = (itemId) => {
    console.log('Item clicked:', itemId);
    // Some logic here
  };

  // ✅ Memoized function
  const handleItemClickMemo = useCallback((itemId) => {
    console.log('Item clicked:', itemId);
    // Some logic here
  }, []); // No dependencies, function never changes

  // ✅ Memoized function with dependencies
  const handleItemUpdate = useCallback((itemId, updates) => {
    setItems(prevItems => 
      prevItems.map(item => 
        item.id === itemId ? { ...item, ...updates } : item
      )
    );
  }, []);

  // ✅ Complex callback with proper dependencies
  const handleFilteredAction = useCallback((action) => {
    const filteredItems = items.filter(item => 
      item.name.toLowerCase().includes(filter.toLowerCase())
    );
    
    switch (action) {
      case 'delete':
        setItems(prevItems => 
          prevItems.filter(item => !filteredItems.includes(item))
        );
        break;
      case 'export':
        exportItems(filteredItems);
        break;
    }
  }, [items, filter]);

  return (
    <div>
      <div>Count: {count}</div>
      <button onClick={() => setCount(c => c + 1)}>Increment</button>
      
      <input
        value={filter}
        onChange={(e) => setFilter(e.target.value)}
        placeholder="Filter items..."
      />
      
      <ItemList
        items={items}
        onItemClick={handleItemClickMemo}
        onItemUpdate={handleItemUpdate}
        onFilteredAction={handleFilteredAction}
      />
    </div>
  );
}
```

#### Performance Profiling with React DevTools

```jsx
// Component to demonstrate profiling
function PerformanceDemo() {
  const [renderCount, setRenderCount] = useState(0);
  const [heavyData, setHeavyData] = useState(generateHeavyData());

  // Force re-render to see in profiler
  const forceRender = () => setRenderCount(c => c + 1);

  return (
    <div>
      <div>Render count: {renderCount}</div>
      <button onClick={forceRender}>Force Re-render</button>
      
      {/* These will show in profiler */}
      <ExpensiveList data={heavyData} />
      <AnotherExpensiveComponent />
    </div>
  );
}

// Profiling techniques:
// 1. Open React DevTools Profiler tab
// 2. Click "Start profiling"
// 3. Interact with the app
// 4. Click "Stop profiling"
// 5. Analyze the flame graph:
//    - Component render times
//    - What triggered re-renders
//    - Component that didn't re-render (grayed out)

function ExpensiveList({ data }) {
  // Add console.log to track renders
  console.log('ExpensiveList rendered');
  
  // Simulate expensive work
  const processedData = useMemo(() => {
    const start = performance.now();
    const result = data.map(item => ({
      ...item,
      processed: item.value * 2
    }));
    const end = performance.now();
    console.log(`Processing took ${end - start} milliseconds`);
    return result;
  }, [data]);

  return (
    <ul>
      {processedData.map(item => (
        <li key={item.id}>{item.processed}</li>
      ))}
    </ul>
  );
}
```

---

## State Management Strategies

### Redux Toolkit (RTK) - Enterprise Scale

**Problem:** How do we manage complex, global state in large applications with predictable updates?

```jsx
// store/userSlice.js
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

// Async thunk for API calls
export const fetchUser = createAsyncThunk(
  'user/fetchUser',
  async (userId, { rejectWithValue }) => {
    try {
      const response = await fetch(`/api/users/${userId}`);
      if (!response.ok) {
        throw new Error('Failed to fetch user');
      }
      return await response.json();
    } catch (error) {
      return rejectWithValue(error.message);
    }
  }
);

export const updateUser = createAsyncThunk(
  'user/updateUser',
  async ({ userId, updates }, { rejectWithValue }) => {
    try {
      const response = await fetch(`/api/users/${userId}`, {
        method: 'PATCH',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(updates)
      });
      if (!response.ok) throw new Error('Update failed');
      return await response.json();
    } catch (error) {
      return rejectWithValue(error.message);
    }
  }
);

const userSlice = createSlice({
  name: 'user',
  initialState: {
    currentUser: null,
    users: {},
    loading: false,
    error: null
  },
  reducers: {
    clearError: (state) => {
      state.error = null;
    },
    setCurrentUser: (state, action) => {
      state.currentUser = action.payload;
    },
    logoutUser: (state) => {
      state.currentUser = null;
      state.users = {};
    }
  },
  extraReducers: (builder) => {
    builder
      // Fetch user
      .addCase(fetchUser.pending, (state) => {
        state.loading = true;
        state.error = null;
      })
      .addCase(fetchUser.fulfilled, (state, action) => {
        state.loading = false;
        state.users[action.payload.id] = action.payload;
        if (!state.currentUser) {
          state.currentUser = action.payload;
        }
      })
      .addCase(fetchUser.rejected, (state, action) => {
        state.loading = false;
        state.error = action.payload;
      })
      
      // Update user
      .addCase(updateUser.fulfilled, (state, action) => {
        const updatedUser = action.payload;
        state.users[updatedUser.id] = updatedUser;
        if (state.currentUser?.id === updatedUser.id) {
          state.currentUser = updatedUser;
        }
      });
  }
});

export const { clearError, setCurrentUser, logoutUser } = userSlice.actions;
export default userSlice.reducer;

// store/index.js
import { configureStore } from '@reduxjs/toolkit';
import userReducer from './userSlice';
import postsReducer from './postsSlice';

export const store = configureStore({
  reducer: {
    user: userReducer,
    posts: postsReducer
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({
      serializableCheck: {
        ignoredActions: ['persist/PERSIST']
      }
    })
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;

// hooks/redux.ts - Typed hooks
import { useDispatch, useSelector, TypedUseSelectorHook } from 'react-redux';
import type { RootState, AppDispatch } from '../store';

export const useAppDispatch = () => useDispatch<AppDispatch>();
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;

// Component usage
function UserProfile({ userId }: { userId: string }) {
  const dispatch = useAppDispatch();
  const { currentUser, loading, error } = useAppSelector(state => state.user);

  useEffect(() => {
    dispatch(fetchUser(userId));
  }, [dispatch, userId]);

  const handleUpdateProfile = (updates: any) => {
    dispatch(updateUser({ userId, updates }));
  };

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      <h1>{currentUser?.name}</h1>
      <button onClick={() => handleUpdateProfile({ name: 'New Name' })}>
        Update Name
      </button>
    </div>
  );
}
```

### Zustand - Lightweight Alternative

**Problem:** How do we manage state with minimal boilerplate while maintaining TypeScript support?

```jsx
import { create } from 'zustand';
import { subscribeWithSelector } from 'zustand/middleware';

// Define store interface
interface UserStore {
  // State
  user: User | null;
  users: Record<string, User>;
  loading: boolean;
  error: string | null;
  
  // Actions
  setUser: (user: User) => void;
  updateUser: (id: string, updates: Partial<User>) => void;
  fetchUser: (id: string) => Promise<void>;
  clearError: () => void;
  reset: () => void;
}

// Create store
const useUserStore = create<UserStore>()(
  subscribeWithSelector((set, get) => ({
    // Initial state
    user: null,
    users: {},
    loading: false,
    error: null,

    // Actions
    setUser: (user) => set({ user }),
    
    clearError: () => set({ error: null }),
    
    updateUser: (id, updates) => set((state) => ({
      users: {
        ...state.users,
        [id]: { ...state.users[id], ...updates }
      },
      user: state.user?.id === id ? { ...state.user, ...updates } : state.user
    })),

    fetchUser: async (id) => {
      set({ loading: true, error: null });
      try {
        const response = await fetch(`/api/users/${id}`);
        if (!response.ok) throw new Error('Failed to fetch user');
        const user = await response.json();
        
        set((state) => ({
          users: { ...state.users, [id]: user },
          user: state.user?.id === id ? user : state.user,
          loading: false
        }));
      } catch (error) {
        set({ error: error.message, loading: false });
      }
    },

    reset: () => set({ user: null, users: {}, loading: false, error: null })
  }))
);

// Component usage
function UserProfile({ userId }: { userId: string }) {
  const { user, loading, error, fetchUser, updateUser } = useUserStore((state) => ({
    user: state.users[userId] || state.user,
    loading: state.loading,
    error: state.error,
    fetchUser: state.fetchUser,
    updateUser: state.updateUser
  }));

  useEffect(() => {
    if (!user) {
      fetchUser(userId);
    }
  }, [userId, user, fetchUser]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      <h1>{user?.name}</h1>
      <button onClick={() => updateUser(userId, { name: 'Updated Name' })}>
        Update Name
      </button>
    </div>
  );
}

// Advanced Zustand patterns
// Slices pattern for large stores
const createUserSlice = (set, get) => ({
  user: null,
  setUser: (user) => set((state) => ({ ...state, user }))
});

const createPostsSlice = (set, get) => ({
  posts: [],
  addPost: (post) => set((state) => ({ 
    ...state, 
    posts: [...state.posts, post] 
  }))
});

const useAppStore = create((...a) => ({
  ...createUserSlice(...a),
  ...createPostsSlice(...a)
}));
```

### Context vs External Stores Decision Matrix

| Factor | Context API | Redux Toolkit | Zustand | Jotai |
|--------|-------------|---------------|---------|-------|
| **Learning Curve** | Low | High | Low | Medium |
| **Bundle Size** | 0KB | ~10KB | ~1KB | ~5KB |
| **DevTools** | Basic | Excellent | Good | Good |
| **Async Handling** | Manual | Built-in | Manual | Built-in |
| **Re-render Control** | Manual | Automatic | Automatic | Atomic |
| **Best For** | Simple state | Complex apps | Medium apps | Atomic updates |

---

## Data Fetching Patterns

### React Query (TanStack Query) - The Modern Approach

**Problem:** How do we handle server state, caching, synchronization, and loading states efficiently?

```jsx
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

// API functions
const api = {
  getUser: async (id: string) => {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) throw new Error('Failed to fetch user');
    return response.json();
  },
  
  getUsers: async ({ page = 1, search = '' }) => {
    const params = new URLSearchParams({ 
      page: page.toString(),
      search 
    });
    const response = await fetch(`/api/users?${params}`);
    if (!response.ok) throw new Error('Failed to fetch users');
    return response.json();
  },
  
  updateUser: async ({ id, updates }: { id: string; updates: any }) => {
    const response = await fetch(`/api/users/${id}`, {
      method: 'PATCH',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(updates)
    });
    if (!response.ok) throw new Error('Failed to update user');
    return response.json();
  }
};

// Query keys factory
const userKeys = {
  all: ['users'] as const,
  lists: () => [...userKeys.all, 'list'] as const,
  list: (filters: any) => [...userKeys.lists(), filters] as const,
  details: () => [...userKeys.all, 'detail'] as const,
  detail: (id: string) => [...userKeys.details(), id] as const,
};

// Custom hooks
function useUser(id: string) {
  return useQuery({
    queryKey: userKeys.detail(id),
    queryFn: () => api.getUser(id),
    enabled: !!id, // Only run if id exists
    staleTime: 5 * 60 * 1000, // 5 minutes
    cacheTime: 10 * 60 * 1000, // 10 minutes
  });
}

function useUsers(filters: { page: number; search: string }) {
  return useQuery({
    queryKey: userKeys.list(filters),
    queryFn: () => api.getUsers(filters),
    keepPreviousData: true, // Keep showing old data while fetching new
    staleTime: 30 * 1000, // 30 seconds
  });
}

function useUpdateUser() {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: api.updateUser,
    onSuccess: (updatedUser, { id }) => {
      // Update user detail cache
      queryClient.setQueryData(userKeys.detail(id), updatedUser);
      
      // Invalidate and refetch user lists
      queryClient.invalidateQueries({ queryKey: userKeys.lists() });
      
      // Optimistically update lists
      queryClient.setQueriesData(
        { queryKey: userKeys.lists() },
        (oldData: any) => {
          if (!oldData) return oldData;
          return {
            ...oldData,
            users: oldData.users.map((user: any) =>
              user.id === id ? updatedUser : user
            )
          };
        }
      );
    },
    onError: () => {
      // Invalidate queries on error to refetch fresh data
      queryClient.invalidateQueries({ queryKey: userKeys.all });
    }
  });
}

// Component with React Query
function UserList() {
  const [page, setPage] = useState(1);
  const [search, setSearch] = useState('');
  const deferredSearch = useDeferredValue(search);

  const {
    data,
    isLoading,
    isError,
    error,
    isFetching,
    isPreviousData
  } = useUsers({ page, search: deferredSearch });

  const updateUserMutation = useUpdateUser();

  const handleUpdateUser = (id: string, updates: any) => {
    updateUserMutation.mutate({ id, updates });
  };

  if (isLoading) return <div>Loading...</div>;
  if (isError) return <div>Error: {error.message}</div>;

  return (
    <div>
      <input
        value={search}
        onChange={(e) => setSearch(e.target.value)}
        placeholder="Search users..."
      />
      
      {isFetching && <div>Updating...</div>}
      
      <div className={isPreviousData ? 'opacity-50' : ''}>
        {data?.users.map(user => (
          <UserCard
            key={user.id}
            user={user}
            onUpdate={(updates) => handleUpdateUser(user.id, updates)}
            isUpdating={updateUserMutation.isLoading}
          />
        ))}
      </div>
      
      <div>
        <button
          onClick={() => setPage(old => Math.max(old - 1, 1))}
          disabled={page === 1}
        >
          Previous
        </button>
        <span>Page {page}</span>
        <button
          onClick={() => setPage(old => old + 1)}
          disabled={isPreviousData || !data?.hasMore}
        >
          Next
        </button>
      </div>
    </div>
  );
}
```

### Suspense for Data Fetching

```jsx
// React Query with Suspense
function useUserSuspense(id: string) {
  return useSuspenseQuery({
    queryKey: userKeys.detail(id),
    queryFn: () => api.getUser(id),
  });
}

// Component that uses Suspense
function UserProfile({ userId }: { userId: string }) {
  const { data: user } = useUserSuspense(userId);

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}

// Parent component with error and loading boundaries
function UserPage({ userId }: { userId: string }) {
  return (
    <ErrorBoundary fallback={<ErrorFallback />}>
      <Suspense fallback={<UserProfileSkeleton />}>
        <UserProfile userId={userId} />
      </Suspense>
    </ErrorBoundary>
  );
}

// Error boundary for data fetching errors
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || <div>Something went wrong.</div>;
    }

    return this.props.children;
  }
}
```

---

## Code-Splitting & Lazy Loading

### Route-Level Code Splitting

**Problem:** How do we reduce initial bundle size by loading code only when needed?

```jsx
import { lazy, Suspense } from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

// Lazy load route components
const HomePage = lazy(() => import('./pages/HomePage'));
const UserPage = lazy(() => import('./pages/UserPage'));
const SettingsPage = lazy(() => import('./pages/SettingsPage'));
const AdminPage = lazy(() => 
  import('./pages/AdminPage').then(module => ({
    default: module.AdminPage
  }))
);

// Lazy load with error handling and retry
const DashboardPage = lazy(() => 
  import('./pages/DashboardPage').catch(() => {
    // Fallback or retry logic
    return import('./pages/ErrorPage');
  })
);

function App() {
  return (
    <BrowserRouter>
      <div className="app">
        <Navigation />
        
        <main>
          <Suspense fallback={<PageLoadingSpinner />}>
            <Routes>
              <Route path="/" element={<HomePage />} />
              <Route path="/users/:id" element={<UserPage />} />
              <Route path="/settings" element={<SettingsPage />} />
              <Route path="/admin" element={<AdminPage />} />
              <Route path="/dashboard" element={<DashboardPage />} />
            </Routes>
          </Suspense>
        </main>
      </div>
    </BrowserRouter>
  );
}

// Enhanced loading component
function PageLoadingSpinner() {
  const [showSpinner, setShowSpinner] = useState(false);

  // Only show spinner after a delay to avoid flashing
  useEffect(() => {
    const timer = setTimeout(() => setShowSpinner(true), 200);
    return () => clearTimeout(timer);
  }, []);

  if (!showSpinner) return null;

  return (
    <div className="page-loading">
      <div className="spinner" />
      <p>Loading page...</p>
    </div>
  );
}
```

### Component-Level Lazy Loading

```jsx
// Lazy load heavy components
const HeavyChart = lazy(() => import('./components/HeavyChart'));
const DataTable = lazy(() => import('./components/DataTable'));

function Dashboard({ showChart, showTable, data }) {
  return (
    <div className="dashboard">
      <h1>Dashboard</h1>
      
      {/* Conditionally load heavy components */}
      {showChart && (
        <Suspense fallback={<ChartSkeleton />}>
          <HeavyChart data={data} />
        </Suspense>
      )}
      
      {showTable && (
        <Suspense fallback={<TableSkeleton />}>
          <DataTable data={data} />
        </Suspense>
      )}
    </div>
  );
}

// Dynamic imports with parameters
function Modal({ type, isOpen, onClose }) {
  const [ModalComponent, setModalComponent] = useState(null);

  useEffect(() => {
    if (isOpen && type) {
      // Dynamically import modal component based on type
      import(`./modals/${type}Modal`).then(module => {
        setModalComponent(() => module.default);
      });
    }
  }, [isOpen, type]);

  if (!isOpen || !ModalComponent) return null;

  return (
    <div className="modal-overlay" onClick={onClose}>
      <Suspense fallback={<ModalSkeleton />}>
        <ModalComponent onClose={onClose} />
      </Suspense>
    </div>
  );
}

// Preloading strategy
function usePreloadRoute(routePath: string) {
  const preload = useCallback(() => {
    // Preload route component
    switch (routePath) {
      case '/dashboard':
        import('./pages/DashboardPage');
        break;
      case '/settings':
        import('./pages/SettingsPage');
        break;
    }
  }, [routePath]);

  return preload;
}

// Component with preloading
function NavigationLink({ to, children }) {
  const preload = usePreloadRoute(to);

  return (
    <Link 
      to={to}
      onMouseEnter={preload} // Preload on hover
      onFocus={preload}      // Preload on focus
    >
      {children}
    </Link>
  );
}
```

---

## Accessibility (a11y)

### Focus Management and Keyboard Navigation

**Problem:** How do we ensure our applications are accessible to users with disabilities?

```jsx
// Accessible modal with focus management
function AccessibleModal({ isOpen, onClose, title, children }) {
  const modalRef = useRef(null);
  const previousFocusRef = useRef(null);

  useEffect(() => {
    if (isOpen) {
      // Store previously focused element
      previousFocusRef.current = document.activeElement;
      
      // Focus the modal
      modalRef.current?.focus();
      
      // Trap focus within modal
      const handleKeyDown = (e) => {
        if (e.key === 'Escape') {
          onClose();
        } else if (e.key === 'Tab') {
          trapFocus(e, modalRef.current);
        }
      };

      document.addEventListener('keydown', handleKeyDown);
      
      return () => {
        document.removeEventListener('keydown', handleKeyDown);
        // Restore focus to previous element
        previousFocusRef.current?.focus();
      };
    }
  }, [isOpen, onClose]);

  if (!isOpen) return null;

  return (
    <div 
      className="modal-overlay" 
      onClick={onClose}
      role="dialog"
      aria-modal="true"
      aria-labelledby="modal-title"
    >
      <div
        ref={modalRef}
        className="modal-content"
        onClick={(e) => e.stopPropagation()}
        tabIndex={-1}
        role="document"
      >
        <header className="modal-header">
          <h2 id="modal-title">{title}</h2>
          <button
            onClick={onClose}
            aria-label="Close modal"
            className="close-button"
          >
            ×
          </button>
        </header>
        
        <div className="modal-body">
          {children}
        </div>
      </div>
    </div>
  );
}

// Focus trap utility
function trapFocus(event, container) {
  const focusableElements = container.querySelectorAll(
    'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
  );
  
  const firstElement = focusableElements[0];
  const lastElement = focusableElements[focusableElements.length - 1];

  if (event.shiftKey) {
    if (document.activeElement === firstElement) {
      lastElement.focus();
      event.preventDefault();
    }
  } else {
    if (document.activeElement === lastElement) {
      firstElement.focus();
      event.preventDefault();
    }
  }
}
```

### Screen Reader Support and ARIA

```jsx
// Accessible form with proper labeling and error handling
function AccessibleForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: ''
  });
  const [errors, setErrors] = useState({});
  const [isSubmitting, setIsSubmitting] = useState(false);

  const handleSubmit = async (e) => {
    e.preventDefault();
    setIsSubmitting(true);
    
    // Validate and submit
    const newErrors = validateForm(formData);
    setErrors(newErrors);
    
    if (Object.keys(newErrors).length === 0) {
      // Submit form
      await submitForm(formData);
    }
    
    setIsSubmitting(false);
  };

  return (
    <form onSubmit={handleSubmit} noValidate>
      <fieldset>
        <legend>Contact Information</legend>
        
        <div className="form-group">
          <label htmlFor="name">
            Name *
          </label>
          <input
            id="name"
            type="text"
            value={formData.name}
            onChange={(e) => setFormData(prev => ({ ...prev, name: e.target.value }))}
            aria-required="true"
            aria-invalid={errors.name ? 'true' : 'false'}
            aria-describedby={errors.name ? 'name-error' : undefined}
          />
          {errors.name && (
            <div id="name-error" role="alert" className="error">
              {errors.name}
            </div>
          )}
        </div>

        <div className="form-group">
          <label htmlFor="email">
            Email Address *
          </label>
          <input
            id="email"
            type="email"
            value={formData.email}
            onChange={(e) => setFormData(prev => ({ ...prev, email: e.target.value }))}
            aria-required="true"
            aria-invalid={errors.email ? 'true' : 'false'}
            aria-describedby={errors.email ? 'email-error' : 'email-help'}
          />
          <div id="email-help" className="help-text">
            We'll never share your email address.
          </div>
          {errors.email && (
            <div id="email-error" role="alert" className="error">
              {errors.email}
            </div>
          )}
        </div>

        <div className="form-group">
          <label htmlFor="message">
            Message
          </label>
          <textarea
            id="message"
            value={formData.message}
            onChange={(e) => setFormData(prev => ({ ...prev, message: e.target.value }))}
            rows={4}
            aria-describedby="message-help"
          />
          <div id="message-help" className="help-text">
            Optional: Tell us more about your inquiry.
          </div>
        </div>
      </fieldset>

      <button
        type="submit"
        disabled={isSubmitting}
        aria-describedby="submit-status"
      >
        {isSubmitting ? 'Submitting...' : 'Submit Form'}
      </button>
      
      {isSubmitting && (
        <div id="submit-status" aria-live="polite" className="sr-only">
          Submitting form, please wait.
        </div>
      )}
    </form>
  );
}

// Accessible data table
function AccessibleDataTable({ data, columns }) {
  const [sortConfig, setSortConfig] = useState({ key: null, direction: 'asc' });

  const sortedData = useMemo(() => {
    if (!sortConfig.key) return data;
    
    return [...data].sort((a, b) => {
      if (a[sortConfig.key] < b[sortConfig.key]) {
        return sortConfig.direction === 'asc' ? -1 : 1;
      }
      if (a[sortConfig.key] > b[sortConfig.key]) {
        return sortConfig.direction === 'asc' ? 1 : -1;
      }
      return 0;
    });
  }, [data, sortConfig]);

  const handleSort = (key) => {
    setSortConfig(prev => ({
      key,
      direction: prev.key === key && prev.direction === 'asc' ? 'desc' : 'asc'
    }));
  };

  return (
    <table role="table" aria-label="User data">
      <caption>
        List of {data.length} users. Use column headers to sort data.
      </caption>
      
      <thead>
        <tr>
          {columns.map(column => (
            <th
              key={column.key}
              scope="col"
              aria-sort={
                sortConfig.key === column.key
                  ? sortConfig.direction === 'asc' ? 'ascending' : 'descending'
                  : 'none'
              }
            >
              <button
                onClick={() => handleSort(column.key)}
                className="sort-button"
                aria-label={`Sort by ${column.label}`}
              >
                {column.label}
                {sortConfig.key === column.key && (
                  <span aria-hidden="true">
                    {sortConfig.direction === 'asc' ? ' ↑' : ' ↓'}
                  </span>
                )}
              </button>
            </th>
          ))}
        </tr>
      </thead>
      
      <tbody>
        {sortedData.map(row => (
          <tr key={row.id}>
            {columns.map(column => (
              <td key={column.key}>
                {row[column.key]}
              </td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

---

**Continue to:** [[reactjs-expert-architectural|Stage 4: Expert & Architectural (Pro)]]

---

## Advanced Engineering Mastery Checklist

Before moving to Stage 4, ensure you can:

- [ ] Implement concurrent rendering features (useTransition, useDeferredValue)
- [ ] Profile and optimize component performance
- [ ] Design scalable state management solutions
- [ ] Implement server-side rendering strategies
- [ ] Use modern data fetching patterns with React Query
- [ ] Implement code-splitting and lazy loading
- [ ] Build accessible components with proper ARIA support
- [ ] Set up internationalization for global applications
- [ ] Create maintainable design systems
- [ ] Write comprehensive test suites
