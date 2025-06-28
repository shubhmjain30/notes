# 🌱 Stage 1: Foundations (Beginner)

[[README|← Back to React.js Interview Prep Checklist]]

Master these fundamental concepts before moving to Stage 2. Each topic builds the foundation for React development.

---

## ✅ Checklist: Foundations

- [ ] **JSX & Rendering Basics** – syntax, fragments, expressions, lists/keys
- [ ] **Component Types** – function vs. (legacy) class components
- [ ] **Props & PropTypes** – data flow, default props, validation
- [ ] **State & setState** – local component state patterns
- [ ] **Event Handling** – synthetic events, binding, lifting state
- [ ] **Conditional Rendering** – inline conditions, short-circuiting, element variables
- [ ] **Basic Styling** – CSS Modules, inline styles, className conventions
- [ ] **Project Bootstrapping** – Vite / CRA / Next app boilerplates
- [ ] **Developer Tooling** – React DevTools, eslint-plugin-react, Prettier

---

## JSX & Rendering Basics

### Understanding JSX Syntax

**Problem:** How do we write HTML-like markup within JavaScript while maintaining the full power of JavaScript expressions?

**Theory:** JSX is a syntax extension that allows writing HTML-like code in JavaScript. It transpiles to `React.createElement()` calls that create React elements.

**Core JSX Rules:**
1. Return a single root element (or use `<React.Fragment>` or `<>`)
2. Close all tags (including self-closing like `<img />`)
3. Use `className` instead of `class`
4. Use `htmlFor` instead of `for`
5. Embed JavaScript expressions with `{}`

```jsx
// ✅ Correct JSX
function WelcomeMessage({ user, notifications }) {
  return (
    <>
      <h1 className="heading">Welcome, {user.name}!</h1>
      <p>You have {notifications.length} new messages</p>
      <img src={user.avatar} alt={`${user.name}'s avatar`} />
    </>
  );
}

// ❌ Common mistakes
function BrokenComponent() {
  return (
    // Missing fragment wrapper
    <h1>Title</h1>
    <p>Description</p>
  );
}
```

### JSX Expressions and Dynamic Content

**When to use:** Embed JavaScript expressions for dynamic values, conditional logic, and data transformation.

```jsx
function UserDashboard({ user, isAdmin, tasks }) {
  const completedTasks = tasks.filter(task => task.completed);
  const pendingCount = tasks.length - completedTasks.length;

  return (
    <div className={`dashboard ${isAdmin ? 'admin-view' : 'user-view'}`}>
      <h2>Hello, {user.firstName} {user.lastName}</h2>
      
      {/* Conditional rendering */}
      {isAdmin && <button>Admin Panel</button>}
      
      {/* Computed values */}
      <p>Progress: {completedTasks.length}/{tasks.length} tasks completed</p>
      
      {/* Template literals */}
      <div className={`progress-bar ${pendingCount === 0 ? 'complete' : 'pending'}`}>
        <div style={{ width: `${(completedTasks.length / tasks.length) * 100}%` }} />
      </div>
    </div>
  );
}
```

### Rendering Lists and Keys

**Problem:** How do we efficiently render dynamic lists while helping React track changes?

**Theory:** The `key` prop helps React identify which items have changed, been added, or removed. This enables efficient updates to the DOM.

**Key Selection Rules:**
- Use stable, unique identifiers (preferably from your data)
- Avoid array indices if the list can be reordered
- Keys must be unique among siblings

```jsx
function TaskList({ tasks, onToggle, onDelete }) {
  return (
    <ul className="task-list">
      {tasks.map(task => (
        <li key={task.id} className={`task ${task.completed ? 'completed' : ''}`}>
          <input
            type="checkbox"
            checked={task.completed}
            onChange={() => onToggle(task.id)}
          />
          <span>{task.title}</span>
          <button onClick={() => onDelete(task.id)}>Delete</button>
        </li>
      ))}
    </ul>
  );
}

// ❌ Bad: Using array index as key
{tasks.map((task, index) => (
  <li key={index}>...</li> // Problematic if list is reordered
))}

// ✅ Good: Using stable ID
{tasks.map(task => (
  <li key={task.id}>...</li>
))}
```

### React Fragments

**Problem:** How do we group multiple elements without adding extra DOM nodes?

```jsx
// Using React.Fragment
function UserInfo({ user }) {
  return (
    <React.Fragment>
      <dt>Name</dt>
      <dd>{user.name}</dd>
      <dt>Email</dt>
      <dd>{user.email}</dd>
    </React.Fragment>
  );
}

// Using shorthand syntax
function UserInfo({ user }) {
  return (
    <>
      <dt>Name</dt>
      <dd>{user.name}</dd>
      <dt>Email</dt>
      <dd>{user.email}</dd>
    </>
  );
}

// When you need a key on the fragment
function ItemList({ items }) {
  return (
    <dl>
      {items.map(item => (
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.definition}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```

---

## Component Types

### Functional Components (Modern Standard)

**Problem:** How do we create reusable UI components that are simple, testable, and support modern React features?

**Theory:** Functional components are the standard approach in modern React. They're simple JavaScript functions that return JSX and can use Hooks for state and lifecycle logic.

```jsx
// Basic functional component
function Greeting({ name, time }) {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      <p>Current time: {time}</p>
    </div>
  );
}

// Arrow function component
const Button = ({ children, onClick, variant = 'primary' }) => {
  return (
    <button 
      className={`btn btn-${variant}`}
      onClick={onClick}
    >
      {children}
    </button>
  );
};

// Component with destructured props
function UserCard({ user: { name, email, avatar }, onEdit }) {
  return (
    <div className="user-card">
      <img src={avatar} alt={`${name}'s avatar`} />
      <h3>{name}</h3>
      <p>{email}</p>
      <button onClick={onEdit}>Edit Profile</button>
    </div>
  );
}
```

### Class Components (Legacy - Understanding Required)

**Problem:** How were components written before Hooks, and how do we maintain legacy codebases?

**Theory:** Class components extend `React.Component` and use lifecycle methods for side effects. Understanding them is crucial for maintaining older codebases.

```jsx
class UserProfile extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isEditing: false,
      userData: props.user
    };
    
    // Binding methods (pre-arrow function era)
    this.handleEdit = this.handleEdit.bind(this);
  }

  // Modern class syntax with arrow functions (no binding needed)
  handleEdit = () => {
    this.setState({ isEditing: !this.state.isEditing });
  };

  handleSave = (newData) => {
    this.setState({ 
      userData: newData, 
      isEditing: false 
    });
  };

  render() {
    const { isEditing, userData } = this.state;
    
    return (
      <div className="user-profile">
        {isEditing ? (
          <EditForm 
            user={userData} 
            onSave={this.handleSave}
            onCancel={() => this.setState({ isEditing: false })}
          />
        ) : (
          <DisplayView 
            user={userData} 
            onEdit={this.handleEdit}
          />
        )}
      </div>
    );
  }
}
```

### Migration from Class to Function Components

```jsx
// Before (Class Component)
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  increment = () => {
    this.setState(prevState => ({ count: prevState.count + 1 }));
  };

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}

// After (Functional Component with Hooks)
function Counter() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount(prevCount => prevCount + 1);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>Increment</button>
    </div>
  );
}
```

---

## Props & PropTypes

### Understanding Props Flow

**Problem:** How do we pass data between components in a predictable, type-safe way?

**Theory:** Props are the mechanism for passing data from parent to child components. They are read-only and create a unidirectional data flow.

```jsx
// Parent component
function App() {
  const user = {
    id: 1,
    name: 'Alice Johnson',
    email: 'alice@example.com',
    role: 'admin'
  };

  const handleUserUpdate = (updatedUser) => {
    console.log('User updated:', updatedUser);
  };

  return (
    <div>
      <UserProfile 
        user={user}
        isEditable={true}
        theme="dark"
        onUpdate={handleUserUpdate}
      />
    </div>
  );
}

// Child component receiving props
function UserProfile({ user, isEditable, theme, onUpdate }) {
  return (
    <div className={`user-profile theme-${theme}`}>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
      <span className={`role role-${user.role}`}>{user.role}</span>
      
      {isEditable && (
        <button onClick={() => onUpdate({ ...user, name: 'Updated Name' })}>
          Update Name
        </button>
      )}
    </div>
  );
}
```

### Default Props

**Problem:** How do we provide fallback values for optional props?

```jsx
// Method 1: ES6 default parameters (preferred)
function Button({ 
  children, 
  variant = 'primary', 
  size = 'medium',
  disabled = false,
  onClick = () => {} 
}) {
  return (
    <button 
      className={`btn btn-${variant} btn-${size}`}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
}

// Method 2: defaultProps (legacy, but still used)
function Card({ title, children, elevated }) {
  return (
    <div className={`card ${elevated ? 'elevated' : ''}`}>
      {title && <h3>{title}</h3>}
      <div className="card-content">
        {children}
      </div>
    </div>
  );
}

Card.defaultProps = {
  elevated: false
};
```

### PropTypes for Runtime Validation

**Problem:** How do we catch prop-related bugs during development?

**Theory:** PropTypes provide runtime type checking for React props, helping catch bugs early and serving as documentation.

```jsx
import PropTypes from 'prop-types';

function UserList({ users, onUserSelect, loading, error }) {
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id} onClick={() => onUserSelect(user)}>
          {user.name} - {user.email}
        </li>
      ))}
    </ul>
  );
}

UserList.propTypes = {
  users: PropTypes.arrayOf(
    PropTypes.shape({
      id: PropTypes.oneOfType([PropTypes.string, PropTypes.number]).isRequired,
      name: PropTypes.string.isRequired,
      email: PropTypes.string.isRequired,
      role: PropTypes.oneOf(['admin', 'user', 'moderator'])
    })
  ).isRequired,
  onUserSelect: PropTypes.func.isRequired,
  loading: PropTypes.bool,
  error: PropTypes.string
};

UserList.defaultProps = {
  loading: false,
  error: null
};

// Complex PropTypes examples
function Dashboard({ config, data, actions }) {
  // Component implementation
}

Dashboard.propTypes = {
  config: PropTypes.shape({
    theme: PropTypes.oneOf(['light', 'dark']),
    layout: PropTypes.oneOf(['grid', 'list']),
    itemsPerPage: PropTypes.number,
    showFilters: PropTypes.bool
  }),
  data: PropTypes.arrayOf(PropTypes.object),
  actions: PropTypes.objectOf(PropTypes.func)
};
```

---

## State & setState

### Understanding Component State

**Problem:** How do we manage data that changes over time within a component?

**Theory:** State represents data that can change and trigger re-renders. In functional components, we use the `useState` Hook.

```jsx
import { useState } from 'react';

function Counter() {
  // useState returns [currentValue, setterFunction]
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);

  const increment = () => setCount(count + step);
  const decrement = () => setCount(count - step);
  const reset = () => setCount(0);

  return (
    <div>
      <h2>Count: {count}</h2>
      <div>
        <label>
          Step: 
          <input 
            type="number" 
            value={step} 
            onChange={(e) => setStep(Number(e.target.value))}
          />
        </label>
      </div>
      <button onClick={increment}>+{step}</button>
      <button onClick={decrement}>-{step}</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

### State Updates with Previous Value

**Problem:** How do we update state based on the previous state value safely?

**Theory:** State updates might be batched or asynchronous. Use functional updates when the new state depends on the previous state.

```jsx
function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [inputValue, setInputValue] = useState('');

  const addTodo = () => {
    if (inputValue.trim()) {
      // ✅ Functional update - safe for async updates
      setTodos(prevTodos => [
        ...prevTodos,
        {
          id: Date.now(),
          text: inputValue.trim(),
          completed: false
        }
      ]);
      setInputValue('');
    }
  };

  const toggleTodo = (id) => {
    setTodos(prevTodos =>
      prevTodos.map(todo =>
        todo.id === id 
          ? { ...todo, completed: !todo.completed }
          : todo
      )
    );
  };

  const deleteTodo = (id) => {
    setTodos(prevTodos => prevTodos.filter(todo => todo.id !== id));
  };

  return (
    <div>
      <div>
        <input
          value={inputValue}
          onChange={(e) => setInputValue(e.target.value)}
          onKeyPress={(e) => e.key === 'Enter' && addTodo()}
          placeholder="Add a todo..."
        />
        <button onClick={addTodo}>Add</button>
      </div>
      
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(todo.id)}
            />
            <span style={{ 
              textDecoration: todo.completed ? 'line-through' : 'none' 
            }}>
              {todo.text}
            </span>
            <button onClick={() => deleteTodo(todo.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### Complex State Management

```jsx
function UserForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    preferences: {
      theme: 'light',
      notifications: true,
      language: 'en'
    }
  });

  // Update nested state
  const updatePreference = (key, value) => {
    setFormData(prev => ({
      ...prev,
      preferences: {
        ...prev.preferences,
        [key]: value
      }
    }));
  };

  // Update top-level state
  const updateField = (field, value) => {
    setFormData(prev => ({
      ...prev,
      [field]: value
    }));
  };

  return (
    <form>
      <input
        value={formData.name}
        onChange={(e) => updateField('name', e.target.value)}
        placeholder="Name"
      />
      
      <input
        value={formData.email}
        onChange={(e) => updateField('email', e.target.value)}
        placeholder="Email"
      />
      
      <select
        value={formData.preferences.theme}
        onChange={(e) => updatePreference('theme', e.target.value)}
      >
        <option value="light">Light</option>
        <option value="dark">Dark</option>
      </select>
      
      <label>
        <input
          type="checkbox"
          checked={formData.preferences.notifications}
          onChange={(e) => updatePreference('notifications', e.target.checked)}
        />
        Enable notifications
      </label>
    </form>
  );
}
```

---

## Event Handling

### Synthetic Events

**Problem:** How do we handle user interactions consistently across different browsers?

**Theory:** React wraps native events in SyntheticEvent objects that provide a consistent API across browsers and include useful methods like `preventDefault()` and `stopPropagation()`.

```jsx
function EventDemo() {
  const handleClick = (event) => {
    console.log('Event type:', event.type);
    console.log('Target element:', event.target);
    console.log('Current target:', event.currentTarget);
    
    // Prevent default behavior
    event.preventDefault();
    
    // Stop event bubbling
    event.stopPropagation();
  };

  const handleSubmit = (event) => {
    event.preventDefault();
    const formData = new FormData(event.target);
    console.log('Form data:', Object.fromEntries(formData));
  };

  const handleKeyPress = (event) => {
    if (event.key === 'Enter') {
      console.log('Enter key pressed');
    }
  };

  return (
    <div>
      <button onClick={handleClick}>Click me</button>
      
      <form onSubmit={handleSubmit}>
        <input 
          name="username" 
          onKeyPress={handleKeyPress}
          placeholder="Press Enter"
        />
        <button type="submit">Submit</button>
      </form>
    </div>
  );
}
```

### Event Handler Patterns

```jsx
function TodoItem({ todo, onToggle, onEdit, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  const [editText, setEditText] = useState(todo.text);

  // Inline handler for simple cases
  const handleDoubleClick = () => setIsEditing(true);

  // Handler with parameters
  const handleSave = () => {
    onEdit(todo.id, editText);
    setIsEditing(false);
  };

  // Handler with event parameter
  const handleKeyPress = (event) => {
    if (event.key === 'Enter') {
      handleSave();
    } else if (event.key === 'Escape') {
      setEditText(todo.text);
      setIsEditing(false);
    }
  };

  // Prevent event bubbling
  const handleDeleteClick = (event) => {
    event.stopPropagation();
    onDelete(todo.id);
  };

  return (
    <li 
      className={`todo-item ${todo.completed ? 'completed' : ''}`}
      onDoubleClick={handleDoubleClick}
    >
      <input
        type="checkbox"
        checked={todo.completed}
        onChange={() => onToggle(todo.id)}
      />
      
      {isEditing ? (
        <input
          value={editText}
          onChange={(e) => setEditText(e.target.value)}
          onKeyPress={handleKeyPress}
          onBlur={handleSave}
          autoFocus
        />
      ) : (
        <span>{todo.text}</span>
      )}
      
      <button onClick={handleDeleteClick}>Delete</button>
    </li>
  );
}
```

### Lifting State Up

**Problem:** How do we share state between sibling components?

**Theory:** When multiple components need to share state, move the state to their closest common ancestor and pass it down as props along with update functions.

```jsx
function App() {
  const [todos, setTodos] = useState([]);
  const [filter, setFilter] = useState('all'); // 'all', 'active', 'completed'

  const addTodo = (text) => {
    setTodos(prev => [...prev, {
      id: Date.now(),
      text,
      completed: false
    }]);
  };

  const toggleTodo = (id) => {
    setTodos(prev => prev.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };

  const deleteTodo = (id) => {
    setTodos(prev => prev.filter(todo => todo.id !== id));
  };

  const filteredTodos = todos.filter(todo => {
    if (filter === 'active') return !todo.completed;
    if (filter === 'completed') return todo.completed;
    return true;
  });

  return (
    <div>
      <TodoInput onAdd={addTodo} />
      <FilterButtons 
        currentFilter={filter} 
        onFilterChange={setFilter} 
      />
      <TodoList 
        todos={filteredTodos}
        onToggle={toggleTodo}
        onDelete={deleteTodo}
      />
    </div>
  );
}

function TodoInput({ onAdd }) {
  const [value, setValue] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    if (value.trim()) {
      onAdd(value.trim());
      setValue('');
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={value}
        onChange={(e) => setValue(e.target.value)}
        placeholder="What needs to be done?"
      />
      <button type="submit">Add</button>
    </form>
  );
}

function FilterButtons({ currentFilter, onFilterChange }) {
  const filters = [
    { key: 'all', label: 'All' },
    { key: 'active', label: 'Active' },
    { key: 'completed', label: 'Completed' }
  ];

  return (
    <div>
      {filters.map(filter => (
        <button
          key={filter.key}
          className={currentFilter === filter.key ? 'active' : ''}
          onClick={() => onFilterChange(filter.key)}
        >
          {filter.label}
        </button>
      ))}
    </div>
  );
}
```

---

## Conditional Rendering

### Basic Conditional Patterns

**Problem:** How do we show different UI based on state or props?

**Theory:** React uses JavaScript's logical operators for conditional rendering. No special template syntax needed.

```jsx
function UserStatus({ user, isLoading, error }) {
  // Early returns for loading and error states
  if (isLoading) {
    return <div className="loading">Loading user data...</div>;
  }

  if (error) {
    return (
      <div className="error">
        <h3>Error loading user</h3>
        <p>{error.message}</p>
        <button onClick={() => window.location.reload()}>
          Try Again
        </button>
      </div>
    );
  }

  // No user data
  if (!user) {
    return <div className="empty">No user found</div>;
  }

  // Main content when everything is loaded
  return (
    <div className="user-status">
      <h2>Welcome, {user.name}!</h2>
      
      {/* Conditional rendering with logical AND */}
      {user.isAdmin && (
        <div className="admin-panel">
          <button>Admin Dashboard</button>
        </div>
      )}
      
      {/* Ternary operator for either/or scenarios */}
      <div className="subscription-status">
        {user.isPremium ? (
          <span className="premium">Premium Member ⭐</span>
        ) : (
          <button>Upgrade to Premium</button>
        )}
      </div>
      
      {/* Multiple conditions */}
      {user.notifications && user.notifications.length > 0 && (
        <div className="notifications">
          <h3>Notifications ({user.notifications.length})</h3>
          <ul>
            {user.notifications.slice(0, 3).map(notification => (
              <li key={notification.id}>{notification.message}</li>
            ))}
          </ul>
        </div>
      )}
    </div>
  );
}
```

### Advanced Conditional Patterns

```jsx
function Dashboard({ user, permissions, data }) {
  // Computed values for cleaner JSX
  const canViewAnalytics = permissions.includes('analytics');
  const canManageUsers = permissions.includes('user_management');
  const hasData = data && data.length > 0;
  
  // Element variables for complex conditions
  let mainContent;
  
  if (!hasData) {
    mainContent = <EmptyState />;
  } else if (user.role === 'admin') {
    mainContent = <AdminDashboard data={data} />;
  } else if (user.role === 'manager') {
    mainContent = <ManagerDashboard data={data} />;
  } else {
    mainContent = <UserDashboard data={data} />;
  }

  return (
    <div className="dashboard">
      <header>
        <h1>Dashboard</h1>
        {/* Conditional navigation based on permissions */}
        <nav>
          <a href="/profile">Profile</a>
          {canViewAnalytics && <a href="/analytics">Analytics</a>}
          {canManageUsers && <a href="/users">Manage Users</a>}
        </nav>
      </header>

      <main>
        {mainContent}
      </main>

      {/* Conditional sidebar */}
      {(canViewAnalytics || canManageUsers) && (
        <aside className="admin-sidebar">
          <h3>Quick Actions</h3>
          {canViewAnalytics && <button>View Reports</button>}
          {canManageUsers && <button>User Management</button>}
        </aside>
      )}
    </div>
  );
}

// Helper components for cleaner code
function EmptyState() {
  return (
    <div className="empty-state">
      <h2>No Data Available</h2>
      <p>Start by adding some content to your dashboard.</p>
      <button>Get Started</button>
    </div>
  );
}
```

### Avoiding Common Pitfalls

```jsx
function ConditionalRenderingPitfalls({ items, count, user }) {
  return (
    <div>
      {/* ❌ Potential bug: renders 0 when count is 0 */}
      {count && <p>You have {count} items</p>}
      
      {/* ✅ Correct: explicitly check for truthiness */}
      {count > 0 && <p>You have {count} items</p>}
      
      {/* ❌ Potential bug: renders empty string for empty array */}
      {items.length && <ItemList items={items} />}
      
      {/* ✅ Correct: check length explicitly */}
      {items.length > 0 && <ItemList items={items} />}
      
      {/* ❌ Potential error: user might be null */}
      {user.isAdmin && <AdminPanel />}
      
      {/* ✅ Correct: check user exists first */}
      {user && user.isAdmin && <AdminPanel />}
      
      {/* ✅ Even better: using optional chaining */}
      {user?.isAdmin && <AdminPanel />}
    </div>
  );
}
```

---

## Basic Styling

### CSS Modules

**Problem:** How do we scope CSS to components to avoid style conflicts?

**Theory:** CSS Modules automatically scope CSS class names to the component, preventing global style conflicts.

```css
/* UserCard.module.css */
.card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 16px;
  margin: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.header {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 16px;
}

.avatar {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  object-fit: cover;
}

.name {
  font-size: 1.2em;
  font-weight: bold;
  margin: 0;
}

.email {
  color: #666;
  font-size: 0.9em;
}

.actions {
  display: flex;
  gap: 8px;
  justify-content: flex-end;
  margin-top: 16px;
}

.button {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.9em;
}

.primary {
  background-color: #007bff;
  color: white;
}

.secondary {
  background-color: #6c757d;
  color: white;
}
```

```jsx
// UserCard.jsx
import styles from './UserCard.module.css';

function UserCard({ user, onEdit, onDelete }) {
  return (
    <div className={styles.card}>
      <div className={styles.header}>
        <img 
          src={user.avatar} 
          alt={`${user.name}'s avatar`}
          className={styles.avatar}
        />
        <div>
          <h3 className={styles.name}>{user.name}</h3>
          <p className={styles.email}>{user.email}</p>
        </div>
      </div>
      
      <div className={styles.actions}>
        <button 
          className={`${styles.button} ${styles.primary}`}
          onClick={onEdit}
        >
          Edit
        </button>
        <button 
          className={`${styles.button} ${styles.secondary}`}
          onClick={onDelete}
        >
          Delete
        </button>
      </div>
    </div>
  );
}
```

### Inline Styles

**Problem:** How do we apply dynamic styles based on props or state?

**Theory:** Inline styles are useful for dynamic styling but should be used sparingly as they have performance implications and limited capabilities.

```jsx
function ProgressBar({ progress, color = 'blue', height = 20 }) {
  const containerStyle = {
    width: '100%',
    height: `${height}px`,
    backgroundColor: '#f0f0f0',
    borderRadius: `${height / 2}px`,
    overflow: 'hidden',
    border: '1px solid #ddd'
  };

  const fillStyle = {
    height: '100%',
    width: `${Math.min(100, Math.max(0, progress))}%`,
    backgroundColor: color,
    transition: 'width 0.3s ease-in-out',
    borderRadius: 'inherit'
  };

  return (
    <div style={containerStyle}>
      <div style={fillStyle} />
    </div>
  );
}

function StatusIndicator({ status, size = 'medium' }) {
  const sizeMap = {
    small: 8,
    medium: 12,
    large: 16
  };

  const colorMap = {
    online: '#28a745',
    offline: '#dc3545',
    away: '#ffc107'
  };

  const dotStyle = {
    width: sizeMap[size],
    height: sizeMap[size],
    borderRadius: '50%',
    backgroundColor: colorMap[status] || '#6c757d',
    display: 'inline-block',
    marginRight: '8px'
  };

  return (
    <span style={dotStyle} title={`Status: ${status}`} />
  );
}
```

### className Conventions and Dynamic Classes

```jsx
function Button({ 
  children, 
  variant = 'primary', 
  size = 'medium',
  disabled = false,
  loading = false,
  fullWidth = false,
  className = '',
  ...props 
}) {
  // Building className string
  const classNames = [
    'btn',
    `btn-${variant}`,
    `btn-${size}`,
    disabled && 'btn-disabled',
    loading && 'btn-loading',
    fullWidth && 'btn-full-width',
    className
  ].filter(Boolean).join(' ');

  return (
    <button 
      className={classNames}
      disabled={disabled || loading}
      {...props}
    >
      {loading && <span className="spinner" />}
      {children}
    </button>
  );
}

// Using a helper library (clsx or classnames)
import clsx from 'clsx';

function Card({ children, elevated, interactive, selected, className }) {
  return (
    <div 
      className={clsx(
        'card',
        elevated && 'card-elevated',
        interactive && 'card-interactive',
        selected && 'card-selected',
        className
      )}
    >
      {children}
    </div>
  );
}
```

---

## Project Bootstrapping

### Creating a New React Project with Vite

**Problem:** How do we set up a modern React development environment quickly?

**Theory:** Vite is the recommended build tool for new React projects, offering fast development server startup and Hot Module Replacement (HMR).

```bash
# Create a new React project with Vite
npm create vite@latest my-react-app -- --template react

# Navigate to project directory
cd my-react-app

# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

### Project Structure Best Practices

```
my-react-app/
├── public/
│   ├── index.html
│   └── favicon.ico
├── src/
│   ├── components/        # Reusable UI components
│   │   ├── ui/           # Basic UI components (Button, Input, etc.)
│   │   └── common/       # Shared business components
│   ├── pages/            # Page components
│   ├── hooks/            # Custom hooks
│   ├── context/          # React contexts
│   ├── utils/            # Utility functions
│   ├── services/         # API calls and external services
│   ├── styles/           # Global styles
│   ├── assets/           # Images, icons, etc.
│   ├── App.jsx
│   └── main.jsx
├── package.json
└── vite.config.js
```

### Essential Package.json Scripts

```json
{
  "name": "my-react-app",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "lint": "eslint . --ext js,jsx --report-unused-disable-directives --max-warnings 0",
    "lint:fix": "eslint . --ext js,jsx --fix",
    "preview": "vite preview",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "format": "prettier --write src/"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.15",
    "@types/react-dom": "^18.2.7",
    "@vitejs/plugin-react": "^4.0.3",
    "eslint": "^8.45.0",
    "eslint-plugin-react": "^7.32.2",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.3",
    "prettier": "^3.0.0",
    "vite": "^4.4.5",
    "vitest": "^0.34.0"
  }
}
```

### Vite Configuration

```javascript
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@pages': path.resolve(__dirname, './src/pages'),
      '@hooks': path.resolve(__dirname, './src/hooks'),
      '@utils': path.resolve(__dirname, './src/utils')
    }
  },
  server: {
    port: 3000,
    open: true
  },
  build: {
    outDir: 'dist',
    sourcemap: true
  }
});
```

---

## Developer Tooling

### React DevTools

**Problem:** How do we debug React applications and inspect component state and props?

**Theory:** React DevTools provide a browser extension that integrates with browser developer tools to inspect React component trees, state, and props.

**Installation:**
- Chrome: Install "React Developer Tools" extension
- Firefox: Install "React DevTools" add-on
- Standalone: `npm install -g react-devtools`

**Key Features:**
1. **Components Tab**: Inspect component hierarchy, props, and state
2. **Profiler Tab**: Measure component performance and render times
3. **Settings**: Configure component filters and debugging options

```jsx
// Adding displayName for easier debugging
function UserProfile({ user }) {
  return <div>{user.name}</div>;
}

UserProfile.displayName = 'UserProfile';

// Using React.memo with displayName
const MemoizedProfile = React.memo(UserProfile);
MemoizedProfile.displayName = 'MemoizedUserProfile';
```

### ESLint Configuration for React

**Problem:** How do we maintain code quality and catch common React mistakes?

```javascript
// .eslintrc.cjs
module.exports = {
  root: true,
  env: { browser: true, es2020: true },
  extends: [
    'eslint:recommended',
    '@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:react/jsx-runtime',
    'plugin:react-hooks/recommended',
  ],
  ignorePatterns: ['dist', '.eslintrc.cjs'],
  parser: '@typescript-eslint/parser',
  plugins: ['react-refresh'],
  rules: {
    'react-refresh/only-export-components': [
      'warn',
      { allowConstantExport: true },
    ],
    'react/prop-types': 'error',
    'react-hooks/rules-of-hooks': 'error',
    'react-hooks/exhaustive-deps': 'warn',
    'react/jsx-uses-react': 'off',
    'react/react-in-jsx-scope': 'off',
  },
  settings: {
    react: {
      version: 'detect',
    },
  },
};
```

### Prettier Configuration

```json
// .prettierrc
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "bracketSpacing": true,
  "arrowParens": "avoid",
  "endOfLine": "lf"
}
```

```javascript
// .prettierignore
dist
build
node_modules
*.min.js
```

### VS Code Configuration

```json
// .vscode/settings.json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "emmet.includeLanguages": {
    "javascript": "javascriptreact"
  },
  "typescript.preferences.importModuleSpecifier": "relative"
}
```

---

## Next Steps

Once you've mastered all the Foundation concepts:

1. **Practice Building**: Create small projects using only these concepts
2. **Code Review**: Review your code for proper prop flow and state management
3. **Debugging**: Use React DevTools extensively
4. **Performance**: Start thinking about when components re-render

**Ready for Stage 2?** → [[reactjs-core-proficiency|Core Proficiency (Intermediate)]]

---

## Foundation Mastery Checklist

Before moving to Stage 2, ensure you can:

- [ ] Write JSX without syntax errors
- [ ] Create functional and class components
- [ ] Pass and validate props correctly
- [ ] Manage local component state
- [ ] Handle events and lift state up
- [ ] Implement conditional rendering patterns
- [ ] Apply basic styling approaches
- [ ] Set up and configure a React project
- [ ] Debug components using React DevTools
