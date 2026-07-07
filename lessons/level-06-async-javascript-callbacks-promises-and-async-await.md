# Level 6: Async JavaScript: callbacks, promises, and async/await

**Track:** JavaScript · **Level:** 6/12 · **Cycle:** 2 · **Difficulty:** `intermediate`

📚 **Today's lesson** — published 2026-07-07

## TL;DR

JavaScript is single-threaded but handles async operations (network requests, file reads, timers) without blocking. The journey from callbacks to Promises to async/await is one of the most important in modern JS.

## Real-world analogies

- A callback is a phone number you leave with the restaurant. They call you when the food is ready.
- A Promise is a receipt with a tracking number. You can check the status anytime: pending, fulfilled, or rejected.
- Async/await is like waiting in the restaurant instead of going home. You pause your code (without blocking the thread) until the result is ready.

## Key concepts

### `The event loop`

JavaScript runs one thing at a time on the main thread. Async operations happen in the background, and their callbacks are queued for when the main thread is free.

### `Callback hell`

Nested callbacks become unreadable: `doA(a => doB(b => doC(c => ...)))`. The 'pyramid of doom'.

### `Promises`

A Promise is a placeholder for a future value. `.then()` runs when it succeeds, `.catch()` when it fails, `.finally()` always.

### `async/await`

Syntactic sugar over Promises. Write async code that looks synchronous. Makes error handling with try/catch much cleaner.

## Code with comments

Every line has a comment. Read it slowly.

```
// === Callbacks (the old way) ===
fs.readFile("file.txt", (err, data) => {
    if (err) {
        console.error("Error:", err);
        return;
    }
    console.log("File contents:", data);
});

// === Promises ===
const fetchData = () => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            const success = true;
            if (success) {
                resolve({ id: 1, name: "Aby" });
            } else {
                reject(new Error("Failed to fetch"));
            }
        }, 1000);
    });
};

fetchData()
    .then(data => console.log("Got:", data))
    .catch(err => console.error("Error:", err))
    .finally(() => console.log("Done"));

// === Promise.all: wait for multiple promises ===
const p1 = fetch("/api/users");
const p2 = fetch("/api/posts");
const p3 = fetch("/api/comments");

Promise.all([p1, p2, p3])
    .then(([users, posts, comments]) => {
        // All three are ready
    })
    .catch(err => {
        // Any one failed
    });

// === Promise.race: first to complete wins ===
Promise.race([p1, p2, p3])
    .then(first => console.log("First:", first));

// === async/await (modern way) ===
async function getUser(id) {
    try {
        const response = await fetch(`/api/users/${id}`);
        if (!response.ok) throw new Error(`HTTP ${response.status}`);
        const user = await response.json();
        return user;
    } catch (error) {
        console.error("Failed to fetch user:", error);
        throw error;  // Re-throw or handle
    }
}

// Using it
async function main() {
    try {
        const user = await getUser(42);
        console.log("User:", user);
    } catch (err) {
        console.error("Could not load user");
    }
}
main();

// === Parallel execution with await ===
async function getAllData() {
    // Sequential (slow): one after another
    const user = await fetch("/api/user/1").then(r => r.json());
    const posts = await fetch("/api/posts").then(r => r.json());

    // Parallel (fast): all at once
    const [user, posts] = await Promise.all([
        fetch("/api/user/1").then(r => r.json()),
        fetch("/api/posts").then(r => r.json())
    ]);
}

// === fetch API: modern HTTP ===
async function createPost(data) {
    const response = await fetch("/api/posts", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(data)
    });

    if (!response.ok) {
        throw new Error(`Failed: ${response.status}`);
    }

    return response.json();
}

// === Common pattern: retry on failure ===
async function fetchWithRetry(url, retries = 3) {
    for (let i = 0; i < retries; i++) {
        try {
            return await fetch(url).then(r => r.json());
        } catch (err) {
            if (i === retries - 1) throw err;
            await new Promise(r => setTimeout(r, 1000 * (i + 1)));  // Wait longer each time
        }
    }
}
```

## Try it yourself

Build a small app that fetches 3 GitHub users in parallel using `Promise.all` and `fetch`, then displays their names and bios. Handle errors with `try/catch`.

## Common pitfalls

- ⚠️ Forgetting `await` — you get a Promise back, not the value. The promise resolves but you don't see the result.
- ⚠️ Not handling Promise rejections. Unhandled rejections crash Node.js (in newer versions). Always `.catch()` or use `try/catch` with async/await.
- ⚠️ Sequential awaits when parallel would work. `await a(); await b();` is slower than `await Promise.all([a(), b()]);` if they're independent.

## What's next?

**Level 7: Modules and the modern build system** — coming in the next drop.

---

_Generated by Hermes · Aby's learning cron · Track: JavaScript · Level 6 · Cycle 2_