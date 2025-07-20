

# Prepare a document covering the most popular and most regularly used design patterns in reactjs to developer large and scalable web application

explanation should be intuitive and code examples are also needed
provide pros and cons of each patterns along with information about the best scenario where this design pattern should be used. multiple scenarios are welcomed
this document should be the one stop solution for learning reactjs design patterns

Based on the comprehensive research I've conducted, I now have extensive information about React design patterns for scalable applications. I'll create a comprehensive document that serves as a one-stop solution for learning React design patterns with explanations, code examples, pros/cons, and best use cases.

# React Design Patterns for Scalable Web Applications: The Complete Guide

## Table of Contents

1. [Introduction](#introduction)
2. [Container and Presentational Components](#container-and-presentational-components)
3. [Custom Hooks Pattern](#custom-hooks-pattern)
4. [Higher-Order Components (HOCs)](#higher-order-components-hocs)
5. [Render Props Pattern](#render-props-pattern)
6. [Compound Components Pattern](#compound-components-pattern)
7. [Context API and Provider Pattern](#context-api-and-provider-pattern)
8. [Memoization Patterns](#memoization-patterns)
9. [Error Boundaries Pattern](#error-boundaries-pattern)
10. [Lazy Loading and Code Splitting](#lazy-loading-and-code-splitting)
11. [Component Composition Pattern](#component-composition-pattern)
12. [State Management Patterns](#state-management-patterns)
13. [Performance Optimization Patterns](#performance-optimization-patterns)
14. [Best Practices and Guidelines](#best-practices-and-guidelines)

## Introduction

React design patterns are **proven, reusable solutions** that address common challenges in React application development[^1]. They guide developers in structuring and organizing code effectively, ensuring **scalability, maintainability, and efficiency** throughout the project lifecycle[^1][^2].

These patterns aren't one-size-fits-all solutions but provide **flexible approaches** to solving specific issues while adhering to React's component-driven architecture[^1]. Understanding and implementing these patterns effectively can mean the difference between an application that crumbles under its weight and one that thrives as it evolves[^3].

## Container and Presentational Components

### Overview

The Container and Presentational Components pattern **separates logic from presentation** by dividing components into two distinct categories[^2][^1]:

- **Container Components**: Handle state, business logic, and data fetching
- **Presentational Components**: Focus solely on rendering UI based on props received


### Code Example

```javascript
// Container Component (Smart Component)
import React, { useState, useEffect } from 'react';
import UserList from './UserList';

const UserListContainer = () => {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await fetch('/api/users');
        const data = await response.json();
        setUsers(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  const handleDeleteUser = (userId) => {
    setUsers(users.filter(user => user.id !== userId));
  };

  return (
    <UserList 
      users={users} 
      loading={loading} 
      error={error} 
      onDeleteUser={handleDeleteUser}
    />
  );
};

export default UserListContainer;
```

```javascript
// Presentational Component (Dumb Component)
import React from 'react';

const UserList = ({ users, loading, error, onDeleteUser }) => {
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!users.length) return <div>No users found</div>;

  return (
    <div className="user-list">
      <h2>Users</h2>
      <ul>
        {users.map(user => (
          <li key={user.id} className="user-item">
            <span>{user.name} ({user.email})</span>
            <button onClick={() => onDeleteUser(user.id)}>
              Delete
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default UserList;
```


### Pros and Cons

**✅ Pros:**

- **Separation of concerns**: Logic and UI are clearly separated[^4][^5]
- **Reusability**: Presentational components can be reused across different contexts[^4][^5]
- **Testability**: Easier to test logic and UI independently[^4][^5]
- **Maintainability**: Changes to business logic don't affect UI components[^4][^5]

**⚠️ Cons:**

- **Increased complexity**: More files and components to manage[^6]
- **Over-engineering**: Can be overkill for simple components[^6]
- **Props drilling**: May need to pass many props through container components[^6]


### Best Use Cases

1. **Large applications** with complex data fetching and state management[^1]
2. **Components that need to be reused** with different data sources[^1]
3. **Teams working on different parts** of the application (UI vs logic)[^1]
4. **Applications requiring extensive testing** of business logic[^5]

## Custom Hooks Pattern

### Overview

Custom hooks are **reusable functions** that encapsulate stateful logic and can be shared between components[^1][^7]. They follow the naming convention of starting with "use" and allow you to extract component logic into reusable functions[^7].

### Code Example

```javascript
// Custom Hook for API data fetching
import { useState, useEffect } from 'react';

const useFetch = (url) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        setError(null);
        const response = await fetch(url);
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
};

// Custom Hook for local storage
const useLocalStorage = (key, initialValue) => {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error('Error reading localStorage:', error);
      return initialValue;
    }
  });

  const setValue = (value) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error('Error setting localStorage:', error);
    }
  };

  return [storedValue, setValue];
};

// Usage in components
const UserProfile = ({ userId }) => {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);
  const [theme, setTheme] = useLocalStorage('theme', 'light');

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div className={`user-profile ${theme}`}>
      <h2>{user?.name}</h2>
      <p>{user?.email}</p>
      <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
        Toggle Theme
      </button>
    </div>
  );
};
```


### Pros and Cons

**✅ Pros:**

- **Reusability**: Logic can be shared across multiple components[^1][^7]
- **Separation of concerns**: Extracts stateful logic from components[^1][^7]
- **Testability**: Hooks can be tested independently[^1][^7]
- **Cleaner components**: Reduces component complexity[^1][^7]

**⚠️ Cons:**

- **Learning curve**: Requires understanding of React hooks lifecycle[^7]
- **Debugging complexity**: Can make debugging more challenging[^7]
- **Over-abstraction**: May lead to unnecessary abstractions[^7]


### Best Use Cases

1. **Data fetching** with consistent patterns across components[^1]
2. **Form management** with validation and state handling[^1]
3. **Local storage** or session storage interactions[^1]
4. **Event listeners** and cleanup logic[^7]
5. **Animation states** and complex state transitions[^1]

## Higher-Order Components (HOCs)

### Overview

Higher-Order Components are **functions that take a component and return a new component** with enhanced functionality[^1][^8]. They're useful for adding cross-cutting concerns like authentication, logging, or theming without duplicating code[^1][^8].

### Code Example

```javascript
// Authentication HOC
import React from 'react';
import { useAuth } from './AuthContext';

const withAuth = (WrappedComponent) => {
  return function AuthenticatedComponent(props) {
    const { user, loading } = useAuth();

    if (loading) {
      return <div>Loading...</div>;
    }

    if (!user) {
      return (
        <div>
          <h2>Access Denied</h2>
          <p>Please log in to access this page.</p>
        </div>
      );
    }

    return <WrappedComponent {...props} user={user} />;
  };
};

// Logging HOC
const withLogging = (WrappedComponent, componentName) => {
  return function LoggingComponent(props) {
    React.useEffect(() => {
      console.log(`${componentName} mounted`);
      return () => {
        console.log(`${componentName} unmounted`);
      };
    }, []);

    React.useEffect(() => {
      console.log(`${componentName} updated with props:`, props);
    });

    return <WrappedComponent {...props} />;
  };
};

// Error Boundary HOC
const withErrorBoundary = (WrappedComponent) => {
  return class ErrorBoundaryWrapper extends React.Component {
    constructor(props) {
      super(props);
      this.state = { hasError: false };
    }

    static getDerivedStateFromError(error) {
      return { hasError: true };
    }

    componentDidCatch(error, errorInfo) {
      console.error('Error caught by HOC:', error, errorInfo);
    }

    render() {
      if (this.state.hasError) {
        return (
          <div>
            <h2>Something went wrong.</h2>
            <button onClick={() => this.setState({ hasError: false })}>
              Try again
            </button>
          </div>
        );
      }

      return <WrappedComponent {...this.props} />;
    }
  };
};

// Usage
const Dashboard = ({ user }) => (
  <div>
    <h1>Welcome, {user.name}!</h1>
    <p>This is your dashboard.</p>
  </div>
);

const EnhancedDashboard = withErrorBoundary(
  withLogging(
    withAuth(Dashboard),
    'Dashboard'
  )
);
```


### Pros and Cons

**✅ Pros:**

- **Code reusability**: Share functionality across multiple components[^1][^8]
- **Separation of concerns**: Keep components focused on their primary responsibility[^1][^8]
- **Composition**: Can be chained together for multiple enhancements[^8]
- **Testing**: Each HOC can be tested independently[^8]

**⚠️ Cons:**

- **Wrapper hell**: Multiple HOCs can create deep component trees[^8]
- **Props collision**: HOCs might overwrite props with the same name[^8]
- **Debugging difficulty**: Can make component trees harder to debug[^8]
- **Static hoisting**: Requires manual hoisting of static methods[^8]


### Best Use Cases

1. **Authentication and authorization** checks[^1][^8]
2. **Error handling** and error boundaries[^8]
3. **Logging and analytics** tracking[^8]
4. **Theme or styling** enhancements[^8]
5. **Feature flagging** and conditional rendering[^8]

## Render Props Pattern

### Overview

The Render Props pattern involves **sharing functionality between components using a prop that is a function**[^1][^8]. This pattern allows greater flexibility and dynamic behavior by letting the consumer control what gets rendered[^1][^8].

### Code Example

```javascript
// Mouse Tracker with Render Props
import React, { useState, useEffect } from 'react';

const MouseTracker = ({ render }) => {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMouseMove = (e) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };

    window.addEventListener('mousemove', handleMouseMove);
    return () => window.removeEventListener('mousemove', handleMouseMove);
  }, []);

  return render(position);
};

// Data Fetcher with Render Props
const DataFetcher = ({ url, render }) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await fetch(url);
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return render({ data, loading, error });
};

// Toggle Component with Render Props
const Toggle = ({ render }) => {
  const [isOn, setIsOn] = useState(false);

  const toggle = () => setIsOn(!isOn);

  return render({ isOn, toggle });
};

// Usage Examples
const App = () => {
  return (
    <div>
      {/* Mouse position display */}
      <MouseTracker
        render={({ x, y }) => (
          <div>
            <h2>Mouse Position</h2>
            <p>X: {x}, Y: {y}</p>
          </div>
        )}
      />

      {/* Data fetching */}
      <DataFetcher
        url="/api/users"
        render={({ data, loading, error }) => {
          if (loading) return <div>Loading...</div>;
          if (error) return <div>Error: {error}</div>;
          return (
            <ul>
              {data?.map(user => (
                <li key={user.id}>{user.name}</li>
              ))}
            </ul>
          );
        }}
      />

      {/* Toggle functionality */}
      <Toggle
        render={({ isOn, toggle }) => (
          <div>
            <button onClick={toggle}>
              {isOn ? 'Turn Off' : 'Turn On'}
            </button>
            {isOn && <p>The toggle is ON!</p>}
          </div>
        )}
      />
    </div>
  );
};
```


### Pros and Cons

**✅ Pros:**

- **Flexibility**: Consumers control what gets rendered[^1][^8]
- **Reusability**: Same logic can be used with different UIs[^1][^8]
- **Composability**: Easy to combine with other patterns[^8]
- **No wrapper hell**: Doesn't create additional component layers[^8]

**⚠️ Cons:**

- **Complexity**: Can make components harder to understand[^8]
- **Performance**: Function calls in render can impact performance[^8]
- **Nesting**: Can lead to deeply nested render prop functions[^8]


### Best Use Cases

1. **Mouse tracking** and user interaction handling[^8]
2. **Data fetching** with customizable rendering[^1][^8]
3. **Toggle states** and modal management[^8]
4. **Animation states** with flexible rendering[^8]
5. **Form handling** with custom field rendering[^8]

## Compound Components Pattern

### Overview

The Compound Components pattern allows **multiple components to work together** as a cohesive unit while maintaining flexibility in their arrangement[^9]. This pattern is similar to how HTML elements like `<select>` and `<option>` work together[^9].

### Code Example

```javascript
// Tabs Compound Component
import React, { useState, createContext, useContext } from 'react';

const TabsContext = createContext();

const Tabs = ({ children, defaultTab = 0 }) => {
  const [activeTab, setActiveTab] = useState(defaultTab);

  const contextValue = {
    activeTab,
    setActiveTab,
  };

  return (
    <TabsContext.Provider value={contextValue}>
      <div className="tabs-container">
        {children}
      </div>
    </TabsContext.Provider>
  );
};

const TabsList = ({ children }) => {
  return (
    <div className="tabs-list" role="tablist">
      {children}
    </div>
  );
};

const Tab = ({ children, index, disabled = false }) => {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  const isActive = activeTab === index;

  return (
    <button
      className={`tab ${isActive ? 'active' : ''} ${disabled ? 'disabled' : ''}`}
      onClick={() => !disabled && setActiveTab(index)}
      disabled={disabled}
      role="tab"
      aria-selected={isActive}
    >
      {children}
    </button>
  );
};

const TabPanels = ({ children }) => {
  return (
    <div className="tab-panels">
      {children}
    </div>
  );
};

const TabPanel = ({ children, index }) => {
  const { activeTab } = useContext(TabsContext);
  const isActive = activeTab === index;

  return (
    <div
      className={`tab-panel ${isActive ? 'active' : ''}`}
      role="tabpanel"
      hidden={!isActive}
    >
      {isActive && children}
    </div>
  );
};

// Assign sub-components to main component
Tabs.List = TabsList;
Tabs.Tab = Tab;
Tabs.Panels = TabPanels;
Tabs.Panel = TabPanel;

// Modal Compound Component
const Modal = ({ children, isOpen, onClose }) => {
  if (!isOpen) return null;

  return (
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={e => e.stopPropagation()}>
        {children}
      </div>
    </div>
  );
};

const ModalHeader = ({ children }) => (
  <div className="modal-header">
    {children}
  </div>
);

const ModalBody = ({ children }) => (
  <div className="modal-body">
    {children}
  </div>
);

const ModalFooter = ({ children }) => (
  <div className="modal-footer">
    {children}
  </div>
);

Modal.Header = ModalHeader;
Modal.Body = ModalBody;
Modal.Footer = ModalFooter;

// Usage Examples
const App = () => {
  const [modalOpen, setModalOpen] = useState(false);

  return (
    <div>
      {/* Tabs Usage */}
      <Tabs defaultTab={0}>
        <Tabs.List>
          <Tabs.Tab index={0}>Home</Tabs.Tab>
          <Tabs.Tab index={1}>About</Tabs.Tab>
          <Tabs.Tab index={2} disabled>Contact</Tabs.Tab>
        </Tabs.List>

        <Tabs.Panels>
          <Tabs.Panel index={0}>
            <h2>Home Content</h2>
            <p>Welcome to the home page!</p>
          </Tabs.Panel>
          <Tabs.Panel index={1}>
            <h2>About Content</h2>
            <p>Learn more about us here.</p>
          </Tabs.Panel>
          <Tabs.Panel index={2}>
            <h2>Contact Content</h2>
            <p>Get in touch with us.</p>
          </Tabs.Panel>
        </Tabs.Panels>
      </Tabs>

      {/* Modal Usage */}
      <button onClick={() => setModalOpen(true)}>
        Open Modal
      </button>

      <Modal isOpen={modalOpen} onClose={() => setModalOpen(false)}>
        <Modal.Header>
          <h2>Confirm Action</h2>
        </Modal.Header>
        <Modal.Body>
          <p>Are you sure you want to proceed?</p>
        </Modal.Body>
        <Modal.Footer>
          <button onClick={() => setModalOpen(false)}>Cancel</button>
          <button onClick={() => setModalOpen(false)}>Confirm</button>
        </Modal.Footer>
      </Modal>
    </div>
  );
};
```


### Pros and Cons

**✅ Pros:**

- **Flexibility**: Components can be rearranged and customized[^9]
- **Reusability**: Can be used in different contexts with varying content[^9]
- **Encapsulation**: Related functionality is grouped together[^9]
- **Intuitive API**: Similar to familiar HTML patterns[^9]

**⚠️ Cons:**

- **Complexity**: More complex to implement than simple components[^9]
- **Context overhead**: Requires React Context for state sharing[^9]
- **Learning curve**: Developers need to understand the compound pattern[^9]


### Best Use Cases

1. **UI component libraries** like tabs, accordions, and modals[^9]
2. **Form components** with labels, inputs, and validation[^9]
3. **Navigation components** with flexible layouts[^9]
4. **Data display components** like tables with headers and rows[^9]
5. **Card components** with headers, bodies, and footers[^9]

## Context API and Provider Pattern

### Overview

The Context API provides a way to **share state globally** across components without passing props manually through the component tree[^10][^11]. The Provider pattern uses Context to efficiently manage global state like themes, authentication, or user preferences[^10][^11].

### Code Example

```javascript
// Auth Context
import React, { createContext, useContext, useReducer, useEffect } from 'react';

const AuthContext = createContext();

const authReducer = (state, action) => {
  switch (action.type) {
    case 'LOGIN_START':
      return { ...state, loading: true, error: null };
    case 'LOGIN_SUCCESS':
      return { 
        ...state, 
        user: action.payload, 
        loading: false, 
        isAuthenticated: true 
      };
    case 'LOGIN_ERROR':
      return { 
        ...state, 
        loading: false, 
        error: action.payload,
        isAuthenticated: false 
      };
    case 'LOGOUT':
      return { 
        ...state, 
        user: null, 
        isAuthenticated: false,
        loading: false 
      };
    default:
      return state;
  }
};

const initialState = {
  user: null,
  isAuthenticated: false,
  loading: false,
  error: null,
};

export const AuthProvider = ({ children }) => {
  const [state, dispatch] = useReducer(authReducer, initialState);

  // Check for existing auth token on mount
  useEffect(() => {
    const token = localStorage.getItem('authToken');
    if (token) {
      // Verify token and get user data
      verifyToken(token);
    }
  }, []);

  const login = async (credentials) => {
    dispatch({ type: 'LOGIN_START' });
    try {
      const response = await fetch('/api/auth/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(credentials),
      });
      
      const data = await response.json();
      
      if (response.ok) {
        localStorage.setItem('authToken', data.token);
        dispatch({ type: 'LOGIN_SUCCESS', payload: data.user });
      } else {
        dispatch({ type: 'LOGIN_ERROR', payload: data.message });
      }
    } catch (error) {
      dispatch({ type: 'LOGIN_ERROR', payload: error.message });
    }
  };

  const logout = () => {
    localStorage.removeItem('authToken');
    dispatch({ type: 'LOGOUT' });
  };

  const verifyToken = async (token) => {
    try {
      const response = await fetch('/api/auth/verify', {
        headers: { Authorization: `Bearer ${token}` },
      });
      
      if (response.ok) {
        const user = await response.json();
        dispatch({ type: 'LOGIN_SUCCESS', payload: user });
      } else {
        localStorage.removeItem('authToken');
      }
    } catch (error) {
      localStorage.removeItem('authToken');
    }
  };

  const value = {
    ...state,
    login,
    logout,
  };

  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
};

// Theme Context
const ThemeContext = createContext();

export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState(() => {
    const savedTheme = localStorage.getItem('theme');
    return savedTheme || 'light';
  });

  useEffect(() => {
    localStorage.setItem('theme', theme);
    document.documentElement.setAttribute('data-theme', theme);
  }, [theme]);

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };

  const value = {
    theme,
    toggleTheme,
  };

  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
};

// Combined Provider
export const AppProvider = ({ children }) => {
  return (
    <AuthProvider>
      <ThemeProvider>
        {children}
      </ThemeProvider>
    </AuthProvider>
  );
};

// Custom hooks for consuming context
export const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  return context;
};

export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
};

// Usage in components
const Header = () => {
  const { user, logout } = useAuth();
  const { theme, toggleTheme } = useTheme();

  return (
    <header className="header">
      <h1>My App</h1>
      <div className="header-actions">
        <button onClick={toggleTheme}>
          {theme === 'light' ? '🌙' : '☀️'}
        </button>
        {user ? (
          <div>
            <span>Welcome, {user.name}!</span>
            <button onClick={logout}>Logout</button>
          </div>
        ) : (
          <button>Login</button>
        )}
      </div>
    </header>
  );
};

// App root
const App = () => {
  return (
    <AppProvider>
      <div className="app">
        <Header />
        <main>
          {/* Your app content */}
        </main>
      </div>
    </AppProvider>
  );
};
```


### Pros and Cons

**✅ Pros:**

- **Eliminates prop drilling**: Data flows directly to components that need it[^10][^11]
- **Global state management**: Perfect for app-wide state like themes or auth[^10][^11]
- **Built-in**: No additional dependencies required[^10][^11]
- **Flexibility**: Can create multiple contexts for different concerns[^11]

**⚠️ Cons:**

- **Re-render issues**: All consumers re-render when context value changes[^11]
- **Not suitable for frequent updates**: Performance issues with rapidly changing data[^11]
- **Testing complexity**: Requires provider wrappers in tests[^11]


### Best Use Cases

1. **Authentication state** and user information[^10][^11]
2. **Theme management** and UI preferences[^10][^11]
3. **Language/localization** settings[^10][^11]
4. **Application configuration** and feature flags[^11]
5. **Shopping cart** or similar app-wide state[^11]

## Memoization Patterns

### Overview

Memoization patterns in React help **optimize performance** by preventing unnecessary re-renders and recalculations[^12][^13]. The main memoization techniques include `React.memo`, `useMemo`, and `useCallback`[^12][^13].

### Code Example

```javascript
// React.memo for component memoization
import React, { memo, useState, useMemo, useCallback } from 'react';

const ExpensiveComponent = memo(({ data, onItemClick }) => {
  console.log('ExpensiveComponent rendered');
  
  return (
    <div className="expensive-component">
      <h3>Expensive Component</h3>
      <ul>
        {data.map(item => (
          <li key={item.id} onClick={() => onItemClick(item)}>
            {item.name} - ${item.price}
          </li>
        ))}
      </ul>
    </div>
  );
});

// Custom comparison function for React.memo
const UserCard = memo(({ user, onEdit }) => {
  console.log('UserCard rendered');
  
  return (
    <div className="user-card">
      <h4>{user.name}</h4>
      <p>{user.email}</p>
      <button onClick={() => onEdit(user)}>Edit</button>
    </div>
  );
}, (prevProps, nextProps) => {
  // Custom comparison - only re-render if user.id changes
  return prevProps.user.id === nextProps.user.id;
});

// Parent component demonstrating all memoization patterns
const ProductList = ({ products, onProductUpdate }) => {
  const [filter, setFilter] = useState('');
  const [sortBy, setSortBy] = useState('name');
  const [user, setUser] = useState({ name: 'John', email: 'john@example.com' });

  // useMemo for expensive calculations
  const filteredAndSortedProducts = useMemo(() => {
    console.log('Filtering and sorting products');
    
    return products
      .filter(product => 
        product.name.toLowerCase().includes(filter.toLowerCase())
      )
      .sort((a, b) => {
        if (sortBy === 'price') {
          return a.price - b.price;
        }
        return a.name.localeCompare(b.name);
      });
  }, [products, filter, sortBy]);

  // useCallback for stable function references
  const handleItemClick = useCallback((item) => {
    console.log('Item clicked:', item);
    onProductUpdate(item);
  }, [onProductUpdate]);

  const handleUserEdit = useCallback((userData) => {
    console.log('Editing user:', userData);
    setUser(userData);
  }, []);

  // Expensive calculation for statistics
  const statistics = useMemo(() => {
    console.log('Calculating statistics');
    
    const total = filteredAndSortedProducts.reduce((sum, product) => sum + product.price, 0);
    const average = total / filteredAndSortedProducts.length || 0;
    const cheapest = filteredAndSortedProducts.reduce((min, product) => 
      !min || product.price < min.price ? product : min, null);
    const mostExpensive = filteredAndSortedProducts.reduce((max, product) => 
      !max || product.price > max.price ? product : max, null);

    return {
      total: total.toFixed(2),
      average: average.toFixed(2),
      cheapest,
      mostExpensive,
      count: filteredAndSortedProducts.length
    };
  }, [filteredAndSortedProducts]);

  return (
    <div className="product-list">
      <div className="controls">
        <input
          type="text"
          placeholder="Filter products..."
          value={filter}
          onChange={(e) => setFilter(e.target.value)}
        />
        <select value={sortBy} onChange={(e) => setSortBy(e.target.value)}>
          <option value="name">Sort by Name</option>
          <option value="price">Sort by Price</option>
        </select>
      </div>

      <div className="statistics">
        <h3>Statistics</h3>
        <p>Total Products: {statistics.count}</p>
        <p>Total Value: ${statistics.total}</p>
        <p>Average Price: ${statistics.average}</p>
        {statistics.cheapest && (
          <p>Cheapest: {statistics.cheapest.name} - ${statistics.cheapest.price}</p>
        )}
        {statistics.mostExpensive && (
          <p>Most Expensive: {statistics.mostExpensive.name} - ${statistics.mostExpensive.price}</p>
        )}
      </div>

      <UserCard user={user} onEdit={handleUserEdit} />
      
      <ExpensiveComponent 
        data={filteredAndSortedProducts} 
        onItemClick={handleItemClick}
      />
    </div>
  );
};

// Usage example
const App = () => {
  const [products, setProducts] = useState([
    { id: 1, name: 'Laptop', price: 999.99 },
    { id: 2, name: 'Mouse', price: 29.99 },
    { id: 3, name: 'Keyboard', price: 79.99 },
    { id: 4, name: 'Monitor', price: 299.99 },
  ]);

  const handleProductUpdate = useCallback((product) => {
    setProducts(prev => 
      prev.map(p => p.id === product.id ? { ...p, ...product } : p)
    );
  }, []);

  return (
    <div>
      <h1>Product Management</h1>
      <ProductList products={products} onProductUpdate={handleProductUpdate} />
    </div>
  );
};
```


### Pros and Cons

**✅ Pros:**

- **Performance optimization**: Prevents unnecessary re-renders and calculations[^12][^13]
- **Stable references**: useCallback provides stable function references[^12][^13]
- **Memory efficiency**: Avoids recreating expensive computations[^12][^13]
- **Better UX**: Smoother user interactions with optimized components[^12][^13]

**⚠️ Cons:**

- **Memory usage**: Memoization consumes additional memory[^12][^13]
- **Overuse complexity**: Can make code harder to maintain if overused[^12][^13]
- **Debugging difficulty**: Can make component behavior less predictable[^12][^13]
- **Premature optimization**: May not provide benefits for simple components[^12][^13]


### Best Use Cases

1. **Expensive calculations** that don't need to run on every render[^12][^13]
2. **Large lists** or data-heavy components[^12][^13]
3. **Stable callback functions** passed to child components[^12][^13]
4. **Components with complex props** that change infrequently[^12][^13]
5. **Performance-critical** applications with many components[^12][^13]

## Error Boundaries Pattern

### Overview

Error boundaries are React components that **catch JavaScript errors** anywhere in their child component tree, log those errors, and display a fallback UI instead of crashing the entire application[^14][^15]. They act as a safety net to prevent white screen errors[^14][^15].

### Code Example

```javascript
// Class-based Error Boundary
import React from 'react';

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { 
      hasError: false, 
      error: null, 
      errorInfo: null 
    };
  }

  static getDerivedStateFromError(error) {
    // Update state to trigger fallback UI
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // Log error to console or error reporting service
    console.error('Error caught by boundary:', error, errorInfo);
    
    // Set detailed error information
    this.setState({
      error: error,
      errorInfo: errorInfo
    });

    // Log to external service (e.g., Sentry, LogRocket)
    this.logErrorToService(error, errorInfo);
  }

  logErrorToService = (error, errorInfo) => {
    // Example: Send to error monitoring service
    // errorReportingService.captureException(error, {
    //   extra: errorInfo,
    //   tags: {
    //     component: 'ErrorBoundary',
    //     version: process.env.REACT_APP_VERSION
    //   }
    // });
  };

  render() {
    if (this.state.hasError) {
      return (
        <div className="error-boundary">
          <div className="error-boundary-content">
            <h2>🚨 Something went wrong</h2>
            <p>We're sorry, but something unexpected happened.</p>
            
            {process.env.NODE_ENV === 'development' && (
              <details style={{ whiteSpace: 'pre-wrap', marginTop: '1rem' }}>
                <summary>Error Details (Development Only)</summary>
                <div style={{ marginTop: '0.5rem' }}>
                  <strong>Error:</strong> {this.state.error && this.state.error.toString()}
                  <br />
                  <strong>Stack Trace:</strong>
                  {this.state.errorInfo.componentStack}
                </div>
              </details>
            )}
            
            <div className="error-boundary-actions">
              <button 
                onClick={() => window.location.reload()}
                className="retry-button"
              >
                Reload Page
              </button>
              <button 
                onClick={() => this.setState({ hasError: false, error: null, errorInfo: null })}
                className="retry-button"
              >
                Try Again
              </button>
            </div>
          </div>
        </div>
      );
    }

    return this.props.children;
  }
}

// Functional Error Boundary Hook (using react-error-boundary library)
import { ErrorBoundary as ReactErrorBoundary } from 'react-error-boundary';

function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div className="error-fallback">
      <h2>Oops! Something went wrong</h2>
      <p>Error: {error.message}</p>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  );
}

// HOC for wrapping components with error boundaries
const withErrorBoundary = (Component, fallbackComponent) => {
  return function WrappedComponent(props) {
    return (
      <ErrorBoundary FallbackComponent={fallbackComponent}>
        <Component {...props} />
      </ErrorBoundary>
    );
  };
};

// Custom Error Boundary for specific use cases
class FeatureErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error(`Error in ${this.props.featureName}:`, error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="feature-error">
          <h3>{this.props.featureName} is temporarily unavailable</h3>
          <p>Please try refreshing the page or contact support if the problem persists.</p>
          <button onClick={() => this.setState({ hasError: false })}>
            Retry {this.props.featureName}
          </button>
        </div>
      );
    }

    return this.props.children;
  }
}

// Usage Examples
const App = () => {
  return (
    <div className="app">
      <ErrorBoundary>
        <Header />
        
        <main>
          <FeatureErrorBoundary featureName="User Dashboard">
            <UserDashboard />
          </FeatureErrorBoundary>
          
          <FeatureErrorBoundary featureName="Product Catalog">
            <ProductCatalog />
          </FeatureErrorBoundary>
        </main>
        
        <Footer />
      </ErrorBoundary>
    </div>
  );
};

// Component that might throw an error
const BuggyComponent = () => {
  const [count, setCount] = useState(0);

  if (count > 3) {
    throw new Error('Component crashed!');
  }

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
};

// Using react-error-boundary library
const AppWithLibrary = () => {
  return (
    <ReactErrorBoundary
      FallbackComponent={ErrorFallback}
      onError={(error, errorInfo) => {
        console.error('App error:', error, errorInfo);
      }}
      onReset={() => {
        // Reset app state if needed
        window.location.reload();
      }}
    >
      <div className="app">
        <BuggyComponent />
      </div>
    </ReactErrorBoundary>
  );
};
```


### Pros and Cons

**✅ Pros:**

- **Prevents crashes**: Stops errors from breaking the entire application[^14][^15]
- **Better UX**: Shows graceful error messages instead of white screens[^14][^15]
- **Error logging**: Captures errors for debugging and monitoring[^14][^15]
- **Recovery options**: Allows users to retry or reload components[^14][^15]

**⚠️ Cons:**

- **Limited scope**: Only catches errors in component tree, not in event handlers[^15]
- **Class components**: Requires class components (though libraries provide hooks)[^15]
- **No async errors**: Cannot catch errors in async code or promises[^15]
- **Additional complexity**: Adds more components to manage[^15]


### Best Use Cases

1. **Production applications** where stability is crucial[^14][^15]
2. **Third-party components** that might fail unexpectedly[^14][^15]
3. **Feature isolation** to prevent one failing feature from breaking others[^14][^15]
4. **Error monitoring** and user feedback collection[^14][^15]
5. **Gradual rollouts** of new features with error containment[^14][^15]

## Lazy Loading and Code Splitting

### Overview

Lazy loading is a technique that **delays loading components until they're actually needed**, improving initial load times and reducing bundle sizes[^16][^17]. React provides built-in support through `React.lazy()` and `Suspense`[^16][^17].

### Code Example

```javascript
// Basic lazy loading with React.lazy
import React, { Suspense, lazy, useState } from 'react';

// Lazy load components
const Dashboard = lazy(() => import('./components/Dashboard'));
const UserProfile = lazy(() => import('./components/UserProfile'));
const Settings = lazy(() => import('./components/Settings'));

// Lazy load with delay simulation
const AdminPanel = lazy(() => 
  import('./components/AdminPanel').then(module => ({
    default: module.AdminPanel
  }))
);

// Lazy load with error handling
const ReportsModule = lazy(() => 
  import('./components/Reports').catch(error => {
    console.error('Failed to load Reports module:', error);
    return import('./components/ErrorFallback');
  })
);

// Loading component
const LoadingSpinner = () => (
  <div className="loading-spinner">
    <div className="spinner"></div>
    <p>Loading...</p>
  </div>
);

// Skeleton loading component
const SkeletonLoader = () => (
  <div className="skeleton-loader">
    <div className="skeleton-header"></div>
    <div className="skeleton-content">
      <div className="skeleton-line"></div>
      <div className="skeleton-line"></div>
      <div className="skeleton-line short"></div>
    </div>
  </div>
);

// Route-based lazy loading
import { BrowserRouter as Router, Routes, Route, Link } from 'react-router-dom';

const HomePage = lazy(() => import('./pages/Home'));
const AboutPage = lazy(() => import('./pages/About'));
const ContactPage = lazy(() => import('./pages/Contact'));

// Conditional lazy loading
const ConditionalLazyComponent = () => {
  const [showAdvanced, setShowAdvanced] = useState(false);
  const [AdvancedFeature, setAdvancedFeature] = useState(null);

  const loadAdvancedFeature = async () => {
    if (!AdvancedFeature) {
      const module = await import('./components/AdvancedFeature');
      setAdvancedFeature(() => module.default);
    }
    setShowAdvanced(true);
  };

  return (
    <div>
      <h2>Basic Features</h2>
      <p>This content is always loaded.</p>
      
      <button onClick={loadAdvancedFeature}>
        Load Advanced Features
      </button>
      
      {showAdvanced && AdvancedFeature && (
        <Suspense fallback={<LoadingSpinner />}>
          <AdvancedFeature />
        </Suspense>
      )}
    </div>
  );
};

// Intersection Observer lazy loading
const LazySection = ({ children, threshold = 0.1 }) => {
  const [isVisible, setIsVisible] = useState(false);
  const [hasLoaded, setHasLoaded] = useState(false);
  const ref = useRef();

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting && !hasLoaded) {
          setIsVisible(true);
          setHasLoaded(true);
        }
      },
      { threshold }
    );

    if (ref.current) {
      observer.observe(ref.current);
    }

    return () => observer.disconnect();
  }, [threshold, hasLoaded]);

  return (
    <div ref={ref}>
      {isVisible ? children : <SkeletonLoader />}
    </div>
  );
};

// Main App component
const App = () => {
  const [currentTab, setCurrentTab] = useState('dashboard');

  return (
    <Router>
      <div className="app">
        <nav>
          <Link to="/">Home</Link>
          <Link to="/about">About</Link>
          <Link to="/contact">Contact</Link>
        </nav>

        <main>
          <Suspense fallback={<LoadingSpinner />}>
            <Routes>
              <Route path="/" element={<HomePage />} />
              <Route path="/about" element={<AboutPage />} />
              <Route path="/contact" element={<ContactPage />} />
            </Routes>
          </Suspense>

          {/* Tab-based lazy loading */}
          <div className="tabs">
            <button 
              onClick={() => setCurrentTab('dashboard')}
              className={currentTab === 'dashboard' ? 'active' : ''}
            >
              Dashboard
            </button>
            <button 
              onClick={() => setCurrentTab('profile')}
              className={currentTab === 'profile' ? 'active' : ''}
            >
              Profile
            </button>
            <button 
              onClick={() => setCurrentTab('settings')}
              className={currentTab === 'settings' ? 'active' : ''}
            >
              Settings
            </button>
          </div>

          <Suspense fallback={<SkeletonLoader />}>
            {currentTab === 'dashboard' && <Dashboard />}
            {currentTab === 'profile' && <UserProfile />}
            {currentTab === 'settings' && <Settings />}
          </Suspense>

          {/* Conditional lazy loading */}
          <ConditionalLazyComponent />

          {/* Scroll-based lazy loading */}
          <LazySection>
            <Suspense fallback={<LoadingSpinner />}>
              <ReportsModule />
            </Suspense>
          </LazySection>
        </main>
      </div>
    </Router>
  );
};

// Preloading utility
const preloadComponent = (componentImport) => {
  const componentImporter = typeof componentImport === 'function' 
    ? componentImport 
    : () => componentImport;
  
  return componentImporter();
};

// Usage: Preload components on hover or user interaction
const NavigationWithPreload = () => {
  const handleMouseEnter = (route) => {
    switch (route) {
      case 'dashboard':
        preloadComponent(() => import('./components/Dashboard'));
        break;
      case 'profile':
        preloadComponent(() => import('./components/UserProfile'));
        break;
      default:
        break;
    }
  };

  return (
    <nav>
      <Link 
        to="/dashboard" 
        onMouseEnter={() => handleMouseEnter('dashboard')}
      >
        Dashboard
      </Link>
      <Link 
        to="/profile" 
        onMouseEnter={() => handleMouseEnter('profile')}
      >
        Profile
      </Link>
    </nav>
  );
};
```


### Pros and Cons

**✅ Pros:**

- **Faster initial load**: Smaller initial bundle size[^16][^17]
- **Better performance**: Components load only when needed[^16][^17]
- **Reduced bandwidth**: Less data transferred upfront[^16][^17]
- **Improved UX**: Faster time-to-interactive[^16][^17]

**⚠️ Cons:**

- **Loading delays**: Users may experience delays when accessing lazy-loaded content[^16][^17]
- **Complexity**: Requires careful planning of what to lazy load[^16][^17]
- **SEO considerations**: May affect search engine crawling[^16][^17]
- **Network dependencies**: Requires additional network requests[^16][^17]


### Best Use Cases

1. **Large applications** with many routes or features[^16][^17]
2. **Admin panels** or dashboard applications[^16][^17]
3. **Modal dialogs** and popup components[^16][^17]
4. **Third-party widgets** and integrations[^16][^17]
5. **Heavy visualizations** like charts or maps[^16][^17]

## Component Composition Pattern

### Overview

Component composition is the practice of **combining smaller components** to build more complex UIs[^18][^19]. This pattern promotes reusability, maintainability, and follows the single responsibility principle[^18][^19].

### Code Example

```javascript
// Basic composition with children
const Card = ({ children, className = '' }) => (
  <div className={`card ${className}`}>
    {children}
  </div>
);

const CardHeader = ({ children, className = '' }) => (
  <div className={`card-header ${className}`}>
    {children}
  </div>
);

const CardBody = ({ children, className = '' }) => (
  <div className={`card-body ${className}`}>
    {children}
  </div>
);

const CardFooter = ({ children, className = '' }) => (
  <div className={`card-footer ${className}`}>
    {children}
  </div>
);

// Props-based composition
const Button = ({ 
  variant = 'primary', 
  size = 'medium', 
  disabled = false, 
  children, 
  onClick,
  ...props 
}) => (
  <button
    className={`btn btn-${variant} btn-${size} ${disabled ? 'disabled' : ''}`}
    onClick={onClick}
    disabled={disabled}
    {...props}
  >
    {children}
  </button>
);

// Composition with render props
const FormField = ({ 
  label, 
  error, 
  required = false, 
  children,
  renderInput,
  renderLabel,
  renderError 
}) => (
  <div className="form-field">
    {renderLabel ? (
      renderLabel({ label, required })
    ) : (
      <label className="form-label">
        {label}
        {required && <span className="required">*</span>}
      </label>
    )}
    
    {renderInput ? renderInput() : children}
    
    {error && (
      renderError ? renderError({ error }) : (
        <span className="form-error">{error}</span>
      )
    )}
  </div>
);

// Higher-order composition
const withLoadingState = (Component) => {
  return function WithLoadingState({ loading, ...props }) {
    if (loading) {
      return <div className="loading">Loading...</div>;
    }
    return <Component {...props} />;
  };
};

// Layout composition
const Layout = ({ children, sidebar, header, footer }) => (
  <div className="layout">
    {header && <header className="layout-header">{header}</header>}
    <div className="layout-content">
      {sidebar && <aside className="layout-sidebar">{sidebar}</aside>}
      <main className="layout-main">{children}</main>
    </div>
    {footer && <footer className="layout-footer">{footer}</footer>}
  </div>
);

// Slot-based composition
const Modal = ({ 
  isOpen, 
  onClose, 
  title, 
  children, 
  actions,
  size = 'medium' 
}) => {
  if (!isOpen) return null;

  return (
    <div className="modal-overlay" onClick={onClose}>
      <div 
        className={`modal modal-${size}`} 
        onClick={(e) => e.stopPropagation()}
      >
        <div className="modal-header">
          <h2>{title}</h2>
          <button className="modal-close" onClick={onClose}>×</button>
        </div>
        <div className="modal-body">
          {children}
        </div>
        {actions && (
          <div className="modal-footer">
            {actions}
          </div>
        )}
      </div>
    </div>
  );
};

// Function composition for utilities
const compose = (...functions) => (args) => 
  functions.reduceRight((arg, fn) => fn(arg), args);

const withAuthentication = (Component) => (props) => {
  const { user } = useAuth();
  if (!user) return <LoginRequired />;
  return <Component {...props} />;
};

const withErrorBoundary = (Component) => (props) => (
  <ErrorBoundary>
    <Component {...props} />
  </ErrorBoundary>
);

const withLoadingState = (Component) => ({ loading, ...props }) => {
  if (loading) return <Loading />;
  return <Component {...props} />;
};

// Usage examples
const UserProfile = () => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [showEditModal, setShowEditModal] = useState(false);

  return (
    <Layout
      header={<h1>User Profile</h1>}
      sidebar={<ProfileNavigation />}
    >
      <Card className="profile-card">
        <CardHeader>
          <h2>Profile Information</h2>
          <Button 
            variant="secondary" 
            onClick={() => setShowEditModal(true)}
          >
            Edit Profile
          </Button>
        </CardHeader>
        
        <CardBody>
          <FormField 
            label="Full Name" 
            renderInput={() => <span>{user?.name}</span>}
          />
          <FormField 
            label="Email" 
            renderInput={() => <span>{user?.email}</span>}
          />
          <FormField 
            label="Phone" 
            renderInput={() => <span>{user?.phone}</span>}
          />
        </CardBody>
        
        <CardFooter>
          <Button variant="primary">Save Changes</Button>
          <Button variant="secondary">Cancel</Button>
        </CardFooter>
      </Card>

      <Modal
        isOpen={showEditModal}
        onClose={() => setShowEditModal(false)}
        title="Edit Profile"
        size="large"
        actions={
          <>
            <Button 
              variant="primary" 
              onClick={() => setShowEditModal(false)}
            >
              Save
            </Button>
            <Button 
              variant="secondary" 
              onClick={() => setShowEditModal(false)}
            >
              Cancel
            </Button>
          </>
        }
      >
        <ProfileEditForm user={user} />
      </Modal>
    </Layout>
  );
};

// Enhanced component with multiple HOCs
const EnhancedUserProfile = compose(
  withErrorBoundary,
  withAuthentication,
  withLoadingState
)(UserProfile);

// Flexible list composition
const DataList = ({ 
  items, 
  renderItem, 
  renderEmpty, 
  renderHeader,
  className = '' 
}) => (
  <div className={`data-list ${className}`}>
    {renderHeader && renderHeader()}
    
    {items.length === 0 ? (
      renderEmpty ? renderEmpty() : <p>No items found</p>
    ) : (
      <ul>
        {items.map((item, index) => (
          <li key={item.id || index}>
            {renderItem(item, index)}
          </li>
        ))}
      </ul>
    )}
  </div>
);

// Usage of flexible list
const ProductList = ({ products }) => (
  <DataList
    items={products}
    renderHeader={() => <h2>Products</h2>}
    renderItem={(product) => (
      <Card>
        <CardBody>
          <h3>{product.name}</h3>
          <p>${product.price}</p>
        </CardBody>
      </Card>
    )}
    renderEmpty={() => <p>No products available</p>}
  />
);
```


### Pros and Cons

**✅ Pros:**

- **Reusability**: Components can be reused in different contexts[^18][^19]
- **Maintainability**: Smaller, focused components are easier to maintain[^18][^19]
- **Testability**: Individual components can be tested independently[^18][^19]
- **Flexibility**: Easy to compose different combinations[^18][^19]

**⚠️ Cons:**

- **Complexity**: Can lead to over-engineering if not used thoughtfully[^18][^19]
- **Performance**: Deep nesting can impact performance[^18][^19]
- **Learning curve**: Requires understanding of composition patterns[^18][^19]


### Best Use Cases

1. **UI component libraries** with consistent design systems[^18][^19]
2. **Form builders** with reusable field components[^18][^19]
3. **Layout systems** with flexible arrangements[^18][^19]
4. **Data visualization** with composable chart components[^18][^19]
5. **Content management** with flexible page builders[^18][^19]

## State Management Patterns

### Overview

State management patterns in React help organize and manage application state efficiently as applications grow in complexity[^20][^21]. Different patterns are suitable for different scenarios and application sizes[^20][^21].

### Code Example

```javascript
// Local State with useState
const Counter = () => {
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);

  const increment = () => setCount(prev => prev + step);
  const decrement = () => setCount(prev => prev - step);
  const reset = () => setCount(0);

  return (
    <div>
      <h2>Count: {count}</h2>
      <input 
        type="number" 
        value={step} 
        onChange={(e) => setStep(Number(e.target.value))}
        placeholder="Step"
      />
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
};

// Complex State with useReducer
const todoReducer = (state, action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        ...state,
        todos: [...state.todos, {
          id: Date.now(),
          text: action.payload,
          completed: false
        }]
      };
    case 'TOGGLE_TODO':
      return {
        ...state,
        todos: state.todos.map(todo =>
          todo.id === action.payload
            ? { ...todo, completed: !todo.completed }
            : todo
        )
      };
    case 'DELETE_TODO':
      return {
        ...state,
        todos: state.todos.filter(todo => todo.id !== action.payload)
      };
    case 'SET_FILTER':
      return {
        ...state,
        filter: action.payload
      };
    default:
      return state;
  }
};

const TodoApp = () => {
  const [state, dispatch] = useReducer(todoReducer, {
    todos: [],
    filter: 'all'
  });

  const [inputValue, setInputValue] = useState('');

  const addTodo = (e) => {
    e.preventDefault();
    if (inputValue.trim()) {
      dispatch({ type: 'ADD_TODO', payload: inputValue });
      setInputValue('');
    }
  };

  const filteredTodos = state.todos.filter(todo => {
    if (state.filter === 'completed') return todo.completed;
    if (state.filter === 'active') return !todo.completed;
    return true;
  });

  return (
    <div>
      <form onSubmit={addTodo}>
        <input
          value={inputValue}
          onChange={(e) => setInputValue(e.target.value)}
          placeholder="Add a todo..."
        />
        <button type="submit">Add</button>
      </form>

      <div>
        <button onClick={() => dispatch({ type: 'SET_FILTER', payload: 'all' })}>
          All
        </button>
        <button onClick={() => dispatch({ type: 'SET_FILTER', payload: 'active' })}>
          Active
        </button>
        <button onClick={() => dispatch({ type: 'SET_FILTER', payload: 'completed' })}>
          Completed
        </button>
      </div>

      <ul>
        {filteredTodos.map(todo => (
          <li key={todo.id}>
            <span 
              style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}
              onClick={() => dispatch({ type: 'TOGGLE_TODO', payload: todo.id })}
            >
              {todo.text}
            </span>
            <button onClick={() => dispatch({ type: 'DELETE_TODO', payload: todo.id })}>
              Delete
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
};

// Global State with Context + useReducer
const AppContext = createContext();

const appReducer = (state, action) => {
  switch (action.type) {
    case 'SET_USER':
      return { ...state, user: action.payload };
    case 'SET_THEME':
      return { ...state, theme: action.payload };
    case 'SET_LOADING':
      return { ...state, loading: action.payload };
    case 'SET_ERROR':
      return { ...state, error: action.payload };
    case 'ADD_NOTIFICATION':
      return {
        ...state,
        notifications: [...state.notifications, action.payload]
      };
    case 'REMOVE_NOTIFICATION':
      return {
        ...state,
        notifications: state.notifications.filter(
          notification => notification.id !== action.payload
        )
      };
    default:
      return state;
  }
};

const AppProvider = ({ children }) => {
  const [state, dispatch] = useReducer(appReducer, {
    user: null,
    theme: 'light',
    loading: false,
    error: null,
    notifications: []
  });

  // Action creators
  const actions = {
    setUser: (user) => dispatch({ type: 'SET_USER', payload: user }),
    setTheme: (theme) => dispatch({ type: 'SET_THEME', payload: theme }),
    setLoading: (loading) => dispatch({ type: 'SET_LOADING', payload: loading }),
    setError: (error) => dispatch({ type: 'SET_ERROR', payload: error }),
    addNotification: (notification) => dispatch({
      type: 'ADD_NOTIFICATION',
      payload: { ...notification, id: Date.now() }
    }),
    removeNotification: (id) => dispatch({
      type: 'REMOVE_NOTIFICATION',
      payload: id
    })
  };

  return (
    <AppContext.Provider value={{ state, actions }}>
      {children}
    </AppContext.Provider>
  );
};

// Custom hook for using app context
const useApp = () => {
  const context = useContext(AppContext);
  if (!context) {
    throw new Error('useApp must be used within an AppProvider');
  }
  return context;
};

// State management with custom hooks
const useApi = (url) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  const fetchData = useCallback(async () => {
    try {
      setLoading(true);
      setError(null);
      const response = await fetch(url);
      if (!response.ok) throw new Error('Failed to fetch');
      const result = await response.json();
      setData(result);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  }, [url]);

  useEffect(() => {
    fetchData();
  }, [fetchData]);

  return { data, loading, error, refetch: fetchData };
};

// State synchronization pattern
const useStateSync = (key, initialValue) => {
  const [state, setState] = useState(() => {
    const saved = localStorage.getItem(key);
    return saved ? JSON.parse(saved) : initialValue;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(state));
  }, [key, state]);

  // Sync across tabs
  useEffect(() => {
    const handleStorageChange = (e) => {
      if (e.key === key) {
        setState(JSON.parse(e.newValue));
      }
    };

    window.addEventListener('storage', handleStorageChange);
    return () => window.removeEventListener('storage', handleStorageChange);
  }, [key]);

  return [state, setState];
};

// Optimistic updates pattern
const useOptimisticUpdate = (apiCall) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  const execute = useCallback(async (optimisticData, ...args) => {
    const previousData = data;
    
    // Apply optimistic update
    setData(optimisticData);
    setLoading(true);
    setError(null);

    try {
      const result = await apiCall(...args);
      setData(result);
    } catch (err) {
      // Revert on error
      setData(previousData);
      setError(err.message);
    } finally {
      setLoading(false);
    }
  }, [apiCall, data]);

  return { data, loading, error, execute };
};

// Usage examples
const UserProfile = () => {
  const { state, actions } = useApp();
  const { data: userData, loading, error } = useApi('/api/user');
  const [preferences, setPreferences] = useStateSync('userPreferences', {
    notifications: true,
    theme: 'light'
  });

  const updateUser = useOptimisticUpdate(async (newData) => {
    const response = await fetch('/api/user', {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(newData)
    });
    return response.json();
  });

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      <h1>User Profile</h1>
      <p>Theme: {state.theme}</p>
      <button onClick={() => actions.setTheme(
        state.theme === 'light' ? 'dark' : 'light'
      )}>
        Toggle Theme
      </button>
      
      <div>
        <h2>Preferences</h2>
        <label>
          <input
            type="checkbox"
            checked={preferences.notifications}
            onChange={(e) => setPreferences({
              ...preferences,
              notifications: e.target.checked
            })}
          />
          Enable Notifications
        </label>
      </div>
    </div>
  );
};
```


### Pros and Cons

**✅ Pros:**

- **Scalability**: Different patterns scale for different application sizes[^20][^21]
- **Predictability**: Clear state flow and updates[^20][^21]
- **Debugging**: Easier to track state changes[^20][^21]
- **Testing**: Isolated state logic is easier to test[^20][^21]

**⚠️ Cons:**

- **Complexity**: Can add unnecessary complexity for simple apps[^20][^21]
- **Learning curve**: Requires understanding of different patterns[^20][^21]
- **Boilerplate**: Some patterns require more setup code[^20][^21]


### Best Use Cases

1. **Local state**: Simple component-specific state with `useState`[^20][^21]
2. **Complex local state**: Form handling, wizards with `useReducer`[^20][^21]
3. **Global state**: App-wide state like auth, theme with Context[^20][^21]
4. **Large applications**: Complex state management with Redux or Zustand[^20][^21]
5. **Real-time data**: WebSocket connections and live updates[^20][^21]

## Performance Optimization Patterns

### Overview

Performance optimization patterns in React focus on **minimizing unnecessary re-renders**, reducing bundle sizes, and improving runtime performance[^22][^23]. These patterns are essential for building fast, responsive applications[^22][^23].

### Code Example

```javascript
// Memoization patterns
import React, { memo, useMemo, useCallback, useState, useEffect } from 'react';

// React.memo with custom comparison
const ProductCard = memo(({ product, onAddToCart, onRemove }) => {
  console.log(`ProductCard rendered: ${product.name}`);
  
  return (
    <div className="product-card">
      <img src={product.image} alt={product.name} />
      <h3>{product.name}</h3>
      <p>${product.price}</p>
      <button onClick={() => onAddToCart(product)}>Add to Cart</button>
      <button onClick={() => onRemove(product.id)}>Remove</button>
    </div>
  );
}, (prevProps, nextProps) => {
  // Custom comparison - only re-render if product data changed
  return (
    prevProps.product.id === nextProps.product.id &&
    prevProps.product.name === nextProps.product.name &&
    prevProps.product.price === nextProps.product.price &&
    prevProps.product.image === nextProps.product.image
  );
});

// Virtual scrolling for large lists
const VirtualizedList = ({ items, itemHeight = 50, containerHeight = 400 }) => {
  const [scrollTop, setScrollTop] = useState(0);
  
  const visibleStart = Math.floor(scrollTop / itemHeight);
  const visibleEnd = Math.min(
    visibleStart + Math.ceil(containerHeight / itemHeight) + 1,
    items.length
  );
  
  const visibleItems = items.slice(visibleStart, visibleEnd);
  const totalHeight = items.length * itemHeight;
  const offsetY = visibleStart * itemHeight;
  
  return (
    <div
      style={{ height: containerHeight, overflow: 'auto' }}
      onScroll={(e) => setScrollTop(e.target.scrollTop)}
    >
      <div style={{ height: totalHeight, position: 'relative' }}>
        <div style={{ transform: `translateY(${offsetY}px)` }}>
          {visibleItems.map((item, index) => (
            <div
              key={visibleStart + index}
              style={{ height: itemHeight }}
            >
              {item.name}
            </div>
          ))}
        </div>
      </div>
    </div>
  );
};

// Debounced search with performance optimization
const SearchComponent = ({ onSearch }) => {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);

  // Debounced search function
  const debouncedSearch = useMemo(
    () => debounce(async (searchQuery) => {
      if (!searchQuery.trim()) {
        setResults([]);
        return;
      }

      setLoading(true);
      try {
        const response = await fetch(`/api/search?q=${searchQuery}`);
        const data = await response.json();
        setResults(data);
      } catch (error) {
        console.error('Search error:', error);
      } finally {
        setLoading(false);
      }
    }, 300),
    []
  );

  useEffect(() => {
    debouncedSearch(query);
    return () => debouncedSearch.cancel();
  }, [query, debouncedSearch]);

  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search..."
      />
      {loading && <div>Searching...</div>}
      <ul>
        {results.map(result => (
          <li key={result.id}>{result.title}</li>
        ))}
      </ul>
    </div>
  );
};

// Optimized event handling
const OptimizedEventHandler = () => {
  const [items, setItems] = useState([]);
  
  // Single event handler for all items
  const handleItemClick = useCallback((e) => {
    const itemId = e.target.dataset.itemId;
    if (itemId) {
      console.log(`Item ${itemId} clicked`);
      // Handle item click
    }
  }, []);

  return (
    <div onClick={handleItemClick}>
      {items.map(item => (
        <div key={item.id} data-item-id={item.id}>
          {item.name}
        </div>
      ))}
    </div>
  );
};

// Image lazy loading with Intersection Observer
const LazyImage = ({ src, alt, className }) => {
  const [imageSrc, setImageSrc] = useState(null);
  const [isLoaded, setIsLoaded] = useState(false);
  const imgRef = useRef();

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setImageSrc(src);
          observer.disconnect();
        }
      },
      { threshold: 0.1 }
    );

    if (imgRef.current) {
      observer.observe(imgRef.current);
    }

    return () => observer.disconnect();
  }, [src]);

  return (
    <div ref={imgRef} className={className}>
      {imageSrc && (
        <img
          src={imageSrc}
          alt={alt}
          onLoad={() => setIsLoaded(true)}
          style={{ 
            opacity: isLoaded ? 1 : 0,
            transition: 'opacity 0.3s'
          }}
        />
      )}
    </div>
  );
};

// Bundle optimization with dynamic imports
const DynamicComponentLoader = ({ componentName }) => {
  const [Component, setComponent] = useState(null);
  const [loading, setLoading] = useState(false);

  const loadComponent = useCallback(async () => {
    setLoading(true);
    try {
      const module = await import(`./components/${componentName}`);
      setComponent(() => module.default);
    } catch (error) {
      console.error(`Failed to load component: ${componentName}`, error);
    } finally {
      setLoading(false);
    }
  }, [componentName]);

  useEffect(() => {
    loadComponent();
  }, [loadComponent]);

  if (loading) return <div>Loading component...</div>;
  if (!Component) return <div>Component not found</div>;

  return <Component />;
};

// Performance monitoring hook
const usePerformanceMonitor = (componentName) => {
  const renderStart = useRef(performance.now());
  const [metrics, setMetrics] = useState({
    renderTime: 0,
    renderCount: 0
  });

  useEffect(() => {
    const renderTime = performance.now() - renderStart.current;
    setMetrics(prev => ({
      renderTime,
      renderCount: prev.renderCount + 1
    }));
  });

  useEffect(() => {
    renderStart.current = performance.now();
  });

  // Log performance metrics in development
  useEffect(() => {
    if (process.env.NODE_ENV === 'development') {
      console.log(`${componentName} - Render #${metrics.renderCount}: ${metrics.renderTime.toFixed(2)}ms`);
    }
  }, [componentName, metrics]);

  return metrics;
};

// Optimized list rendering with keys
const OptimizedList = ({ items, onItemUpdate }) => {
  const [filter, setFilter] = useState('');
  
  // Memoize filtered items
  const filteredItems = useMemo(() => {
    return items.filter(item => 
      item.name.toLowerCase().includes(filter.toLowerCase())
    );
  }, [items, filter]);

  // Memoize item update handlers
  const handleItemUpdate = useCallback((id, updates) => {
    onItemUpdate(id, updates);
  }, [onItemUpdate]);

  return (
    <div>
      <input
        type="text"
        value={filter}
        onChange={(e) => setFilter(e.target.value)}
        placeholder="Filter items..."
      />
      
      <div>
        {filteredItems.map(item => (
          <OptimizedListItem
            key={item.id}
            item={item}
            onUpdate={handleItemUpdate}
          />
        ))}
      </div>
    </div>
  );
};

const OptimizedListItem = memo(({ item, onUpdate }) => {
  const metrics = usePerformanceMonitor(`ListItem-${item.id}`);
  
  const handleUpdate = useCallback((updates) => {
    onUpdate(item.id, updates);
  }, [item.id, onUpdate]);

  return (
    <div className="list-item">
      <span>{item.name}</span>
      <button onClick={() => handleUpdate({ status: 'completed' })}>
        Complete
      </button>
    </div>
  );
});

// Utility function for debouncing
function debounce(func, wait) {
  let timeout;
  
  const debounced = (...args) => {
    clearTimeout(timeout);
    timeout = setTimeout(() => func.apply(this, args), wait);
  };
  
  debounced.cancel = () => {
    clearTimeout(timeout);
  };
  
  return debounced;
}

// Main component demonstrating all patterns
const PerformanceOptimizedApp = () => {
  const [products, setProducts] = useState([]);
  const [cart, setCart] = useState([]);
  const metrics = usePerformanceMonitor('App');

  const addToCart = useCallback((product) => {
    setCart(prev => [...prev, product]);
  }, []);

  const removeProduct = useCallback((id) => {
    setProducts(prev => prev.filter(p => p.id !== id));
  }, []);

  return (
    <div>
      <h1>Performance Optimized Store</h1>
      
      <SearchComponent onSearch={console.log} />
      
      <div className="products">
        {products.map(product => (
          <ProductCard
            key={product.id}
            product={product}
            onAddToCart={addToCart}
            onRemove={removeProduct}
          />
        ))}
      </div>

      <VirtualizedList
        items={cart}
        itemHeight={60}
        containerHeight={300}
      />

      {process.env.NODE_ENV === 'development' && (
        <div className="performance-metrics">
          <h3>Performance Metrics</h3>
          <p>Render Count: {metrics.renderCount}</p>
          <p>Last Render Time: {metrics.renderTime.toFixed(2)}ms</p>
        </div>
      )}
    </div>
  );
};
```


### Pros and Cons

**✅ Pros:**

- **Improved performance**: Faster rendering and reduced re-renders[^22][^23]
- **Better UX**: Smoother interactions and shorter load times[^22][^23]
- **Scalability**: Applications remain performant as they grow[^22][^23]
- **Resource efficiency**: Reduced memory and CPU usage[^22][^23]

**⚠️ Cons:**

- **Complexity**: Adds development and maintenance overhead[^22][^23]
- **Premature optimization**: Can lead to unnecessary complexity[^22][^23]
- **Memory trade-offs**: Memoization uses more memory[^22][^23]
- **Debugging difficulty**: Optimized code can be harder to debug[^22][^23]


### Best Use Cases

1. **Large lists** with many items that need virtual scrolling[^22][^23]
2. **Data-heavy applications** with complex state and frequent updates[^22][^23]
3. **Real-time applications** where performance is critical[^22][^23]
4. **Mobile applications** where resources are limited[^22][^23]
5. **Component libraries** that need to be performant across different uses[^22][^23]

## Best Practices and Guidelines

### General Principles

1. **Start Simple**: Begin with basic patterns and add complexity only when needed[^2][^1]
2. **Measure Performance**: Use React DevTools Profiler to identify bottlenecks before optimizing[^23][^24]
3. **Follow Convention**: Use established naming conventions and folder structures[^25][^26]
4. **Test Thoroughly**: Each pattern should be tested independently and in integration[^4][^5]

### Pattern Selection Guidelines

- **Use Container/Presentational** for components that need data fetching and complex logic[^2][^1]
- **Use Custom Hooks** for reusable stateful logic across components[^1][^7]
- **Use HOCs** for cross-cutting concerns like authentication and logging[^1][^8]
- **Use Render Props** for flexible component composition[^1][^8]
- **Use Context** for global state that changes infrequently[^10][^11]
- **Use Memoization** for expensive calculations and frequently re-rendering components[^12][^13]


### Performance Considerations

1. **Avoid Premature Optimization**: Don't optimize until you have performance issues[^12][^23]
2. **Use React DevTools**: Profile your application regularly to identify bottlenecks[^23][^24]
3. **Implement Code Splitting**: Use lazy loading for routes and heavy components[^16][^17]
4. **Optimize Bundle Size**: Use tree shaking and analyze bundle composition[^16][^17]
5. **Monitor Key Metrics**: Track render times, bundle sizes, and user interactions[^23][^24]

### Scalability Guidelines

1. **Modular Architecture**: Organize code by features rather than file types[^27][^25]
2. **Consistent Patterns**: Use the same patterns throughout your application[^27][^25]
3. **Documentation**: Document pattern usage and architectural decisions[^27][^25]
4. **Team Alignment**: Ensure all team members understand the chosen patterns[^27][^25]
5. **Regular Reviews**: Periodically review and refactor patterns as the application grows[^27][^25]

## Conclusion

React design patterns provide a solid foundation for building scalable, maintainable, and performant web applications[^2][^1]. By understanding and implementing these patterns appropriately, developers can create applications that are not only functional but also maintainable and efficient[^2][^1].

The key to successful pattern implementation is **choosing the right pattern for the right use case**[^1]. Start with simpler patterns and gradually introduce more complex ones as your application grows[^2]. Remember that patterns are tools to solve specific problems, not solutions to be applied everywhere[^1].

As you build larger applications, these patterns will become invaluable for maintaining code quality, enabling team collaboration, and ensuring your application can scale effectively[^3][^25]. The investment in learning and implementing these patterns will pay dividends in the long-term maintainability and success of your React applications[^2][^1].

<div style="text-align: center">⁂</div>

[^1]: https://www.designrush.com/best-designs/websites/trends/react-design-patterns

[^2]: https://www.uxpin.com/studio/blog/react-design-patterns/

[^3]: https://www.kellton.com/kellton-tech-blog/react-design-patterns-dominating-react-development-lifecycle

[^4]: https://refine.dev/blog/react-design-patterns/

[^5]: https://codeparrot.ai/blogs/react-design-patterns-best-practices-for-scalable-applicationsreact-design-patterns-best-practices-for-scalable-applications

[^6]: https://hackernoon.com/every-popular-react-pattern-you-should-know-about

[^7]: https://javascriptpatterns.vercel.app/patterns/react-patterns/hooks-pattern

[^8]: https://dev.to/elizabethsobiya/react-design-patterns-thatll-make-you-a-better-dev-1l52

[^9]: https://dev.to/muhammadazfaraslam/react-design-patterns-compound-component-pattern-2p0a

[^10]: https://www.loginradius.com/blog/engineering/react-context-api-guide-with-example

[^11]: https://www.linkedin.com/pulse/context-api-react-advanced-use-cases-patterns-jon-howard-bexze

[^12]: https://dev.to/hexshift/how-to-optimize-react-apps-with-memoization-and-custom-comparison-functions-32h1

[^13]: https://blog.stackademic.com/memoization-in-react-using-hooks-3e83712932a9?gi=fc63cf512375

[^14]: https://builtin.com/software-engineering-perspectives/react-error-boundary

[^15]: https://legacy.reactjs.org/docs/error-boundaries.html

[^16]: https://dev.to/shyam0118/react-lazy-loading-boosting-performance-with-code-splitting-45lg

[^17]: https://www.browserstack.com/guide/lazy-loading-in-react

[^18]: https://javascript.plainenglish.io/react-component-composition-top-5-best-practices-4713fc358b7b?gi=42cdc8264fd1

[^19]: https://www.developerway.com/posts/components-composition-how-to-get-it-right

[^20]: https://blog.logrocket.com/modern-guide-react-state-patterns/

[^21]: https://dev.to/imabhinavdev/state-management-in-react-a-comprehensive-guide-1po1

[^22]: https://dev.to/ra1nbow1/supercharging-react-performance-best-tips-and-tools-4ff0

[^23]: https://hackernoon.com/best-practices-for-react-performance-optimization

[^24]: https://www.sitepoint.com/react-performance-optimization/

[^25]: https://www.geeksforgeeks.org/reactjs/react-architecture-pattern-and-best-practices/

[^26]: https://maybe.works/blogs/react-architecture

[^27]: https://dev.to/drruvari/building-scalable-react-applications-design-patterns-and-architecture-39a0

[^28]: https://clouddevs.com/react/building-scalable-apps/

[^29]: https://dev.to/sofia-tech/building-scalable-web-applications-with-react-4nme

[^30]: https://javascript.plainenglish.io/building-scalable-react-applications-best-practices-and-strategies-3fbbc6f3099c?gi=70d5099f8161

[^31]: https://blog.bitsrc.io/react-design-patterns-for-2024-5f2696868222?gi=5bd73866f586

[^32]: https://www.geeksforgeeks.org/react-design-patterns/

[^33]: https://aglowiditsolutions.com/blog/react-design-patterns/

[^34]: https://www.youtube.com/watch?v=Lbj3vmp8spI

[^35]: https://www.commencis.com/thoughts/mastering-react-design-patterns-for-scalable-apps/

[^36]: https://www.linkedin.com/pulse/top-react-design-patterns-dominating-development-2024-neil-johnson-iccgc

[^37]: https://www.patterns.dev/react/

[^38]: https://encantotek.com/proven-react-design-patterns-practical-solutions-for-scalable-ui-development/

[^39]: https://www.javacodegeeks.com/2024/04/master-react-in-2024-4-design-patterns-you-need-to-know.html

[^40]: https://magicui.design/blog/react-design-patterns

[^41]: https://www.patterns.dev/react/hooks-pattern/

[^42]: https://react.dev/reference/react/hooks

[^43]: https://legacy.reactjs.org/docs/legacy-context.html

[^44]: https://www.reddit.com/r/reactjs/comments/1fgv7lg/react_design_patterns_instance_hook_pattern/

[^45]: https://semaphoreci.com/blog/context-api-react

[^46]: https://dev.to/fpaghar/react-component-design-patterns-part-1-5f0g

[^47]: https://javascript.plainenglish.io/react-hook-patterns-and-best-practices-78c419cd76fe?gi=087484dcaae2

[^48]: https://dev.to/luqmanshaban/react-context-api-a-step-by-step-guide-i1i

[^49]: https://dev.to/ricardolmsilva/composition-pattern-in-react-28mj

[^50]: https://dev.to/tene/ultimate-react-hooks-guide-best-practices-examples-3h52

[^51]: https://dev.to/kurmivivek295/contextprovider-pattern-4m1c

[^52]: https://legacy.reactjs.org/docs/composition-vs-inheritance.html

[^53]: https://blog.stackademic.com/understanding-the-react-hook-pattern-and-its-implementation-in-plain-javascript-9f602a8bafe7?gi=d4ddc19b099b

[^54]: https://legacy.reactjs.org/docs/context.html

[^55]: https://gazar.dev/design-pattern/error-bounderies-react-design-pattern

[^56]: https://www.geeksforgeeks.org/reactjs/react-js-error-boundaries/

[^57]: https://react.dev/reference/react/memo

[^58]: https://dev.to/saijamii/boost-your-react-app-performance-with-lazy-loading-5046

[^59]: https://dev.to/codeofrelevancy/optimize-react-component-performance-with-memoization-using-reactmemo-3chp

[^60]: https://retool.com/blog/react-lazy-loading-and-performance

[^61]: https://www.codecademy.com/learn/learn-advanced-react/modules/advanced-react-error-boundaries/cheatsheet

[^62]: https://www.sitepoint.com/implement-memoization-in-react-to-improve-performance/

[^63]: https://www.syncfusion.com/blogs/post/lazy-loading-with-react

[^64]: https://dev.to/thisurathenuka/make-errors-user-friendly-with-react-error-boundaries-9e7

[^65]: https://blog.stackademic.com/boosting-react-performance-with-memoization-techniques-ddb6b7e080bb?gi=9f306f49e5ad

[^66]: https://www.youtube.com/watch?v=eF5n7Cmreso

[^67]: https://blog.stackademic.com/understanding-and-using-error-boundaries-in-react-c89231323a40?gi=487c3e3cbbc3

[^68]: https://www.geeksforgeeks.org/reactjs/what-is-memoization-in-react/

[^69]: https://react.dev/learn/managing-state

[^70]: https://www.angularminds.com/blog/react-architecture-patterns-in-reactjs-apps

[^71]: https://dev.to/parthee/react-design-patterns-best-practices-for-building-scalable-and-reusable-components-49bc

[^72]: https://ultimatecourses.com/learn/react-state-management

[^73]: https://dev.to/logrocket/5-react-performance-optimization-techniques-107c

[^74]: https://javascript.plainenglish.io/optimizing-react-performance-strategies-and-best-practices-bd8fb5ac5df0?gi=3183bd55c8ec

[^75]: https://dev.to/kinsflow/effective-patterns-for-shared-state-management-in-react-4ha1

[^76]: https://dev.to/chintanonweb/react-performance-optimization-best-practices-with-examples-3c76

[^77]: https://dev.to/logrocket/the-modern-guide-to-react-state-patterns-4098?url=https%3A%2F%2Fdev.to%2Flogrocket%2Fthe-modern-guide-to-react-state-patterns-4098

[^78]: https://dev.to/amaresh_adak/react-performance-optimization-from-slow-to-lightning-fast-complete-guide-2025-19hl

