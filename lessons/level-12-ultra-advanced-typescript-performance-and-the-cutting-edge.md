# Level 12: Ultra-advanced: TypeScript, performance, and the cutting edge

**Track:** JavaScript · **Level:** 12/12 · **Difficulty:** `ultra-advanced`

📚 **Today's lesson** — published 2026-07-02

## TL;DR

You've got the foundations. Now: TypeScript (types that catch bugs at compile time), performance optimization (memoization, code splitting, virtual lists), and the modern stack (Next.js, server components, edge functions).

## Real-world analogies

- TypeScript is JavaScript with a spell-checker. It catches typos and wrong types BEFORE your users do. Less runtime errors, better autocomplete, easier refactoring.
- Code splitting is like packing for a trip. Don't put everything in one giant suitcase. Split it into smaller bags that you only unpack when needed.

## Key concepts

### `TypeScript`

A typed superset of JavaScript. Compiles to plain JS. Catches type errors at build time. Industry standard for serious projects.

### `Performance`

Memoization (`useMemo`, `React.memo`), code splitting (`React.lazy`, dynamic `import()`), virtual lists (render only visible items), debouncing/throttling.

### `Server components (Next.js 13+)`

Components that run on the server, not the browser. Smaller bundle, direct database access, no API routes needed.

### `Edge functions`

Code that runs at CDN edge locations — close to users, no cold start. Vercel Edge, Cloudflare Workers, Deno Deploy.

## Code with comments

Every line has a comment. Read it slowly.

```
// === TypeScript basics ===
// Primitives
let name: string = "Aby";
let age: number = 30;
let active: boolean = true;
let nothing: null = null;
let anything: any = "could be anything";  // Avoid any when possible

// Arrays and tuples
let numbers: number[] = [1, 2, 3];
let pair: [string, number] = ["Aby", 30];  // tuple: fixed length, mixed types

// Objects
type User = {
    id: number;
    name: string;
    email?: string;            // optional
    readonly createdAt: Date;  // cannot be reassigned
};

const user: User = { id: 1, name: "Aby" };

// Union types
let id: string | number;  // Either string or number
id = "abc";
id = 42;

// Generics
function first<T>(arr: T[]): T | undefined {
    return arr[0];
}
first<number>([1, 2, 3]);  // 1 (typed as number)

// Interfaces (similar to types, for object shapes)
interface Animal {
    name: string;
    speak(): string;
}
class Dog implements Animal {
    constructor(public name: string) {}
    speak() { return `${this.name} barks`; }
}

// === React performance: memoization ===
import { useMemo, useCallback, memo } from "react";

// useMemo: cache expensive computations
function ExpensiveList({ items, filter }) {
    const filtered = useMemo(() => {
        return items.filter(item => item.includes(filter));
    }, [items, filter]);  // Recompute only when items or filter change

    return <ul>{filtered.map(i => <li key={i}>{i}</li>)}</ul>;
}

// useCallback: cache function references
function Parent() {
    const [count, setCount] = useState(0);
    const handleClick = useCallback(() => {
        console.log("clicked");
    }, []);  // Same function across renders (no deps to trigger re-create)

    return <Child onClick={handleClick} />;
}

// memo: skip re-render if props haven't changed
const Child = memo(function Child({ onClick }) {
    return <button onClick={onClick}>Click</button>;
});

// === Code splitting with React.lazy ===
import { lazy, Suspense } from "react";

const Dashboard = lazy(() => import("./Dashboard"));
const Settings = lazy(() => import("./Settings"));

function App() {
    return (
        <Suspense fallback={<p>Loading...</p>}>
            {showDashboard ? <Dashboard /> : <Settings />}
        </Suspense>
    );
}

// === Debouncing (avoid running on every keystroke) ===
function useDebounce(value, delay = 300) {
    const [debounced, setDebounced] = useState(value);
    useEffect(() => {
        const timer = setTimeout(() => setDebounced(value), delay);
        return () => clearTimeout(timer);
    }, [value, delay]);
    return debounced;
}

// Use it in a search input
function Search() {
    const [query, setQuery] = useState("");
    const debounced = useDebounce(query, 300);
    // Only fetch when debounced changes, not on every keystroke
    useEffect(() => {
        if (debounced) fetchResults(debounced);
    }, [debounced]);
    return <input value={query} onChange={e => setQuery(e.target.value)} />;
}

// === Next.js App Router (server components) ===
// app/page.tsx — runs on the server, no "use client" needed
import { db } from "@/lib/db";

export default async function Page() {
    const users = await db.user.findMany();  // Direct DB access!
    return (
        <ul>
            {users.map(user => <li key={user.id}>{user.name}</li>)}
        </ul>
    );
}

// app/search-bar.tsx — client component
"use client";
import { useState } from "react";

export function SearchBar() {
    const [q, setQ] = useState("");
    return <input value={q} onChange={e => setQ(e.target.value)} />;
}
```

## Try it yourself

Convert a small JS project to TypeScript. Add types to your functions, props, and state. Notice how many bugs the compiler catches. Try `tsc --strict` for the strictest checks.

## Common pitfalls

- ⚠️ Over-using `any`. It defeats the purpose of TypeScript. Use `unknown` if you don't know the type, then narrow with checks.
- ⚠️ Premature optimization. Don't add `useMemo` and `useCallback` everywhere. Add them when you measure a real performance problem.
- ⚠️ Using `useEffect` for everything. If you can compute a value during render, do that. `useEffect` is for side effects (network, subscriptions, DOM mutations).

## What's next?

🎉 You've completed the track! Time to build something real.

---

_Generated by Hermes · Aby's learning cron · Track: JavaScript · Level 12_