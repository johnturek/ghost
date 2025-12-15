---
title: 'React Hooks: A Complete Guide for Beginners'
description: 'Learn how to use React Hooks to build powerful functional components with state and side effects'
pubDate: 'Dec 10 2025'
heroImage: '../../assets/blog-placeholder-1.jpg'
---

React Hooks revolutionized how we write React components. Instead of using class components for state and lifecycle methods, we can now use functional components with hooks. Let's dive into the most important hooks you need to know.

## Why Hooks?

Before hooks, you had to convert functional components to class components just to add state. Hooks let you use state and other React features without writing classes.

**Benefits:**
- Simpler, more readable code
- Easier to share stateful logic between components
- No `this` keyword confusion
- Better code organization

## useState: Adding State

The most basic hook for adding state to functional components:

\`\`\`javascript
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
\`\`\`

**Key points:**
- `useState` returns an array: [current value, setter function]
- The argument to `useState` is the initial state
- You can have multiple state variables

### Multiple State Variables

\`\`\`javascript
function UserForm() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [age, setAge] = useState(0);

  return (
    <form>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <input
        value={email}
        onChange={(e) => setEmail(e.target.value)}
      />
    </form>
  );
}
\`\`\`

### State with Objects

\`\`\`javascript
function UserForm() {
  const [user, setUser] = useState({
    name: '',
    email: '',
    age: 0
  });

  const updateField = (field, value) => {
    setUser(prev => ({
      ...prev,
      [field]: value
    }));
  };

  return (
    <input
      value={user.name}
      onChange={(e) => updateField('name', e.target.value)}
    />
  );
}
\`\`\`

## useEffect: Side Effects

Handle side effects like data fetching, subscriptions, or manually changing the DOM:

\`\`\`javascript
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    setLoading(true);
    fetch(\`/api/users/\${userId}\`)
      .then(res => res.json())
      .then(data => {
        setUser(data);
        setLoading(false);
      });
  }, [userId]); // Re-run when userId changes

  if (loading) return <div>Loading...</div>;
  return <div>{user.name}</div>;
}
\`\`\`

### Cleanup Functions

\`\`\`javascript
useEffect(() => {
  const subscription = api.subscribe(data => {
    setData(data);
  });

  // Cleanup function
  return () => {
    subscription.unsubscribe();
  };
}, []);
\`\`\`

### Effect Dependencies

\`\`\`javascript
// Run once on mount
useEffect(() => {
  console.log('Component mounted');
}, []);

// Run on every render
useEffect(() => {
  console.log('Component rendered');
});

// Run when specific values change
useEffect(() => {
  console.log('Count changed');
}, [count]);
\`\`\`

## useContext: Sharing Data

Access context values without prop drilling:

\`\`\`javascript
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext();

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

function ThemedButton() {
  const { theme, setTheme } = useContext(ThemeContext);

  return (
    <button
      style={{ background: theme === 'light' ? '#fff' : '#000' }}
      onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}
    >
      Toggle Theme
    </button>
  );
}
\`\`\`

## useRef: Accessing DOM Elements

Create mutable references that persist across renders:

\`\`\`javascript
import { useRef, useEffect } from 'react';

function AutoFocusInput() {
  const inputRef = useRef(null);

  useEffect(() => {
    inputRef.current.focus();
  }, []);

  return <input ref={inputRef} />;
}
\`\`\`

### Storing Mutable Values

\`\`\`javascript
function Timer() {
  const [count, setCount] = useState(0);
  const intervalRef = useRef(null);

  const start = () => {
    intervalRef.current = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
  };

  const stop = () => {
    clearInterval(intervalRef.current);
  };

  return (
    <div>
      <p>{count}</p>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
    </div>
  );
}
\`\`\`

## useMemo: Performance Optimization

Memoize expensive calculations:

\`\`\`javascript
import { useMemo, useState } from 'react';

function ExpensiveComponent({ items }) {
  const [filter, setFilter] = useState('');

  const filteredItems = useMemo(() => {
    console.log('Filtering items...');
    return items.filter(item =>
      item.name.toLowerCase().includes(filter.toLowerCase())
    );
  }, [items, filter]); // Only recalculate when these change

  return (
    <div>
      <input
        value={filter}
        onChange={(e) => setFilter(e.target.value)}
      />
      {filteredItems.map(item => (
        <div key={item.id}>{item.name}</div>
      ))}
    </div>
  );
}
\`\`\`

## useCallback: Memoizing Functions

Prevent unnecessary re-renders by memoizing callback functions:

\`\`\`javascript
import { useCallback, useState } from 'react';

function TodoList() {
  const [todos, setTodos] = useState([]);

  const addTodo = useCallback((text) => {
    setTodos(prev => [...prev, { id: Date.now(), text }]);
  }, []); // Function identity stays the same

  return <TodoForm onSubmit={addTodo} />;
}
\`\`\`

## Custom Hooks: Reusable Logic

Create your own hooks to share stateful logic:

\`\`\`javascript
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    setLoading(true);
    fetch(url)
      .then(res => res.json())
      .then(data => {
        setData(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err);
        setLoading(false);
      });
  }, [url]);

  return { data, loading, error };
}

// Usage
function UserProfile({ userId }) {
  const { data, loading, error } = useFetch(\`/api/users/\${userId}\`);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  return <div>{data.name}</div>;
}
\`\`\`

## Rules of Hooks

**Important rules you must follow:**

1. Only call hooks at the top level (not inside loops, conditions, or nested functions)
2. Only call hooks from React function components or custom hooks
3. Name custom hooks with "use" prefix

\`\`\`javascript
// ❌ Wrong
function Component() {
  if (condition) {
    const [state, setState] = useState(0); // Don't do this!
  }
}

// ✅ Correct
function Component() {
  const [state, setState] = useState(0);

  if (condition) {
    // Use the state here
  }
}
\`\`\`

## Common Patterns

### Form Handling

\`\`\`javascript
function useForm(initialValues) {
  const [values, setValues] = useState(initialValues);

  const handleChange = (e) => {
    setValues({
      ...values,
      [e.target.name]: e.target.value
    });
  };

  const reset = () => setValues(initialValues);

  return { values, handleChange, reset };
}

// Usage
function LoginForm() {
  const { values, handleChange, reset } = useForm({
    email: '',
    password: ''
  });

  return (
    <form>
      <input name="email" value={values.email} onChange={handleChange} />
      <input name="password" value={values.password} onChange={handleChange} />
      <button type="button" onClick={reset}>Reset</button>
    </form>
  );
}
\`\`\`

### Local Storage

\`\`\`javascript
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const item = localStorage.getItem(key);
    return item ? JSON.parse(item) : initialValue;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

// Usage
function App() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');

  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      Current theme: {theme}
    </button>
  );
}
\`\`\`

## Best Practices

1. **Keep hooks at the top level** of your component
2. **Use descriptive names** for state variables
3. **Split complex state** into multiple useState calls
4. **Extract reusable logic** into custom hooks
5. **Use useCallback and useMemo** sparingly (only when needed)
6. **Clean up effects** to prevent memory leaks

## Next Steps

- Practice by converting class components to hooks
- Build custom hooks for common patterns
- Learn about useReducer for complex state logic
- Explore React Query for data fetching
- Check out the [React Hooks documentation](https://react.dev/reference/react)

Hooks make React development more enjoyable and productive. Start using them in your projects today!
