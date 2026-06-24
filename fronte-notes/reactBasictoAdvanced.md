# 📘 React.js Complete Notes - Zero to Advanced

## 📑 Table of Contents
1. [React Basics](#react-basics)
2. [JSX](#jsx)
3. [Components](#components)
4. [Props & State](#props--state)
5. [Event Handling](#event-handling)
6. [Conditional Rendering](#conditional-rendering)
7. [Lists & Keys](#lists--keys)
8. [Forms](#forms)
9. [Lifecycle Methods](#lifecycle-methods)
10. [Hooks](#hooks)
11. [Context API](#context-api)
12. [React Router](#react-router)
13. [Redux](#redux)
14. [Performance Optimization](#performance-optimization)
15. [Custom Hooks](#custom-hooks)
16. [Higher Order Components (HOC)](#higher-order-components)
17. [Render Props](#render-props)
18. [Error Boundaries](#error-boundaries)
19. [React Patterns](#react-patterns)
20. [Scenario Based Questions](#scenario-based-questions)
21. [Interview Questions & Answers](#interview-questions--answers)

---

## React Basics

### What is React?

**Hindi Explanation:**
React ek JavaScript library hai jo Facebook ne banaya hai. Iska use UI (User Interface) banane ke liye hota hai. Ye component-based architecture follow karta hai, matlab aap apne UI ko chhote-chhote reusable parts (components) mein tod sakte ho.

**English Interview Answer:**
React is an open-source JavaScript library developed by Facebook for building user interfaces, particularly single-page applications. It follows a component-based architecture and uses a Virtual DOM for efficient rendering. React enables developers to create reusable UI components that manage their own state.

```jsx
// First React App
import React from 'react';
import ReactDOM from 'react-dom/client';

function App() {
  return <h1>Hello, React!</h1>;
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

### Virtual DOM

**Hindi Explanation:**
Virtual DOM ek lightweight copy hai Real DOM ki. Jab bhi state change hoti hai, React pehle Virtual DOM mein changes karta hai, phir Real DOM se compare (diffing) karta hai, aur sirf jo changes hue hain wo Real DOM mein update karta hai. Isse performance fast hoti hai.

**English Interview Answer:**
The Virtual DOM is an in-memory representation of the real DOM. When state changes occur, React creates a new Virtual DOM tree, compares it with the previous one using a diffing algorithm (reconciliation), and then updates only the changed nodes in the actual DOM. This minimizes expensive DOM manipulations and improves performance.

---

## JSX

### What is JSX?

**Hindi Explanation:**
JSX ka matlab hai JavaScript XML. Ye ek syntax extension hai JavaScript ki jo HTML jaisa dikhta hai lekin actually JavaScript hai. Babel JSX ko `React.createElement()` calls mein convert karta hai.

**English Interview Answer:**
JSX (JavaScript XML) is a syntax extension for JavaScript that allows writing HTML-like code within JavaScript. It gets transpiled to `React.createElement()` calls by Babel. JSX makes the code more readable and enables us to write UI components in a declarative way.

```jsx
// JSX Example
const element = <h1 className="title">Hello, {name}!</h1>;

// Above JSX compiles to:
const element = React.createElement('h1', { className: 'title' }, `Hello, ${name}!`);
```

### JSX Rules

```jsx
// 1. Must return a single parent element
// ❌ Wrong
return (
  <h1>Hello</h1>
  <p>World</p>
);

// ✅ Correct
return (
  <div>
    <h1>Hello</h1>
    <p>World</p>
  </div>
);

// ✅ Or use Fragment
return (
  <>
    <h1>Hello</h1>
    <p>World</p>
  </>
);

// 2. className instead of class
<div className="container">Content</div>

// 3. htmlFor instead of for
<label htmlFor="name">Name</label>

// 4. Self-closing tags must be closed
<img src="image.png" alt="pic" />
<input type="text" />

// 5. JavaScript expressions in curly braces
<p>{2 + 2}</p>
<p>{isLoggedIn ? "Welcome" : "Please Login"}</p>
```

---

## Components

### Functional Components

**Hindi Explanation:**
Functional components simple JavaScript functions hain jo JSX return karte hain. Ye hooks ke saath state aur lifecycle methods use kar sakte hain. Aaj kal ye preferred approach hai.

**English Interview Answer:**
Functional components are JavaScript functions that accept props as arguments and return React elements (JSX). With the introduction of Hooks in React 16.8, functional components can now manage state and side effects, making them the preferred way to write components.

```jsx
// Functional Component
function Welcome({ name }) {
  return <h1>Hello, {name}!</h1>;
}

// Arrow Function Component
const Welcome = ({ name }) => {
  return <h1>Hello, {name}!</h1>;
};
```

### Class Components

**Hindi Explanation:**
Class components ES6 classes hain jo React.Component se extend karti hain. Inme render() method mandatory hai. Ye purana approach hai, ab mostly functional components use hote hain.

```jsx
// Class Component
import React, { Component } from 'react';

class Welcome extends Component {
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}
```

### Difference: Functional vs Class Components

| Feature | Functional | Class |
|---------|-----------|-------|
| Syntax | Function | ES6 Class |
| State | useState Hook | this.state |
| Lifecycle | useEffect Hook | lifecycle methods |
| `this` keyword | Not needed | Required |
| Performance | Slightly better | Slightly heavier |
| Readability | More readable | More verbose |

---

## Props & State

### Props (Properties)

**Hindi Explanation:**
Props parent component se child component ko data pass karne ka tarika hai. Props read-only hote hain, matlab child component inhe modify nahi kar sakta. Ye ek-directional data flow follow karta hai (top to bottom).

**English Interview Answer:**
Props (short for properties) are a mechanism for passing data from parent to child components. They are immutable (read-only) and follow a unidirectional data flow. Props enable component reusability by allowing the same component to render different data.

```jsx
// Parent Component
function App() {
  return <UserCard name="Rahul" age={25} isAdmin={true} />;
}

// Child Component
function UserCard({ name, age, isAdmin }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      {isAdmin && <span>Admin User</span>}
    </div>
  );
}

// Props with default values
function Button({ text = "Click Me", color = "blue" }) {
  return <button style={{ backgroundColor: color }}>{text}</button>;
}

// Children Props
function Card({ children, title }) {
  return (
    <div className="card">
      <h3>{title}</h3>
      <div className="card-body">{children}</div>
    </div>
  );
}

// Usage
<Card title="My Card">
  <p>This is card content</p>
  <button>Action</button>
</Card>
```

### State

**Hindi Explanation:**
State ek component ka internal data hai jo time ke saath change ho sakta hai. Jab state change hoti hai, component re-render hota hai. State ko directly modify nahi karte, setState (class) ya setter function (hooks) use karte hain.

**English Interview Answer:**
State is a built-in object that stores data belonging to a component. Unlike props, state is mutable and managed within the component. When state changes, React re-renders the component to reflect the updated data. State should be updated immutably using setState or the setter function from useState.

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  const increment = () => setCount(prev => prev + 1);
  const decrement = () => setCount(prev => prev - 1);
  const reset = () => setCount(0);

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

### Props vs State

| Feature | Props | State |
|---------|-------|-------|
| Ownership | Parent component | Own component |
| Mutability | Immutable | Mutable (via setter) |
| Purpose | Pass data down | Manage internal data |
| Triggers re-render | When parent re-renders | When updated |

---

## Event Handling

**Hindi Explanation:**
React mein events camelCase mein likhte hain (onClick, onChange, onSubmit). Event handler ek function hota hai, string nahi. React synthetic events use karta hai jo cross-browser compatible hote hain.

**English Interview Answer:**
React uses synthetic events — a cross-browser wrapper around native events. Events are named in camelCase and accept function references rather than strings. React's event system provides consistent behavior across all browsers and includes event pooling for performance.

```jsx
function EventExamples() {
  // Click Event
  const handleClick = (e) => {
    console.log('Button clicked!', e.target);
  };

  // Input Change Event
  const [text, setText] = useState('');
  const handleChange = (e) => {
    setText(e.target.value);
  };

  // Form Submit Event
  const handleSubmit = (e) => {
    e.preventDefault(); // Prevent page reload
    console.log('Form submitted!');
  };

  // Passing arguments to event handler
  const handleDelete = (id) => {
    console.log(`Delete item ${id}`);
  };

  return (
    <div>
      <button onClick={handleClick}>Click Me</button>
      <input value={text} onChange={handleChange} />
      <form onSubmit={handleSubmit}>
        <button type="submit">Submit</button>
      </form>
      <button onClick={() => handleDelete(5)}>Delete</button>
    </div>
  );
}
```

---

## Conditional Rendering

**Hindi Explanation:**
Conditional rendering ka matlab hai ki kuch condition ke basis pe different UI dikhana. React mein hum if/else, ternary operator, && operator, ya switch case use kar sakte hain.

```jsx
function Dashboard({ isLoggedIn, role, notifications }) {
  // Method 1: if/else (early return)
  if (!isLoggedIn) {
    return <LoginPage />;
  }

  // Method 2: Ternary Operator
  const greeting = role === 'admin' ? 'Welcome, Admin!' : 'Welcome, User!';

  // Method 3: && (Short Circuit)
  // Renders only if condition is true
  const notificationBadge = notifications.length > 0 && (
    <span className="badge">{notifications.length}</span>
  );

  // Method 4: Switch Case (for multiple conditions)
  const renderRole = () => {
    switch (role) {
      case 'admin': return <AdminPanel />;
      case 'editor': return <EditorPanel />;
      case 'viewer': return <ViewerPanel />;
      default: return <GuestPanel />;
    }
  };

  return (
    <div>
      <h1>{greeting}</h1>
      {notificationBadge}
      {renderRole()}
    </div>
  );
}
```

---

## Lists & Keys

**Hindi Explanation:**
Lists render karne ke liye hum `.map()` method use karte hain. Har list item ko ek unique `key` prop deni zaroori hai. Key se React ko pata chalta hai ki kaunsa item add, remove ya update hua hai. Index ko key ke taur par use karna avoid karo agar list dynamic hai.

**English Interview Answer:**
Keys help React identify which items have changed, been added, or removed. They should be stable, unique, and predictable. Using array index as key is discouraged for dynamic lists because it can lead to incorrect component state and rendering issues during reordering.

```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>
          <span>{todo.text}</span>
          <span>{todo.completed ? '✅' : '⬜'}</span>
        </li>
      ))}
    </ul>
  );
}

// ❌ Bad: Using index as key for dynamic lists
{items.map((item, index) => <Item key={index} {...item} />)}

// ✅ Good: Using unique ID
{items.map((item) => <Item key={item.id} {...item} />)}
```

---

## Forms

### Controlled Components

**Hindi Explanation:**
Controlled components mein form data React state ke through handle hota hai. Input ki value state se aati hai aur onChange event se state update hoti hai. React hi "single source of truth" hota hai.

```jsx
function RegistrationForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    password: '',
    gender: '',
    agree: false,
  });

  const [errors, setErrors] = useState({});

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value,
    }));
  };

  const validate = () => {
    const newErrors = {};
    if (!formData.name) newErrors.name = 'Name is required';
    if (!formData.email.includes('@')) newErrors.email = 'Invalid email';
    if (formData.password.length < 6) newErrors.password = 'Min 6 characters';
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (validate()) {
      console.log('Form Data:', formData);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="name" value={formData.name} onChange={handleChange} />
      {errors.name && <span className="error">{errors.name}</span>}

      <input name="email" value={formData.email} onChange={handleChange} />
      {errors.email && <span className="error">{errors.email}</span>}

      <input name="password" type="password" value={formData.password} onChange={handleChange} />
      {errors.password && <span className="error">{errors.password}</span>}

      <select name="gender" value={formData.gender} onChange={handleChange}>
        <option value="">Select</option>
        <option value="male">Male</option>
        <option value="female">Female</option>
      </select>

      <label>
        <input name="agree" type="checkbox" checked={formData.agree} onChange={handleChange} />
        I agree to terms
      </label>

      <button type="submit" disabled={!formData.agree}>Register</button>
    </form>
  );
}
```

### Uncontrolled Components

**Hindi Explanation:**
Uncontrolled components mein DOM khud form data handle karta hai. Hum useRef hook use karke directly DOM se value access karte hain. Ye simple forms ke liye useful hai.

```jsx
import { useRef } from 'react';

function SimpleForm() {
  const nameRef = useRef();
  const emailRef = useRef();

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(nameRef.current.value, emailRef.current.value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={nameRef} defaultValue="" />
      <input ref={emailRef} defaultValue="" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

---

## Lifecycle Methods

**Hindi Explanation:**
Lifecycle methods class components mein use hote hain. Ek component ki life mein 3 phases hain: Mounting (DOM mein add hona), Updating (re-render hona), Unmounting (DOM se remove hona). Functional components mein useEffect hook se ye sab handle hota hai.

```jsx
// Class Component Lifecycle
class MyComponent extends React.Component {
  // MOUNTING
  constructor(props) {
    super(props);
    this.state = { data: null };
  }

  static getDerivedStateFromProps(props, state) {
    // Rarely used - sync state with props
    return null;
  }

  componentDidMount() {
    // API calls, subscriptions, DOM manipulation
    this.fetchData();
  }

  // UPDATING
  shouldComponentUpdate(nextProps, nextState) {
    // Performance optimization - return false to skip re-render
    return true;
  }

  componentDidUpdate(prevProps, prevState) {
    if (prevProps.id !== this.props.id) {
      this.fetchData();
    }
  }

  // UNMOUNTING
  componentWillUnmount() {
    // Cleanup: remove listeners, cancel requests
  }

  render() {
    return <div>{this.state.data}</div>;
  }
}
```

---

## Hooks

### useState

```jsx
const [state, setState] = useState(initialValue);

// With object state
const [user, setUser] = useState({ name: '', age: 0 });
setUser(prev => ({ ...prev, name: 'Rahul' })); // Merge updates

// With array state
const [items, setItems] = useState([]);
setItems(prev => [...prev, newItem]); // Add item
setItems(prev => prev.filter(item => item.id !== id)); // Remove item
setItems(prev => prev.map(item =>
  item.id === id ? { ...item, done: true } : item
)); // Update item

// Lazy initialization (expensive computation)
const [data, setData] = useState(() => computeExpensiveValue());
```

### useEffect

**Hindi Explanation:**
useEffect hook side effects handle karne ke liye hai - jaise API calls, subscriptions, DOM manipulation. Ye componentDidMount, componentDidUpdate, aur componentWillUnmount ka replacement hai.

**English Interview Answer:**
useEffect is a Hook that lets you perform side effects in functional components. It runs after render and can optionally clean up before the component unmounts or before the effect re-runs. The dependency array controls when the effect executes.

```jsx
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  // Runs on mount and when userId changes
  useEffect(() => {
    let isMounted = true; // Prevent state update on unmounted component

    const fetchUser = async () => {
      setLoading(true);
      try {
        const res = await fetch(`/api/users/${userId}`);
        const data = await res.json();
        if (isMounted) {
          setUser(data);
          setLoading(false);
        }
      } catch (err) {
        if (isMounted) setLoading(false);
      }
    };

    fetchUser();

    // Cleanup function (like componentWillUnmount)
    return () => {
      isMounted = false;
    };
  }, [userId]); // Dependency array

  // Effect with no dependencies - runs once on mount
  useEffect(() => {
    console.log('Component mounted');
    return () => console.log('Component unmounted');
  }, []);

  // Effect with no array - runs on every render (avoid this)
  useEffect(() => {
    console.log('Runs on every render');
  });

  if (loading) return <p>Loading...</p>;
  return <div>{user?.name}</div>;
}
```

### useRef

**Hindi Explanation:**
useRef ek mutable reference create karta hai jo re-renders ke beech persist karti hai bina re-render cause kiye. DOM elements access karne aur mutable values store karne ke liye use hoti hai.

```jsx
import { useRef, useEffect } from 'react';

function TextInput() {
  const inputRef = useRef(null);
  const renderCount = useRef(0);

  useEffect(() => {
    // Auto-focus on mount
    inputRef.current.focus();
  }, []);

  useEffect(() => {
    renderCount.current += 1; // Won't cause re-render
  });

  return (
    <div>
      <input ref={inputRef} type="text" />
      <p>Rendered {renderCount.current} times</p>
    </div>
  );
}
```

### useMemo & useCallback

**Hindi Explanation:**
`useMemo` expensive calculations ko memoize karta hai - result tab tak cache rehta hai jab tak dependencies change nahi hoti. `useCallback` function reference ko memoize karta hai - ye child components ke unnecessary re-renders rokne ke liye useful hai.

```jsx
import { useMemo, useCallback, useState } from 'react';

function ProductList({ products, tax }) {
  // useMemo: Memoize expensive calculation
  const totalPrice = useMemo(() => {
    console.log('Calculating total...');
    return products.reduce((sum, p) => sum + p.price, 0) * (1 + tax);
  }, [products, tax]);

  // useCallback: Memoize function reference
  const handleDelete = useCallback((id) => {
    setProducts(prev => prev.filter(p => p.id !== id));
  }, []); // Empty deps = function never changes

  return (
    <div>
      <h2>Total: ₹{totalPrice}</h2>
      {products.map(p => (
        <ProductItem key={p.id} product={p} onDelete={handleDelete} />
      ))}
    </div>
  );
}

// Child component with React.memo to prevent unnecessary re-renders
const ProductItem = React.memo(({ product, onDelete }) => {
  console.log(`Rendering ${product.name}`);
  return (
    <div>
      <span>{product.name} - ₹{product.price}</span>
      <button onClick={() => onDelete(product.id)}>Delete</button>
    </div>
  );
});
```

### useReducer

**Hindi Explanation:**
useReducer complex state logic ke liye use hota hai jab multiple related state values hon ya next state previous state pe depend kare. Ye Redux jaisa pattern hai lekin component level pe.

```jsx
import { useReducer } from 'react';

const initialState = {
  todos: [],
  filter: 'all',
  loading: false,
};

function todoReducer(state, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return { ...state, todos: [...state.todos, action.payload] };
    case 'TOGGLE_TODO':
      return {
        ...state,
        todos: state.todos.map(todo =>
          todo.id === action.payload
            ? { ...todo, completed: !todo.completed }
            : todo
        ),
      };
    case 'DELETE_TODO':
      return {
        ...state,
        todos: state.todos.filter(todo => todo.id !== action.payload),
      };
    case 'SET_FILTER':
      return { ...state, filter: action.payload };
    default:
      return state;
  }
}

function TodoApp() {
  const [state, dispatch] = useReducer(todoReducer, initialState);
  const [text, setText] = useState('');

  const addTodo = () => {
    if (text.trim()) {
      dispatch({
        type: 'ADD_TODO',
        payload: { id: Date.now(), text, completed: false }
      });
      setText('');
    }
  };

  const filteredTodos = state.todos.filter(todo => {
    if (state.filter === 'active') return !todo.completed;
    if (state.filter === 'completed') return todo.completed;
    return true;
  });

  return (
    <div>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <button onClick={addTodo}>Add</button>
      <div>
        <button onClick={() => dispatch({ type: 'SET_FILTER', payload: 'all' })}>All</button>
        <button onClick={() => dispatch({ type: 'SET_FILTER', payload: 'active' })}>Active</button>
        <button onClick={() => dispatch({ type: 'SET_FILTER', payload: 'completed' })}>Done</button>
      </div>
      {filteredTodos.map(todo => (
        <div key={todo.id}>
          <span
            onClick={() => dispatch({ type: 'TOGGLE_TODO', payload: todo.id })}
            style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}
          >
            {todo.text}
          </span>
          <button onClick={() => dispatch({ type: 'DELETE_TODO', payload: todo.id })}>❌</button>
        </div>
      ))}
    </div>
  );
}
```

---

## Context API

**Hindi Explanation:**
Context API prop drilling problem solve karta hai. Jab deeply nested components ko data chahiye, toh har level pe props pass karne ki jagah Context use karte hain. Ye ek global state management solution hai small-to-medium apps ke liye.

**English Interview Answer:**
The Context API provides a way to share values between components without explicitly passing props through every level of the component tree. It's ideal for global data like themes, authentication, and language preferences. For complex state management, it can be combined with useReducer.

```jsx
import { createContext, useContext, useState } from 'react';

// 1. Create Context
const ThemeContext = createContext();
const AuthContext = createContext();

// 2. Create Provider
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  const login = (userData) => setUser(userData);
  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

// 3. Custom hook for consuming context
function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) throw new Error('useTheme must be used within ThemeProvider');
  return context;
}

function useAuth() {
  const context = useContext(AuthContext);
  if (!context) throw new Error('useAuth must be used within AuthProvider');
  return context;
}

// 4. Consume in any nested component
function Navbar() {
  const { theme, toggleTheme } = useTheme();
  const { user, logout } = useAuth();

  return (
    <nav className={`navbar-${theme}`}>
      <span>Welcome, {user?.name}</span>
      <button onClick={toggleTheme}>Toggle Theme</button>
      <button onClick={logout}>Logout</button>
    </nav>
  );
}

// 5. Wrap App with Providers
function App() {
  return (
    <AuthProvider>
      <ThemeProvider>
        <Navbar />
        <MainContent />
      </ThemeProvider>
    </AuthProvider>
  );
}
```

---

## React Router

**Hindi Explanation:**
React Router single page applications mein client-side routing provide karta hai. Page reload ke bina different components render hote hain URL change hone par.

```jsx
import { BrowserRouter, Routes, Route, Link, useParams, useNavigate, Navigate, Outlet } from 'react-router-dom';

// Protected Route Component
function ProtectedRoute({ isAuthenticated }) {
  if (!isAuthenticated) return <Navigate to="/login" replace />;
  return <Outlet />;
}

// Dynamic Route with useParams
function UserProfile() {
  const { userId } = useParams();
  const navigate = useNavigate();

  return (
    <div>
      <h2>User: {userId}</h2>
      <button onClick={() => navigate(-1)}>Go Back</button>
      <button onClick={() => navigate('/dashboard')}>Go to Dashboard</button>
    </div>
  );
}

function App() {
  const [isAuth, setIsAuth] = useState(false);

  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/users/123">User 123</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/login" element={<Login />} />
        <Route path="/users/:userId" element={<UserProfile />} />

        {/* Protected Routes */}
        <Route element={<ProtectedRoute isAuthenticated={isAuth} />}>
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/settings" element={<Settings />} />
        </Route>

        {/* 404 Page */}
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}
```

---

## Redux

**Hindi Explanation:**
Redux ek predictable state management library hai. Isme ek central store hota hai jisme poore application ka state rehta hai. State change karne ke liye actions dispatch karte hain, jo reducers ke through state update karte hain.

**English Interview Answer:**
Redux is a predictable state container that follows three principles: single source of truth (one store), state is read-only (changed only through actions), and changes are made with pure functions (reducers). Redux Toolkit is the modern recommended way to write Redux logic.

```jsx
// Redux Toolkit (Modern Approach)
import { configureStore, createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import { Provider, useSelector, useDispatch } from 'react-redux';

// Async Thunk for API calls
const fetchUsers = createAsyncThunk('users/fetch', async () => {
  const res = await fetch('/api/users');
  return res.json();
});

// Slice (combines reducer + actions)
const userSlice = createSlice({
  name: 'users',
  initialState: {
    list: [],
    loading: false,
    error: null,
  },
  reducers: {
    addUser: (state, action) => {
      state.list.push(action.payload); // Immer allows "mutation"
    },
    removeUser: (state, action) => {
      state.list = state.list.filter(u => u.id !== action.payload);
    },
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchUsers.pending, (state) => { state.loading = true; })
      .addCase(fetchUsers.fulfilled, (state, action) => {
        state.loading = false;
        state.list = action.payload;
      })
      .addCase(fetchUsers.rejected, (state, action) => {
        state.loading = false;
        state.error = action.error.message;
      });
  },
});

export const { addUser, removeUser } = userSlice.actions;

// Store
const store = configureStore({
  reducer: { users: userSlice.reducer },
});

// Component
function UserList() {
  const { list, loading } = useSelector(state => state.users);
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(fetchUsers());
  }, [dispatch]);

  if (loading) return <p>Loading...</p>;
  return (
    <ul>
      {list.map(user => (
        <li key={user.id}>
          {user.name}
          <button onClick={() => dispatch(removeUser(user.id))}>Remove</button>
        </li>
      ))}
    </ul>
  );
}

// App
function App() {
  return (
    <Provider store={store}>
      <UserList />
    </Provider>
  );
}
```

---

## Performance Optimization

**Hindi Explanation:**
React mein performance optimize karne ke kaafi tarike hain: React.memo se unnecessary re-renders rokna, useMemo/useCallback se expensive operations cache karna, lazy loading se initial bundle size kam karna, aur virtualization se large lists efficiently render karna.

```jsx
// 1. React.memo - Skip re-render if props unchanged
const ExpensiveList = React.memo(({ items }) => {
  return items.map(item => <div key={item.id}>{item.name}</div>);
});

// 2. Lazy Loading with Suspense
import { lazy, Suspense } from 'react';

const HeavyChart = lazy(() => import('./HeavyChart'));

function Dashboard() {
  return (
    <Suspense fallback={<div>Loading chart...</div>}>
      <HeavyChart />
    </Suspense>
  );
}

// 3. Code Splitting with Routes
const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </Suspense>
  );
}

// 4. Debouncing expensive operations
function SearchBox() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);

  useEffect(() => {
    const timer = setTimeout(() => {
      if (query) fetchResults(query).then(setResults);
    }, 300); // Debounce 300ms

    return () => clearTimeout(timer);
  }, [query]);

  return <input value={query} onChange={(e) => setQuery(e.target.value)} />;
}

// 5. useTransition (React 18) - for non-urgent updates
import { useTransition } from 'react';

function FilterList({ items }) {
  const [filter, setFilter] = useState('');
  const [isPending, startTransition] = useTransition();

  const handleChange = (e) => {
    startTransition(() => {
      setFilter(e.target.value); // Low priority update
    });
  };

  const filtered = items.filter(i => i.includes(filter));

  return (
    <div>
      <input onChange={handleChange} />
      {isPending && <span>Filtering...</span>}
      {filtered.map(i => <div key={i}>{i}</div>)}
    </div>
  );
}
```

---

## Custom Hooks

**Hindi Explanation:**
Custom hooks reusable logic extract karne ka tarika hai. Ye functions hain jo "use" se start hote hain aur andar dusre hooks use kar sakte hain. Ye components ke beech stateful logic share karne ka best practice hai.

```jsx
// useLocalStorage - Persist state in localStorage
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const item = localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

// useFetch - Generic data fetching
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    const fetchData = async () => {
      try {
        setLoading(true);
        const res = await fetch(url, { signal: controller.signal });
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        const json = await res.json();
        setData(json);
      } catch (err) {
        if (err.name !== 'AbortError') setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}

// useDebounce
function useDebounce(value, delay = 500) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

// useToggle
function useToggle(initial = false) {
  const [value, setValue] = useState(initial);
  const toggle = useCallback(() => setValue(v => !v), []);
  return [value, toggle];
}

// Usage
function App() {
  const [theme, setTheme] = useLocalStorage('theme', 'dark');
  const { data: users, loading } = useFetch('/api/users');
  const [isOpen, toggleOpen] = useToggle();

  return <div>...</div>;
}
```

---

## Higher Order Components

**Hindi Explanation:**
HOC ek function hai jo ek component le ke ek naya enhanced component return karta hai. Ye code reuse ka purana pattern hai. Ab mostly custom hooks use hote hain, lekin legacy code mein HOC milte hain.

```jsx
// HOC that adds loading state
function withLoading(WrappedComponent) {
  return function WithLoadingComponent({ isLoading, ...props }) {
    if (isLoading) return <div>Loading...</div>;
    return <WrappedComponent {...props} />;
  };
}

// HOC that adds authentication check
function withAuth(WrappedComponent) {
  return function WithAuthComponent(props) {
    const { user } = useAuth();
    if (!user) return <Navigate to="/login" />;
    return <WrappedComponent {...props} user={user} />;
  };
}

// Usage
const UserListWithLoading = withLoading(UserList);
const ProtectedDashboard = withAuth(Dashboard);
```

---

## Render Props

**Hindi Explanation:**
Render props ek pattern hai jisme component ko ek function prop pass karte hain jo batata hai ki kya render karna hai. Ye component logic share karne ka ek aur tarika hai.

```jsx
// Mouse tracker using render props
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  const handleMouseMove = (e) => {
    setPosition({ x: e.clientX, y: e.clientY });
  };

  return (
    <div onMouseMove={handleMouseMove} style={{ height: '100vh' }}>
      {render(position)}
    </div>
  );
}

// Usage
<MouseTracker render={({ x, y }) => (
  <h1>Mouse: ({x}, {y})</h1>
)} />
```

---

## Error Boundaries

**Hindi Explanation:**
Error Boundaries class components hain jo apne child component tree mein JavaScript errors catch karte hain, error UI dikhate hain, aur poore app ko crash hone se bachate hain. Note: Ye sirf class components mein available hain.

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught:', error, errorInfo);
    // Log to error reporting service
  }

  render() {
    if (this.state.hasError) {
      return (
        <div>
          <h2>Something went wrong!</h2>
          <button onClick={() => this.setState({ hasError: false })}>
            Try Again
          </button>
        </div>
      );
    }
    return this.props.children;
  }
}

// Usage
<ErrorBoundary>
  <RiskyComponent />
</ErrorBoundary>
```

---

## React Patterns

### Compound Components

```jsx
// Tab component using compound pattern
const TabContext = createContext();

function Tabs({ children, defaultTab }) {
  const [activeTab, setActiveTab] = useState(defaultTab);
  return (
    <TabContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabContext.Provider>
  );
}

Tabs.List = ({ children }) => <div className="tab-list">{children}</div>;

Tabs.Tab = ({ value, children }) => {
  const { activeTab, setActiveTab } = useContext(TabContext);
  return (
    <button
      className={activeTab === value ? 'active' : ''}
      onClick={() => setActiveTab(value)}
    >
      {children}
    </button>
  );
};

Tabs.Panel = ({ value, children }) => {
  const { activeTab } = useContext(TabContext);
  return activeTab === value ? <div>{children}</div> : null;
};

// Usage
<Tabs defaultTab="tab1">
  <Tabs.List>
    <Tabs.Tab value="tab1">Tab 1</Tabs.Tab>
    <Tabs.Tab value="tab2">Tab 2</Tabs.Tab>
  </Tabs.List>
  <Tabs.Panel value="tab1">Content 1</Tabs.Panel>
  <Tabs.Panel value="tab2">Content 2</Tabs.Panel>
</Tabs>
```

---

## Scenario Based Questions

### Scenario 1: Infinite Scroll Implementation

**Hindi Explanation:**
User scroll karta hai aur bottom pe pahunchne par aur data load hota hai. IntersectionObserver API use karke detect karte hain ki user page ke end pe pahunch gaya.

```jsx
function InfiniteScroll() {
  const [items, setItems] = useState([]);
  const [page, setPage] = useState(1);
  const [hasMore, setHasMore] = useState(true);
  const observerRef = useRef();

  const lastItemRef = useCallback((node) => {
    if (observerRef.current) observerRef.current.disconnect();

    observerRef.current = new IntersectionObserver(entries => {
      if (entries[0].isIntersecting && hasMore) {
        setPage(prev => prev + 1);
      }
    });

    if (node) observerRef.current.observe(node);
  }, [hasMore]);

  useEffect(() => {
    fetch(`/api/items?page=${page}`)
      .then(res => res.json())
      .then(data => {
        setItems(prev => [...prev, ...data.items]);
        setHasMore(data.hasMore);
      });
  }, [page]);

  return (
    <div>
      {items.map((item, i) => (
        <div key={item.id} ref={i === items.length - 1 ? lastItemRef : null}>
          {item.name}
        </div>
      ))}
    </div>
  );
}
```

### Scenario 2: Implementing a Shopping Cart

```jsx
const CartContext = createContext();

function cartReducer(state, action) {
  switch (action.type) {
    case 'ADD_ITEM': {
      const existing = state.find(i => i.id === action.payload.id);
      if (existing) {
        return state.map(i =>
          i.id === action.payload.id ? { ...i, qty: i.qty + 1 } : i
        );
      }
      return [...state, { ...action.payload, qty: 1 }];
    }
    case 'REMOVE_ITEM':
      return state.filter(i => i.id !== action.payload);
    case 'UPDATE_QTY':
      return state.map(i =>
        i.id === action.payload.id ? { ...i, qty: action.payload.qty } : i
      );
    case 'CLEAR':
      return [];
    default:
      return state;
  }
}

function CartProvider({ children }) {
  const [cart, dispatch] = useReducer(cartReducer, []);

  const total = useMemo(
    () => cart.reduce((sum, i) => sum + i.price * i.qty, 0),
    [cart]
  );

  return (
    <CartContext.Provider value={{ cart, dispatch, total }}>
      {children}
    </CartContext.Provider>
  );
}

const useCart = () => useContext(CartContext);
```

### Scenario 3: Debounced Search with API

```jsx
function SearchComponent() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);
  const debouncedQuery = useDebounce(query, 400);

  useEffect(() => {
    if (!debouncedQuery) {
      setResults([]);
      return;
    }

    const controller = new AbortController();
    setLoading(true);

    fetch(`/api/search?q=${debouncedQuery}`, { signal: controller.signal })
      .then(res => res.json())
      .then(data => {
        setResults(data);
        setLoading(false);
      })
      .catch(err => {
        if (err.name !== 'AbortError') setLoading(false);
      });

    return () => controller.abort();
  }, [debouncedQuery]);

  return (
    <div>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search..."
      />
      {loading && <p>Searching...</p>}
      {results.map(r => <div key={r.id}>{r.title}</div>)}
    </div>
  );
}
```

### Scenario 4: Pagination

```jsx
function PaginatedTable() {
  const [data, setData] = useState([]);
  const [currentPage, setCurrentPage] = useState(1);
  const [totalPages, setTotalPages] = useState(0);
  const itemsPerPage = 10;

  useEffect(() => {
    fetch(`/api/data?page=${currentPage}&limit=${itemsPerPage}`)
      .then(res => res.json())
      .then(({ items, total }) => {
        setData(items);
        setTotalPages(Math.ceil(total / itemsPerPage));
      });
  }, [currentPage]);

  return (
    <div>
      <table>
        <tbody>
          {data.map(item => <tr key={item.id}><td>{item.name}</td></tr>)}
        </tbody>
      </table>
      <div className="pagination">
        <button disabled={currentPage === 1} onClick={() => setCurrentPage(p => p - 1)}>Prev</button>
        {Array.from({ length: totalPages }, (_, i) => (
          <button
            key={i + 1}
            className={currentPage === i + 1 ? 'active' : ''}
            onClick={() => setCurrentPage(i + 1)}
          >
            {i + 1}
          </button>
        ))}
        <button disabled={currentPage === totalPages} onClick={() => setCurrentPage(p => p + 1)}>Next</button>
      </div>
    </div>
  );
}
```

### Scenario 5: Modal/Dialog Component

```jsx
import { createPortal } from 'react-dom';

function Modal({ isOpen, onClose, title, children }) {
  useEffect(() => {
    const handleEsc = (e) => { if (e.key === 'Escape') onClose(); };
    if (isOpen) {
      document.addEventListener('keydown', handleEsc);
      document.body.style.overflow = 'hidden';
    }
    return () => {
      document.removeEventListener('keydown', handleEsc);
      document.body.style.overflow = '';
    };
  }, [isOpen, onClose]);

  if (!isOpen) return null;

  return createPortal(
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={(e) => e.stopPropagation()}>
        <header>
          <h2>{title}</h2>
          <button onClick={onClose}>✕</button>
        </header>
        <div className="modal-body">{children}</div>
      </div>
    </div>,
    document.getElementById('modal-root')
  );
}
```

---

## Interview Questions & Answers

### Q1: What is the difference between Virtual DOM and Real DOM?

**English Answer:**
The Real DOM is the actual browser DOM — manipulating it is slow because it triggers reflow and repaint. The Virtual DOM is a lightweight JavaScript representation. React maintains two Virtual DOM trees, diffs them (reconciliation), and batch-updates only changed nodes in the Real DOM.

---

### Q2: What is reconciliation in React?

**Hindi:** Reconciliation wo process hai jisme React purane Virtual DOM ko naye se compare karta hai aur minimum changes find karke Real DOM update karta hai. React "diffing algorithm" use karta hai jo O(n) complexity mein kaam karta hai.

**English Answer:** Reconciliation is React's algorithm for diffing two Virtual DOM trees to determine the minimum number of changes needed to update the real DOM. It uses heuristics: elements of different types produce different trees, and keys help identify stable elements in lists.

---

### Q3: What are React Hooks rules?

**English Answer:**
1. Only call Hooks at the top level (not inside loops, conditions, or nested functions)
2. Only call Hooks from React function components or custom Hooks
3. Custom hooks must start with "use"

These rules ensure React can correctly track hook state across re-renders using call order.

---

### Q4: What is prop drilling and how to solve it?

**Hindi:** Prop drilling tab hota hai jab data ko multiple levels of components se pass karna padta hai sirf is liye ki deeply nested component ko wo data chahiye. Solution: Context API, Redux, Zustand, ya component composition.

**English Answer:** Prop drilling occurs when data is passed through multiple intermediate components that don't need it, just to reach a deeply nested component. Solutions include Context API, state management libraries (Redux, Zustand), or component composition patterns.

---

### Q5: Explain useEffect cleanup function.

**English Answer:**
The cleanup function in useEffect runs before the component unmounts and before the effect re-runs (on dependency change). It's used to prevent memory leaks by cleaning up subscriptions, event listeners, timers, or aborting API calls.

```jsx
useEffect(() => {
  const subscription = api.subscribe(data);
  return () => subscription.unsubscribe(); // Cleanup
}, []);
```

---

### Q6: What is the difference between useMemo and useCallback?

**English Answer:**
- `useMemo` memoizes a **computed value** — returns the cached result of a function
- `useCallback` memoizes a **function reference** — returns the same function instance

Both accept a dependency array and recompute only when dependencies change. `useCallback(fn, deps)` is equivalent to `useMemo(() => fn, deps)`.

---

### Q7: How does React handle batch updates?

**Hindi:** React 18 se pehle sirf event handlers mein batching hoti thi. React 18 mein automatic batching hai — multiple setState calls ek hi re-render mein process hote hain chahe wo setTimeout, promises, ya native events mein hon.

**English Answer:** React 18 introduces automatic batching — multiple state updates in any context (event handlers, timeouts, promises, native events) are grouped into a single re-render for performance. Use `flushSync` to opt out of batching when needed.

---

### Q8: What is React.memo and when to use it?

**English Answer:**
`React.memo` is a higher-order component that memoizes a component — it skips re-rendering if props haven't changed (shallow comparison). Use it for components that:
- Render often with the same props
- Are expensive to render
- Receive stable props (combine with useCallback/useMemo for objects/functions)

---

### Q9: Controlled vs Uncontrolled Components?

**English Answer:**
- **Controlled:** Form data is handled by React state. Input value comes from state, changes go through onChange handler. React is the "single source of truth."
- **Uncontrolled:** Form data is handled by the DOM itself. Use `ref` to access values. Simpler but less control.

Use controlled for validation, conditional disabling, enforced formats. Use uncontrolled for simple forms or integrating non-React code.

---

### Q10: What is React Fiber?

**Hindi:** React Fiber React 16 mein introduce hua naya reconciliation engine hai. Ye rendering work ko chunks mein todta hai aur frames ke beech spread karta hai. Isse high-priority updates (animations) low-priority updates (data fetching) se pehle process ho sakte hain.

**English Answer:** React Fiber is the reimplementation of React's core reconciliation algorithm (React 16+). It enables incremental rendering — the ability to split rendering work into chunks and spread it over multiple frames. This allows React to pause, abort, or prioritize work, enabling features like Suspense and concurrent rendering.

---

### Q11: What are the differences between `useEffect` and `useLayoutEffect`?

**English Answer:**
- `useEffect`: Runs asynchronously **after** paint. Non-blocking. Use for most side effects (API calls, subscriptions).
- `useLayoutEffect`: Runs synchronously **before** paint. Blocking. Use when you need to read layout/measure DOM or synchronously mutate DOM before user sees it.

---

### Q12: How to optimize a React app that renders 10,000 items?

**English Answer:**
1. **Virtualization** — Use react-window or react-virtuoso to render only visible items
2. **Pagination** — Load data in chunks
3. **React.memo** — Prevent unnecessary re-renders of list items
4. **useMemo** — Memoize filtered/sorted lists
5. **Web Workers** — Offload heavy computation
6. **useTransition** — Mark updates as non-urgent

---

### Q13: What is the Stale Closure problem in React?

**Hindi:** Jab ek function purani (stale) state value capture kar leta hai closure ke through, toh ye stale closure problem hai. Ye useEffect ya event handlers mein hota hai jab dependencies sahi se nahi di hoti.

**English Answer:**
A stale closure occurs when a function captures an outdated value from its enclosing scope. In React, this happens when callbacks or effects reference state/props that have since changed but the closure still holds the old value.

```jsx
// Problem
const [count, setCount] = useState(0);
useEffect(() => {
  const id = setInterval(() => console.log(count), 1000); // Always logs 0!
  return () => clearInterval(id);
}, []); // Missing dependency

// Solution 1: Add dependency
useEffect(() => { ... }, [count]);

// Solution 2: Use ref
const countRef = useRef(count);
countRef.current = count;
useEffect(() => {
  const id = setInterval(() => console.log(countRef.current), 1000);
  return () => clearInterval(id);
}, []);

// Solution 3: Functional update
setCount(prev => prev + 1); // Always uses latest value
```

---

### Q14: Explain React's key prop in detail.

**English Answer:**
Keys help React identify which items in a list have changed. Without keys, React uses index by default which causes issues when items are reordered, inserted, or deleted (wrong component state mapping). Keys should be:
- **Stable** — don't change between renders
- **Unique** — among siblings
- **Predictable** — derived from data, not random

---

### Q15: What is Server-Side Rendering (SSR) vs Client-Side Rendering (CSR)?

**English Answer:**
- **CSR:** Browser downloads empty HTML + JS bundle, React renders UI on client. Slower initial load, faster subsequent navigation.
- **SSR:** Server generates full HTML for each request, sends it to browser. Faster initial load, better SEO. Frameworks: Next.js, Remix.

| Aspect | CSR | SSR |
|--------|-----|-----|
| Initial Load | Slower | Faster |
| SEO | Poor | Good |
| Server Load | Low | Higher |
| Interactivity | After JS loads | After hydration |

---

### Q16: What is React Suspense and how does it work?

**English Answer:**
Suspense lets components "wait" for something before rendering, showing a fallback UI in the meantime. It works with:
- `React.lazy()` for code splitting
- Data fetching libraries (React Query, SWR with Suspense mode)
- React Server Components

```jsx
<Suspense fallback={<Spinner />}>
  <LazyComponent />
</Suspense>
```

---

### Q17: Scenario — Component re-renders too many times. How to debug?

**English Answer:**
1. Use React DevTools Profiler to identify which components re-render and why
2. Check if parent re-renders are cascading down
3. Look for new object/array/function references created on every render
4. Solutions: React.memo, useMemo, useCallback, move state closer to where it's used, split context

---

### Q18: Scenario — State update is not reflecting immediately. Why?

**Hindi:** React mein setState asynchronous hai. Agar aap setState ke turant baad state read karte ho toh purani value milegi. Solution: useEffect mein state change observe karo ya functional update use karo.

**English Answer:**
State updates in React are asynchronous and batched. The new state is not immediately available after calling setState. To perform logic after state updates, use useEffect with that state as a dependency, or use the functional form of setState (`setState(prev => ...)`) to ensure you're working with the latest value.

---

### Q19: How would you implement authentication in React?

**English Answer:**
1. Store auth token in httpOnly cookie (secure) or memory (not localStorage for sensitive tokens)
2. Create AuthContext with user state, login, logout functions
3. Protected Route component that checks auth status
4. Axios/fetch interceptors for attaching tokens and handling 401s
5. Redirect to login on unauthorized access

---

### Q20: What is the difference between `useRef` and `createRef`?

**English Answer:**
- `useRef` — for functional components; persists same ref object across re-renders
- `createRef` — creates a new ref on every render; suitable for class components

```jsx
// useRef persists between renders
const ref = useRef(null); // Same object every render

// createRef creates new ref each render (in functional component = bad)
const ref = createRef(); // New object every render
```

---

## 🎯 Quick Reference Cheat Sheet

| Hook | Purpose |
|------|---------|
| `useState` | Local state management |
| `useEffect` | Side effects (API, subscriptions) |
| `useContext` | Consume context values |
| `useReducer` | Complex state logic |
| `useRef` | Mutable ref, DOM access |
| `useMemo` | Memoize computed values |
| `useCallback` | Memoize functions |
| `useLayoutEffect` | Sync DOM measurements |
| `useTransition` | Non-urgent state updates |
| `useDeferredValue` | Defer non-critical re-renders |
| `useId` | Generate unique IDs for accessibility |

---

## 📚 Learning Path

1. **Beginner:** JSX → Components → Props → State → Events → Lists
2. **Intermediate:** Hooks → Context → Router → Forms → Lifecycle
3. **Advanced:** Redux → Performance → Patterns → SSR → Testing → Concurrent Features

---

> **Pro Tip:** Practice building real projects — Todo App, E-commerce Cart, Blog with Auth, Chat App, Dashboard with Charts. Theory + Practice = Interview Ready! 🚀
