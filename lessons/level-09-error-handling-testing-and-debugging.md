# Level 9: Error handling, testing, and debugging

**Track:** JavaScript · **Level:** 9/12 · **Cycle:** 2 · **Difficulty:** `intermediate-plus`

📚 **Today's lesson** — published 2026-07-09

## TL;DR

Every real-world JS app needs to handle errors gracefully and be tested. This level covers try/catch, custom errors, async error handling, and the basics of unit testing with Jest or Vitest.

## Real-world analogies

- Try/catch is a safety net under a tightrope walker. If they fall (throw), the net catches them (catch) and they don't die (crash the app).
- Tests are spell-check for code. They catch typos (bugs) before your readers (users) do.

## Key concepts

### `Try/catch/finally`

`try` runs the code, `catch` runs if it throws, `finally` runs no matter what. Use `finally` for cleanup.

### `Custom error classes`

Extend `Error` to create specific error types. Lets you handle different errors differently.

### `Async error handling`

`async/await` lets you use `try/catch`. With raw Promises, use `.catch()`. With callbacks, the first arg is the error.

### `Testing`

Unit tests check individual functions. Integration tests check how they work together. The most popular JS test frameworks: Jest (Facebook), Vitest (modern, fast), Mocha (older).

## Code with comments

Every line has a comment. Read it slowly.

```
// === Try/catch/finally ===
try {
    const data = JSON.parse(invalidJson);
    console.log(data);
} catch (error) {
    console.error("Failed to parse:", error.message);
} finally {
    console.log("Cleanup runs whether or not there was an error");
}

// === Throwing your own errors ===
function divide(a, b) {
    if (b === 0) {
        throw new Error("Division by zero");
    }
    return a / b;
}

// === Custom error classes ===
class ValidationError extends Error {
    constructor(field, message) {
        super(message);
        this.name = "ValidationError";
        this.field = field;
    }
}

class NetworkError extends Error {
    constructor(url, status) {
        super(`Request to ${url} failed with status ${status}`);
        this.name = "NetworkError";
        this.status = status;
    }
}

function validateUser(user) {
    if (!user.email) {
        throw new ValidationError("email", "Email is required");
    }
    if (!user.age || user.age < 0) {
        throw new ValidationError("age", "Valid age is required");
    }
}

// === Async error handling ===
async function fetchUser(id) {
    try {
        const response = await fetch(`/api/users/${id}`);
        if (!response.ok) {
            throw new NetworkError(`/api/users/${id}`, response.status);
        }
        return await response.json();
    } catch (error) {
        if (error instanceof NetworkError) {
            console.error("Network problem:", error.status);
        } else if (error instanceof ValidationError) {
            console.error("Invalid data:", error.field);
        } else {
            console.error("Unknown error:", error);
        }
        throw error;  // Re-throw if you can't handle it
    }
}

// === Optional catch binding (ES2019) ===
try {
    doSomething();
} catch {  // Don't need (error) if you don't use it
    console.log("Something went wrong");
}

// === Debugging with console ===
console.log("Basic log");
console.warn("Warning!");
console.error("Error!");
console.table([{a: 1, b: 2}, {a: 3, b: 4}]);  // Pretty table
console.time("loop");  // Start timer
for (let i = 0; i < 1000; i++) {}
console.timeEnd("loop");  // Logs elapsed time
console.trace("Trace: shows call stack");

// === Debugger statement ===
function complexFunction(x) {
    debugger;  // Pauses execution if DevTools is open
    return x * 2;
}

// === Vitest example (or Jest — almost identical) ===
import { describe, it, expect, beforeEach } from "vitest";
import { BankAccount } from "./bank-account";

describe("BankAccount", () => {
    let account;

    beforeEach(() => {
        account = new BankAccount(100);
    });

    it("starts with the initial balance", () => {
        expect(account.balance).toBe(100);
    });

    it("deposits money", () => {
        account.deposit(50);
        expect(account.balance).toBe(150);
    });

    it("rejects negative deposits", () => {
        expect(() => account.deposit(-10)).toThrow("Must be positive");
    });

    it("supports async operations", async () => {
        const result = await account.asyncOperation();
        expect(result).toBeDefined();
    });
});

// === Common test patterns ===
expect(x).toBe(y);          // Strict equality
expect(x).toEqual(y);       // Deep equality (for objects)
expect(x).toBeTruthy();     // Truthy check
expect(x).toContain("foo"); // Array/string contains
expect(() => fn()).toThrow(); // Function throws
expect(promise).resolves.toBe(value);  // Async resolves to value
```

## Try it yourself

Write a `divide(a, b)` function that throws a `DivisionByZeroError` (custom class) when `b === 0`. Write a Vitest/Jest test file with 3 tests: normal division, division by zero throws, and chained calculations work.

## Common pitfalls

- ⚠️ Catching errors and silently ignoring them. `try { ... } catch (e) {}` hides bugs. Always log or re-throw.
- ⚠️ Forgetting `await` in try/catch. `try { fetch(...) }` doesn't catch async errors because `fetch` is what returns the promise.
- ⚠️ Throwing non-Error values. Always `throw new Error('msg')` — `throw 'oops'` is bad practice (and has different stack trace behavior).

## What's next?

**Level 10: Node.js and the backend: JavaScript on the server** — coming in the next drop.

---

_Generated by Hermes · Aby's learning cron · Track: JavaScript · Level 9 · Cycle 2_