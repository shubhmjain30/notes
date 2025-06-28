# 🔧 Stage 2: Core Proficiency (Intermediate)

[[README|← Back to React.js Interview Prep Checklist]]

Master these intermediate concepts to build dynamic, interactive React applications. This stage focuses on Hooks, advanced patterns, and essential tooling.

---

## ✅ Checklist: Core Proficiency

- [ ] **Hooks Fundamentals** – useState, useEffect, rules of hooks
- [ ] **Derived & Calculated State** – memoization with useMemo
- [ ] **Refs & DOM Access** – useRef, forwarding refs
- [ ] **Custom Hooks** – abstraction, composition, naming conventions
- [ ] **Context API** – provider patterns, avoiding re-renders
- [ ] **Error Boundaries** – lifecycle methods, fallback UIs
- [ ] **Forms** – controlled vs. uncontrolled, libraries (React Hook Form/Formik)
- [ ] **Routing** – React Router v6 dynamic routing, nested routes
- [ ] **Basic Testing** – React Testing Library, Jest, act(), mock functions
- [ ] **TypeScript with React** – typing props, generics, utility types

---

## Hooks Fundamentals

### The Rules of Hooks

**Problem:** Why do Hooks have specific rules, and how do they work internally?

**Theory:** Hooks rely on call order to maintain state between renders. React uses a linked list internally to track Hook state.

**The Two Rules:**
1. **Only call Hooks at the top level** - Never inside loops, conditions, or nested functions
2. **Only call Hooks from React functions** - React function components or custom Hooks

```jsx
// ❌ Violating Rules of Hooks
function BadComponent({ showUser }) {
  if (showUser) {
    const [user, setUser] = useState(null); // ❌ Conditional Hook call
  }

  const [count, setCount] = useState(0);
  
  for (let i = 0; i < 3; i++) {
    const [value, setValue] = useState(i); // ❌ Hook in loop
  }

  function helper() {
    const [data, setData] = useState([]); // ❌ Hook in nested function
  }

  return <div>{count}</div>;
}

// ✅ Following Rules of Hooks
function GoodComponent({ showUser }) {
  const [user, setUser] = useState(null);
  const [count, setCount] = useState(0);
  const [values, setValues] = useState([0, 1, 2]);

  // Conditional logic using the state
  const userDisplay = showUser ? user : null;

  return (
    <div>
      {userDisplay && <div>{userDisplay.name}</div>}
      <div>{count}</div>
    </div>
  );
}
```

### useState Deep Dive

**Problem:** How do we manage complex state updates efficiently?

```jsx
function ShoppingCart() {
  const [cart, setCart] = useState({
    items: [],
    total: 0,
    discountCode: '',
    shippingInfo: {
      address: '',
      city: '',
      zipCode: ''
    }
  });

  // ❌ Direct mutation - DON'T DO THIS
  const addItemBad = (item) => {
    cart.items.push(item); // Mutating state directly
    setCart(cart); // React won't detect the change
  };

  // ✅ Proper immutable update
  const addItem = (item) => {
    setCart(prevCart => ({
      ...prevCart,
      items: [...prevCart.items, { ...item, id: Date.now() }],
      total: prevCart.total + item.price
    }));
  };

  // ✅ Updating nested objects
  const updateShippingAddress = (address) => {
    setCart(prevCart => ({
      ...prevCart,
      shippingInfo: {
        ...prevCart.shippingInfo,
        address: address
      }
    }));
  };

  // ✅ Functional state update for complex logic
  const removeItem = (itemId) => {
    setCart(prevCart => {
      const itemToRemove = prevCart.items.find(item => item.id === itemId);
      const updatedItems = prevCart.items.filter(item => item.id !== itemId);
      
      return {
        ...prevCart,
        items: updatedItems,
        total: prevCart.total - (itemToRemove?.price || 0)
      };
    });
  };

  // ✅ Multiple state updates in single function
  const applyDiscount = (code) => {
    setCart(prevCart => {
      const discount = calculateDiscount(code, prevCart.total);
      return {
        ...prevCart,
        discountCode: code,
        total: prevCart.total - discount
      };
    });
  };

  return (
    <div>
      <h2>Shopping Cart ({cart.items.length} items)</h2>
      <div>Total: ${cart.total.toFixed(2)}</div>
      
      {cart.items.map(item => (
        <div key={item.id}>
          {item.name} - ${item.price}
          <button onClick={() => removeItem(item.id)}>Remove</button>
        </div>
      ))}
      
      <input
        placeholder="Discount code"
        value={cart.discountCode}
        onChange={(e) => setCart(prev => ({ ...prev, discountCode: e.target.value }))}
      />
      <button onClick={() => applyDiscount(cart.discountCode)}>
        Apply Discount
      </button>
    </div>
  );
}
```

### useEffect Mastery

**Problem:** How do we manage side effects, lifecycle events, and cleanup properly?

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  // Effect with dependency array - runs when userId changes
  useEffect(() => {
    let cancelled = false; // Cleanup flag

    const fetchUser = async () => {
      try {
        setLoading(true);
        setError(null);
        
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) throw new Error('User not found');
        
        const userData = await response.json();
        
        // Only update state if component is still mounted
        if (!cancelled) {
          setUser(userData);
        }
      } catch (err) {
        if (!cancelled) {
          setError(err.message);
        }
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    };

    fetchUser();

    // Cleanup function
    return () => {
      cancelled = true;
    };
  }, [userId]); // Dependency array

  // Effect for document title - runs on every render where user changes
  useEffect(() => {
    if (user) {
      document.title = `Profile - ${user.name}`;
    }
    
    // Cleanup when component unmounts
    return () => {
      document.title = 'React App';
    };
  }, [user]);

  // Effect with no dependencies - runs only on mount
  useEffect(() => {
    console.log('Component mounted');
    
    // Setup event listener
    const handleKeyPress = (e) => {
      if (e.key === 'Escape') {
        // Handle escape key
      }
    };
    
    window.addEventListener('keydown', handleKeyPress);
    
    // Cleanup event listener
    return () => {
      window.removeEventListener('keydown', handleKeyPress);
      console.log('Component unmounted');
    };
  }, []); // Empty dependency array

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>User not found</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

### useEffect Common Patterns

```jsx
// Pattern 1: Data fetching with AbortController
function DataFetcher({ endpoint }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const controller = new AbortController();

    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await fetch(endpoint, {
          signal: controller.signal
        });
        const result = await response.json();
        setData(result);
      } catch (error) {
        if (error.name !== 'AbortError') {
          console.error('Fetch error:', error);
        }
      } finally {
        setLoading(false);
      }
    };

    fetchData();

    return () => controller.abort();
  }, [endpoint]);

  return loading ? <div>Loading...</div> : <div>{JSON.stringify(data)}</div>;
}

// Pattern 2: Timer/Interval cleanup
function Timer() {
  const [seconds, setSeconds] = useState(0);
  const [isRunning, setIsRunning] = useState(false);

  useEffect(() => {
    let intervalId;

    if (isRunning) {
      intervalId = setInterval(() => {
        setSeconds(prev => prev + 1);
      }, 1000);
    }

    return () => {
      if (intervalId) {
        clearInterval(intervalId);
      }
    };
  }, [isRunning]);

  return (
    <div>
      <div>Time: {seconds}s</div>
      <button onClick={() => setIsRunning(!isRunning)}>
        {isRunning ? 'Pause' : 'Start'}
      </button>
      <button onClick={() => { setSeconds(0); setIsRunning(false); }}>
        Reset
      </button>
    </div>
  );
}

// Pattern 3: Window size tracking
function useWindowSize() {
  const [windowSize, setWindowSize] = useState({
    width: undefined,
    height: undefined,
  });

  useEffect(() => {
    function handleResize() {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    }

    window.addEventListener('resize', handleResize);
    handleResize(); // Call handler right away so state gets updated with initial window size

    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return windowSize;
}
```

---

## Derived & Calculated State

### useMemo for Expensive Calculations

**Problem:** How do we avoid recalculating expensive values on every render?

**Theory:** `useMemo` memoizes the result of a computation and only recalculates when dependencies change.

```jsx
function ExpensiveList({ items, searchTerm, sortBy, filterCategory }) {
  // ❌ Expensive calculation on every render
  const processedItemsBad = items
    .filter(item => 
      item.name.toLowerCase().includes(searchTerm.toLowerCase()) &&
      (filterCategory === 'all' || item.category === filterCategory)
    )
    .sort((a, b) => {
      if (sortBy === 'name') return a.name.localeCompare(b.name);
      if (sortBy === 'price') return a.price - b.price;
      if (sortBy === 'date') return new Date(b.createdAt) - new Date(a.createdAt);
      return 0;
    })
    .map(item => ({
      ...item,
      formattedPrice: formatCurrency(item.price),
      timeAgo: formatTimeAgo(item.createdAt)
    }));

  // ✅ Memoized expensive calculation
  const processedItems = useMemo(() => {
    console.log('Recalculating processed items...'); // Will only log when dependencies change
    
    return items
      .filter(item => 
        item.name.toLowerCase().includes(searchTerm.toLowerCase()) &&
        (filterCategory === 'all' || item.category === filterCategory)
      )
      .sort((a, b) => {
        if (sortBy === 'name') return a.name.localeCompare(b.name);
        if (sortBy === 'price') return a.price - b.price;
        if (sortBy === 'date') return new Date(b.createdAt) - new Date(a.createdAt);
        return 0;
      })
      .map(item => ({
        ...item,
        formattedPrice: formatCurrency(item.price),
        timeAgo: formatTimeAgo(item.createdAt)
      }));
  }, [items, searchTerm, sortBy, filterCategory]);

  // Derived values that don't need memoization (cheap calculations)
  const totalItems = processedItems.length;
  const totalValue = processedItems.reduce((sum, item) => sum + item.price, 0);

  return (
    <div>
      <div className="summary">
        <p>Showing {totalItems} items</p>
        <p>Total value: {formatCurrency(totalValue)}</p>
      </div>
      
      <div className="items">
        {processedItems.map(item => (
          <ItemCard key={item.id} item={item} />
        ))}
      </div>
    </div>
  );
}

// Helper functions
function formatCurrency(amount) {
  return new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD'
  }).format(amount);
}

function formatTimeAgo(date) {
  const now = new Date();
  const diffInMs = now - new Date(date);
  const diffInDays = Math.floor(diffInMs / (1000 * 60 * 60 * 24));
  
  if (diffInDays === 0) return 'Today';
  if (diffInDays === 1) return 'Yesterday';
  return `${diffInDays} days ago`;
}
```

### useMemo vs Regular Variables

```jsx
function ComponentExample({ data, filters }) {
  // ❌ This will recalculate on EVERY render, even if data/filters haven't changed
  const expensiveCalculation = data
    .filter(item => filters.category === 'all' || item.category === filters.category)
    .map(item => performExpensiveTransformation(item))
    .sort((a, b) => a.score - b.score);

  // ✅ This will only recalculate when data or filters change
  const memoizedCalculation = useMemo(() => {
    return data
      .filter(item => filters.category === 'all' || item.category === filters.category)
      .map(item => performExpensiveTransformation(item))
      .sort((a, b) => a.score - b.score);
  }, [data, filters]);

  // ✅ For simple/cheap calculations, regular variables are fine
  const count = data.length;
  const hasData = count > 0;
  const isEmpty = !hasData;

  return (
    <div>
      {isEmpty ? (
        <p>No data available</p>
      ) : (
        <div>
          <p>Found {count} items</p>
          {memoizedCalculation.map(item => (
            <div key={item.id}>{item.name}</div>
          ))}
        </div>
      )}
    </div>
  );
}
```

### Advanced useMemo Patterns

```jsx
// Pattern 1: Memoizing object/array creation
function UserList({ users, selectedIds }) {
  // ❌ Creates new object on every render, causing child re-renders
  const userStats = {
    total: users.length,
    selected: selectedIds.length,
    active: users.filter(u => u.isActive).length
  };

  // ✅ Memoized object creation
  const memoizedUserStats = useMemo(() => ({
    total: users.length,
    selected: selectedIds.length,
    active: users.filter(u => u.isActive).length
  }), [users, selectedIds]);

  return <UserStatsDisplay stats={memoizedUserStats} />;
}

// Pattern 2: Memoizing with complex dependencies
function DataProcessor({ rawData, config, userPreferences }) {
  const processedData = useMemo(() => {
    // Only recalculate when specific config properties change
    const relevantConfig = {
      algorithm: config.algorithm,
      threshold: config.threshold,
      includeMetadata: config.includeMetadata
    };

    return processData(rawData, relevantConfig, userPreferences);
  }, [
    rawData, 
    config.algorithm, 
    config.threshold, 
    config.includeMetadata, 
    userPreferences
  ]);

  return <DataVisualization data={processedData} />;
}
```

---

## Refs & DOM Access

### useRef for DOM Manipulation

**Problem:** How do we access DOM elements directly when React's declarative approach isn't sufficient?

```jsx
function FocusableInput() {
  const inputRef = useRef(null);
  const [isVisible, setIsVisible] = useState(false);

  // Focus input when component mounts
  useEffect(() => {
    if (inputRef.current) {
      inputRef.current.focus();
    }
  }, []);

  // Focus input when it becomes visible
  useEffect(() => {
    if (isVisible && inputRef.current) {
      inputRef.current.focus();
    }
  }, [isVisible]);

  const handleClearClick = () => {
    if (inputRef.current) {
      inputRef.current.value = '';
      inputRef.current.focus();
    }
  };

  return (
    <div>
      <button onClick={() => setIsVisible(!isVisible)}>
        {isVisible ? 'Hide' : 'Show'} Input
      </button>
      
      {isVisible && (
        <div>
          <input
            ref={inputRef}
            type="text"
            placeholder="This input will auto-focus"
          />
          <button onClick={handleClearClick}>Clear & Focus</button>
        </div>
      )}
    </div>
  );
}
```

### useRef for Storing Mutable Values

**Problem:** How do we store values that persist across renders but don't trigger re-renders when changed?

```jsx
function Timer() {
  const [seconds, setSeconds] = useState(0);
  const [isRunning, setIsRunning] = useState(false);
  
  // useRef to store interval ID - doesn't cause re-renders when changed
  const intervalRef = useRef(null);
  
  // useRef to store the latest callback - solves stale closure problem
  const savedCallback = useRef();

  // Update saved callback
  useEffect(() => {
    savedCallback.current = () => {
      setSeconds(prev => prev + 1);
    };
  });

  // Setup/cleanup interval
  useEffect(() => {
    if (isRunning) {
      intervalRef.current = setInterval(() => {
        savedCallback.current();
      }, 1000);
    } else if (intervalRef.current) {
      clearInterval(intervalRef.current);
      intervalRef.current = null;
    }

    return () => {
      if (intervalRef.current) {
        clearInterval(intervalRef.current);
      }
    };
  }, [isRunning]);

  const handleReset = () => {
    setSeconds(0);
    setIsRunning(false);
  };

  return (
    <div>
      <div>Seconds: {seconds}</div>
      <button onClick={() => setIsRunning(!isRunning)}>
        {isRunning ? 'Pause' : 'Start'}
      </button>
      <button onClick={handleReset}>Reset</button>
    </div>
  );
}
```

### Forwarding Refs

**Problem:** How do we pass refs through component boundaries to access child DOM elements?

```jsx
// Custom input component that forwards ref
const CustomInput = React.forwardRef((props, ref) => {
  const { label, error, ...inputProps } = props;

  return (
    <div className="input-group">
      {label && <label>{label}</label>}
      <input
        ref={ref}
        className={`input ${error ? 'input-error' : ''}`}
        {...inputProps}
      />
      {error && <span className="error-message">{error}</span>}
    </div>
  );
});

// Set display name for debugging
CustomInput.displayName = 'CustomInput';

// Using the forwarded ref
function Form() {
  const emailRef = useRef(null);
  const passwordRef = useRef(null);

  const handleSubmit = (e) => {
    e.preventDefault();
    
    const email = emailRef.current.value;
    const password = passwordRef.current.value;
    
    if (!email) {
      emailRef.current.focus();
      return;
    }
    
    if (!password) {
      passwordRef.current.focus();
      return;
    }
    
    // Process form submission
    console.log('Submitting:', { email, password });
  };

  return (
    <form onSubmit={handleSubmit}>
      <CustomInput
        ref={emailRef}
        label="Email"
        type="email"
        placeholder="Enter your email"
        required
      />
      
      <CustomInput
        ref={passwordRef}
        label="Password"
        type="password"
        placeholder="Enter your password"
        required
      />
      
      <button type="submit">Login</button>
    </form>
  );
}
```

### useImperativeHandle for Custom Ref APIs

**Problem:** How do we expose custom methods to parent components through refs?

```jsx
// Video player component with imperative API
const VideoPlayer = React.forwardRef((props, ref) => {
  const videoRef = useRef(null);
  const [isPlaying, setIsPlaying] = useState(false);
  const [currentTime, setCurrentTime] = useState(0);

  // Expose custom methods to parent through ref
  useImperativeHandle(ref, () => ({
    play: () => {
      videoRef.current?.play();
      setIsPlaying(true);
    },
    pause: () => {
      videoRef.current?.pause();
      setIsPlaying(false);
    },
    seekTo: (time) => {
      if (videoRef.current) {
        videoRef.current.currentTime = time;
        setCurrentTime(time);
      }
    },
    getCurrentTime: () => videoRef.current?.currentTime || 0,
    getDuration: () => videoRef.current?.duration || 0,
    isPlaying: () => isPlaying
  }));

  const handleTimeUpdate = () => {
    if (videoRef.current) {
      setCurrentTime(videoRef.current.currentTime);
    }
  };

  return (
    <video
      ref={videoRef}
      onTimeUpdate={handleTimeUpdate}
      onPlay={() => setIsPlaying(true)}
      onPause={() => setIsPlaying(false)}
      {...props}
    />
  );
});

VideoPlayer.displayName = 'VideoPlayer';

// Parent component using the video player
function VideoApp() {
  const playerRef = useRef(null);

  const handlePlayPause = () => {
    if (playerRef.current.isPlaying()) {
      playerRef.current.pause();
    } else {
      playerRef.current.play();
    }
  };

  const handleSeekForward = () => {
    const currentTime = playerRef.current.getCurrentTime();
    playerRef.current.seekTo(currentTime + 10);
  };

  return (
    <div>
      <VideoPlayer
        ref={playerRef}
        src="/video.mp4"
        controls={false}
      />
      
      <div className="controls">
        <button onClick={handlePlayPause}>Play/Pause</button>
        <button onClick={handleSeekForward}>+10s</button>
      </div>
    </div>
  );
}
```

---

## Custom Hooks

### Creating Reusable Logic

**Problem:** How do we extract and reuse stateful logic across multiple components?

**Theory:** Custom Hooks are functions that start with "use" and can call other Hooks. They allow sharing stateful logic without changing component hierarchy.

```jsx
// Custom hook for managing local storage
function useLocalStorage(key, initialValue) {
  // Get initial value from localStorage or use provided initial value
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(`Error reading localStorage key "${key}":`, error);
      return initialValue;
    }
  });

  // Return a wrapped version of useState's setter function that persists to localStorage
  const setValue = (value) => {
    try {
      // Allow value to be a function so we have the same API as useState
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(`Error setting localStorage key "${key}":`, error);
    }
  };

  // Listen for storage events (when localStorage is changed in another tab)
  useEffect(() => {
    const handleStorageChange = (e) => {
      if (e.key === key && e.newValue !== null) {
        try {
          setStoredValue(JSON.parse(e.newValue));
        } catch (error) {
          console.error(`Error parsing localStorage value for key "${key}":`, error);
        }
      }
    };

    window.addEventListener('storage', handleStorageChange);
    return () => window.removeEventListener('storage', handleStorageChange);
  }, [key]);

  return [storedValue, setValue];
}

// Using the custom hook
function Settings() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  const [language, setLanguage] = useLocalStorage('language', 'en');
  const [notifications, setNotifications] = useLocalStorage('notifications', true);

  return (
    <div>
      <h2>Settings</h2>
      
      <div>
        <label>
          Theme:
          <select value={theme} onChange={(e) => setTheme(e.target.value)}>
            <option value="light">Light</option>
            <option value="dark">Dark</option>
          </select>
        </label>
      </div>
      
      <div>
        <label>
          Language:
          <select value={language} onChange={(e) => setLanguage(e.target.value)}>
            <option value="en">English</option>
            <option value="es">Spanish</option>
            <option value="fr">French</option>
          </select>
        </label>
      </div>
      
      <div>
        <label>
          <input
            type="checkbox"
            checked={notifications}
            onChange={(e) => setNotifications(e.target.checked)}
          />
          Enable notifications
        </label>
      </div>
    </div>
  );
}
```

### Advanced Custom Hooks

```jsx
// Custom hook for API data fetching
function useApi(url, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  const { 
    immediate = true, 
    transform = (data) => data,
    dependencies = []
  } = options;

  const fetchData = useCallback(async () => {
    try {
      setLoading(true);
      setError(null);
      
      const response = await fetch(url);
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      
      const result = await response.json();
      const transformedData = transform(result);
      
      setData(transformedData);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  }, [url, transform]);

  useEffect(() => {
    if (immediate) {
      fetchData();
    }
  }, [fetchData, immediate, ...dependencies]);

  const refetch = () => {
    fetchData();
  };

  return {
    data,
    loading,
    error,
    refetch
  };
}

// Custom hook for form validation
function useFormValidation(initialValues, validationRules) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});

  const validateField = (name, value) => {
    const rules = validationRules[name];
    if (!rules) return '';

    for (const rule of rules) {
      const error = rule(value, values);
      if (error) return error;
    }
    return '';
  };

  const validateForm = () => {
    const newErrors = {};
    let isValid = true;

    Object.keys(validationRules).forEach(name => {
      const error = validateField(name, values[name]);
      if (error) {
        newErrors[name] = error;
        isValid = false;
      }
    });

    setErrors(newErrors);
    return isValid;
  };

  const handleChange = (name, value) => {
    setValues(prev => ({ ...prev, [name]: value }));
    
    // Validate field if it has been touched
    if (touched[name]) {
      const error = validateField(name, value);
      setErrors(prev => ({ ...prev, [name]: error }));
    }
  };

  const handleBlur = (name) => {
    setTouched(prev => ({ ...prev, [name]: true }));
    const error = validateField(name, values[name]);
    setErrors(prev => ({ ...prev, [name]: error }));
  };

  const reset = () => {
    setValues(initialValues);
    setErrors({});
    setTouched({});
  };

  return {
    values,
    errors,
    touched,
    handleChange,
    handleBlur,
    validateForm,
    reset,
    isValid: Object.keys(errors).length === 0
  };
}

// Using custom hooks together
function UserForm() {
  const validationRules = {
    email: [
      (value) => !value ? 'Email is required' : '',
      (value) => !/\S+@\S+\.\S+/.test(value) ? 'Email is invalid' : ''
    ],
    password: [
      (value) => !value ? 'Password is required' : '',
      (value) => value.length < 6 ? 'Password must be at least 6 characters' : ''
    ],
    confirmPassword: [
      (value) => !value ? 'Please confirm password' : '',
      (value, values) => value !== values.password ? 'Passwords do not match' : ''
    ]
  };

  const {
    values,
    errors,
    touched,
    handleChange,
    handleBlur,
    validateForm,
    reset
  } = useFormValidation(
    { email: '', password: '', confirmPassword: '' },
    validationRules
  );

  const {
    data: submitResult,
    loading: submitting,
    error: submitError,
    refetch: submitForm
  } = useApi('/api/register', {
    immediate: false,
    transform: (data) => ({ success: true, user: data })
  });

  const handleSubmit = async (e) => {
    e.preventDefault();
    
    if (validateForm()) {
      // Submit form
      await submitForm();
      if (!submitError) {
        reset();
      }
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <input
          type="email"
          placeholder="Email"
          value={values.email}
          onChange={(e) => handleChange('email', e.target.value)}
          onBlur={() => handleBlur('email')}
        />
        {touched.email && errors.email && (
          <span className="error">{errors.email}</span>
        )}
      </div>

      <div>
        <input
          type="password"
          placeholder="Password"
          value={values.password}
          onChange={(e) => handleChange('password', e.target.value)}
          onBlur={() => handleBlur('password')}
        />
        {touched.password && errors.password && (
          <span className="error">{errors.password}</span>
        )}
      </div>

      <div>
        <input
          type="password"
          placeholder="Confirm Password"
          value={values.confirmPassword}
          onChange={(e) => handleChange('confirmPassword', e.target.value)}
          onBlur={() => handleBlur('confirmPassword')}
        />
        {touched.confirmPassword && errors.confirmPassword && (
          <span className="error">{errors.confirmPassword}</span>
        )}
      </div>

      <button type="submit" disabled={submitting}>
        {submitting ? 'Creating Account...' : 'Create Account'}
      </button>

      {submitError && <div className="error">Error: {submitError}</div>}
      {submitResult?.success && <div className="success">Account created successfully!</div>}
    </form>
  );
}
```

---

## Context API

### Creating and Using Context

**Problem:** How do we share state across many components without prop drilling?

**Theory:** Context provides a way to pass data through the component tree without having to pass props down manually at every level.

```jsx
// 1. Create context
const ThemeContext = createContext({
  theme: 'light',
  toggleTheme: () => {}
});

// 2. Create provider component
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(prevTheme => prevTheme === 'light' ? 'dark' : 'light');
  };

  const value = {
    theme,
    toggleTheme
  };

  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}

// 3. Create custom hook for consuming context
function useTheme() {
  const context = useContext(ThemeContext);
  
  if (context === undefined) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  
  return context;
}

// 4. Use in components
function App() {
  return (
    <ThemeProvider>
      <Header />
      <MainContent />
      <Footer />
    </ThemeProvider>
  );
}

function Header() {
  const { theme, toggleTheme } = useTheme();

  return (
    <header className={`header header-${theme}`}>
      <h1>My App</h1>
      <button onClick={toggleTheme}>
        Switch to {theme === 'light' ? 'dark' : 'light'} mode
      </button>
    </header>
  );
}

function MainContent() {
  const { theme } = useTheme();

  return (
    <main className={`main main-${theme}`}>
      <p>This content adapts to the theme!</p>
    </main>
  );
}
```

### Complex Context with Reducer

```jsx
// Actions
const ACTIONS = {
  LOGIN: 'LOGIN',
  LOGOUT: 'LOGOUT',
  UPDATE_PROFILE: 'UPDATE_PROFILE',
  SET_LOADING: 'SET_LOADING'
};

// Reducer
function authReducer(state, action) {
  switch (action.type) {
    case ACTIONS.SET_LOADING:
      return { ...state, loading: action.payload };
      
    case ACTIONS.LOGIN:
      return {
        ...state,
        user: action.payload.user,
        token: action.payload.token,
        isAuthenticated: true,
        loading: false
      };
      
    case ACTIONS.LOGOUT:
      return {
        ...state,
        user: null,
        token: null,
        isAuthenticated: false,
        loading: false
      };
      
    case ACTIONS.UPDATE_PROFILE:
      return {
        ...state,
        user: { ...state.user, ...action.payload }
      };
      
    default:
      return state;
  }
}

// Context
const AuthContext = createContext();

// Provider
function AuthProvider({ children }) {
  const [state, dispatch] = useReducer(authReducer, {
    user: null,
    token: null,
    isAuthenticated: false,
    loading: true
  });

  // Initialize auth state from localStorage
  useEffect(() => {
    const token = localStorage.getItem('token');
    const user = localStorage.getItem('user');

    if (token && user) {
      dispatch({
        type: ACTIONS.LOGIN,
        payload: {
          token,
          user: JSON.parse(user)
        }
      });
    } else {
      dispatch({ type: ACTIONS.SET_LOADING, payload: false });
    }
  }, []);

  // Actions
  const login = async (credentials) => {
    dispatch({ type: ACTIONS.SET_LOADING, payload: true });
    
    try {
      const response = await fetch('/api/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(credentials)
      });
      
      const data = await response.json();
      
      if (response.ok) {
        localStorage.setItem('token', data.token);
        localStorage.setItem('user', JSON.stringify(data.user));
        
        dispatch({
          type: ACTIONS.LOGIN,
          payload: data
        });
      } else {
        throw new Error(data.message);
      }
    } catch (error) {
      dispatch({ type: ACTIONS.SET_LOADING, payload: false });
      throw error;
    }
  };

  const logout = () => {
    localStorage.removeItem('token');
    localStorage.removeItem('user');
    dispatch({ type: ACTIONS.LOGOUT });
  };

  const updateProfile = (updates) => {
    dispatch({
      type: ACTIONS.UPDATE_PROFILE,
      payload: updates
    });
  };

  const value = {
    ...state,
    login,
    logout,
    updateProfile
  };

  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
}

// Custom hook
function useAuth() {
  const context = useContext(AuthContext);
  
  if (context === undefined) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  
  return context;
}

// Usage
function LoginForm() {
  const { login, loading } = useAuth();
  const [credentials, setCredentials] = useState({ email: '', password: '' });
  const [error, setError] = useState('');

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      await login(credentials);
    } catch (err) {
      setError(err.message);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={credentials.email}
        onChange={(e) => setCredentials(prev => ({ ...prev, email: e.target.value }))}
        placeholder="Email"
        required
      />
      <input
        type="password"
        value={credentials.password}
        onChange={(e) => setCredentials(prev => ({ ...prev, password: e.target.value }))}
        placeholder="Password"
        required
      />
      <button type="submit" disabled={loading}>
        {loading ? 'Logging in...' : 'Login'}
      </button>
      {error && <div className="error">{error}</div>}
    </form>
  );
}
```

### Avoiding Context Re-render Issues

**Problem:** How do we prevent unnecessary re-renders when using Context?

```jsx
// ❌ Problem: All consumers re-render when any part of context changes
function ProblematicProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState('light');
  const [settings, setSettings] = useState({});

  // This object is recreated on every render, causing all consumers to re-render
  const value = {
    user,
    setUser,
    theme,
    setTheme,
    settings,
    setSettings
  };

  return (
    <MyContext.Provider value={value}>
      {children}
    </MyContext.Provider>
  );
}

// ✅ Solution 1: Split contexts by concern
const UserContext = createContext();
const ThemeContext = createContext();
const SettingsContext = createContext();

function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  
  const value = useMemo(() => ({
    user,
    setUser
  }), [user]);

  return (
    <UserContext.Provider value={value}>
      {children}
    </UserContext.Provider>
  );
}

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  const value = useMemo(() => ({
    theme,
    setTheme
  }), [theme]);

  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}

// ✅ Solution 2: Memoize context value
function OptimizedProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState('light');

  const userValue = useMemo(() => ({
    user,
    setUser
  }), [user]);

  const themeValue = useMemo(() => ({
    theme,
    setTheme
  }), [theme]);

  return (
    <UserContext.Provider value={userValue}>
      <ThemeContext.Provider value={themeValue}>
        {children}
      </ThemeContext.Provider>
    </UserContext.Provider>
  );
}

// ✅ Solution 3: Separate state and actions
const StateContext = createContext();
const ActionsContext = createContext();

function AppProvider({ children }) {
  const [state, setState] = useState({
    user: null,
    theme: 'light',
    settings: {}
  });

  // Actions don't change, so they won't cause re-renders
  const actions = useMemo(() => ({
    setUser: (user) => setState(prev => ({ ...prev, user })),
    setTheme: (theme) => setState(prev => ({ ...prev, theme })),
    updateSettings: (updates) => setState(prev => ({ 
      ...prev, 
      settings: { ...prev.settings, ...updates }
    }))
  }), []);

  return (
    <StateContext.Provider value={state}>
      <ActionsContext.Provider value={actions}>
        {children}
      </ActionsContext.Provider>
    </StateContext.Provider>
  );
}

// Custom hooks for consuming split contexts
function useAppState() {
  const context = useContext(StateContext);
  if (!context) throw new Error('useAppState must be used within AppProvider');
  return context;
}

function useAppActions() {
  const context = useContext(ActionsContext);
  if (!context) throw new Error('useAppActions must be used within AppProvider');
  return context;
}
```

---

*Continue to next sections...*

**Next:** [[reactjs-advanced-engineering|Stage 3: Advanced Engineering (Upper-Intermediate)]]

---

## Core Proficiency Mastery Checklist

Before moving to Stage 3, ensure you can:

- [ ] Use Hooks following all rules correctly
- [ ] Implement complex state logic with useState and useEffect
- [ ] Optimize performance with useMemo and useCallback
- [ ] Access and manipulate DOM elements with refs
- [ ] Create reusable custom hooks
- [ ] Implement Context API for state sharing
- [ ] Handle forms with controlled components
- [ ] Set up routing with React Router
- [ ] Write basic tests with React Testing Library
- [ ] Type React components with TypeScript
