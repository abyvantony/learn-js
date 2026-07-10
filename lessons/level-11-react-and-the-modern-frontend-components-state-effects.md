# Level 11: React and the modern frontend: components, state, effects

**Track:** JavaScript · **Level:** 11/12 · **Cycle:** 2 · **Difficulty:** `advanced`

📚 **Today's lesson** — published 2026-07-10

## TL;DR

React is the most popular frontend library. It lets you build UIs from components — reusable pieces that describe what the page should look like based on data. State changes trigger re-renders. Effects handle side effects.

## Real-world analogies

- A React component is a function that returns HTML. You give it data (props), it gives you back the page. When data changes, the function runs again, and you get new HTML.
- State is the component's memory. When state changes, the component re-renders — the page updates to match the new state.

## Key concepts

### `JSX`

HTML-like syntax inside JavaScript: `const el = <h1>Hello</h1>;`. Compiles to `React.createElement(...)` calls.

### `Components`

Functions that return JSX. Start with capital letter (`<App />`, not `<app />`). Can take props (inputs) and have state (memory).

### `Hooks`

`useState` (state), `useEffect` (side effects), `useMemo` (memoize), `useRef` (mutable ref), `useContext` (shared state). Rules: only call at top level, only in components or other hooks.

### `State management`

Local state: `useState`. Shared state: Context API. Complex apps: Redux, Zustand, Jotai. Modern trend: server-state libraries like React Query / TanStack Query.

## Code with comments

Every line has a comment. Read it slowly.

```
// === JSX: HTML in JavaScript ===
const name = "Aby";
const element = <h1>Hello, {name}!</h1>;

// JSX differences from HTML:
// - className instead of class
// - htmlFor instead of for
// - camelCase: onClick, onChange, tabIndex
// - Self-closing tags: <br />, <img />

// === Function component ===
function Welcome({ name }) {
    return <h1>Hello, {name}!</h1>;
}

// Use it
<Welcome name="Aby" />

// === useState: state in a component ===
import { useState } from "react";

function Counter() {
    const [count, setCount] = useState(0);  // Initial value: 0

    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>+1</button>
            <button onClick={() => setCount(0)}>Reset</button>
        </div>
    );
}

// === useEffect: side effects (data fetching, subscriptions) ===
import { useState, useEffect } from "react";

function UserList() {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        fetch("/api/users")
            .then(r => r.json())
            .then(data => {
                setUsers(data);
                setLoading(false);
            });
    }, []);  // Empty array = run once on mount

    if (loading) return <p>Loading...</p>;

    return (
        <ul>
            {users.map(user => (
                <li key={user.id}>{user.name}</li>
            ))}
        </ul>
    );
}

// === Conditional rendering ===
function Greeting({ user }) {
    if (!user) return <p>Please log in</p>;

    return (
        <div>
            <h1>Hello, {user.name}!</h1>
            {user.isAdmin && <AdminPanel />}
        </div>
    );
}

// === Lists ===
function TodoList({ todos }) {
    return (
        <ul>
            {todos.map(todo => (
                <li key={todo.id} className={todo.done ? "done" : ""}>
                    {todo.text}
                </li>
            ))}
        </ul>
    );
}

// === Forms ===
function LoginForm() {
    const [email, setEmail] = useState("");
    const [password, setPassword] = useState("");

    const handleSubmit = (e) => {
        e.preventDefault();
        // Send to API
        console.log({ email, password });
    };

    return (
        <form onSubmit={handleSubmit}>
            <input
                type="email"
                value={email}
                onChange={e => setEmail(e.target.value)}
            />
            <input
                type="password"
                value={password}
                onChange={e => setPassword(e.target.value)}
            />
            <button type="submit">Log in</button>
        </form>
    );
}

// === Custom hook: reuse stateful logic ===
function useLocalStorage(key, initialValue) {
    const [value, setValue] = useState(() => {
        const stored = localStorage.getItem(key);
        return stored ? JSON.parse(stored) : initialValue;
    });

    useEffect(() => {
        localStorage.setItem(key, JSON.stringify(value));
    }, [key, value]);

    return [value, setValue];
}

// Usage:
function Settings() {
    const [theme, setTheme] = useLocalStorage("theme", "light");
    return <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>{theme}</button>;
}
```

## Try it yourself

Build a `TodoApp` component: input + button to add, list of items, click to toggle done, delete button. Use `useState` for the list. Bonus: persist with `localStorage`.

## Common pitfalls

- ⚠️ Mutating state directly. `todos.push(newTodo)` doesn't trigger a re-render. Use `setTodos([...todos, newTodo])`.
- ⚠️ Forgetting the dependency array in `useEffect`. Without it, the effect runs on every render (usually bad).
- ⚠️ Using index as `key` in lists. Causes weird re-render bugs when items move. Use a stable ID.

## What's next?

**Level 12: Ultra-advanced: TypeScript, performance, and the cutting edge** — coming in the next drop.

---

_Generated by Hermes · Aby's learning cron · Track: JavaScript · Level 11 · Cycle 2_