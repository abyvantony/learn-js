# Level 10: Node.js and the backend: JavaScript on the server

**Track:** JavaScript · **Level:** 10/12 · **Cycle:** 2 · **Difficulty:** `advanced`

📚 **Today's lesson** — published 2026-07-09

## TL;DR

Node.js is JavaScript outside the browser. With it, you can build servers, CLI tools, scripts — anything that needs to read files, talk to databases, or respond to network requests. Combined with npm, it's a complete backend stack.

## Real-world analogies

- Browser JavaScript is the front of the restaurant (host, waiter). Node.js is the kitchen — it does the work behind the scenes, then sends the result out.
- Express is the order ticket system. It receives orders (HTTP requests), routes them to the right kitchen station (handler), and sends back the food (response).

## Key concepts

### `The runtime`

Node.js adds browser-less APIs to JavaScript: `fs` (filesystem), `path` (file paths), `http` (HTTP server), `os` (operating system), `process` (current process).

### `Express`

The most popular Node.js web framework. Light, unopinionated. Defines routes that match URL patterns and run handler functions.

### `Middleware`

Functions that run before/after handlers. Logging, authentication, body parsing — all middleware. Composable with `app.use()`.

### `Environment variables`

`process.env.PORT`, `process.env.DATABASE_URL`. Config that changes between dev/staging/prod. Use the `dotenv` package to load from `.env` files.

## Code with comments

Every line has a comment. Read it slowly.

```
// === A minimal Node.js HTTP server (no framework) ===
import { createServer } from "node:http";

const server = createServer((req, res) => {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello from Node!");
});

server.listen(3000, () => console.log("Server on http://localhost:3000"));

// === Express: the popular framework ===
import express from "express";
const app = express();

// Middleware
app.use(express.json());  // Parse JSON request bodies
app.use((req, res, next) => {
    console.log(`${req.method} ${req.url}`);
    next();  // Pass to next middleware
});

// Routes
app.get("/", (req, res) => {
    res.send("Hello!");
});

app.get("/users/:id", (req, res) => {
    const userId = req.params.id;
    res.json({ id: userId, name: "Aby" });
});

app.post("/users", (req, res) => {
    const user = req.body;
    // Save to database...
    res.status(201).json(user);
});

app.listen(3000, () => console.log("API on port 3000"));

// === Reading files ===
import { readFile, writeFile } from "node:fs/promises";

const content = await readFile("package.json", "utf8");
const pkg = JSON.parse(content);

await writeFile("output.txt", "Hello!", "utf8");

// === Path handling (cross-platform) ===
import { join, dirname, basename } from "node:path";
const fullPath = join("/users", "aby", "file.txt");  // "/users/aby/file.txt"

// === Environment variables ===
import "dotenv/config";
const port = process.env.PORT || 3000;
const dbUrl = process.env.DATABASE_URL;

// === A complete Express CRUD API ===
import express from "express";
import { readFile, writeFile } from "node:fs/promises";

const app = express();
app.use(express.json());

const DB_FILE = "./users.json";

async function getUsers() {
    try {
        const data = await readFile(DB_FILE, "utf8");
        return JSON.parse(data);
    } catch {
        return [];
    }
}

async function saveUsers(users) {
    await writeFile(DB_FILE, JSON.stringify(users, null, 2));
}

app.get("/api/users", async (req, res) => {
    const users = await getUsers();
    res.json(users);
});

app.post("/api/users", async (req, res) => {
    const users = await getUsers();
    const newUser = { id: Date.now(), ...req.body };
    users.push(newUser);
    await saveUsers(users);
    res.status(201).json(newUser);
});

app.get("/api/users/:id", async (req, res) => {
    const users = await getUsers();
    const user = users.find(u => u.id === parseInt(req.params.id));
    if (!user) return res.status(404).json({ error: "Not found" });
    res.json(user);
});

app.listen(3000);

// === npm scripts in package.json ===
{
    "scripts": {
        "start": "node server.js",
        "dev": "node --watch server.js",  // Auto-restart on file change (Node 18+)
        "test": "vitest",
        "lint": "eslint ."
    }
}
```

## Try it yourself

Build a Node.js + Express server with two routes: `GET /api/notes` and `POST /api/notes`. Store notes in a JSON file. Test it with `curl` from the command line.

## Common pitfalls

- ⚠️ Blocking the event loop. A `while` loop running 1B iterations freezes the server. Use async APIs for I/O.
- ⚠️ Not handling promise rejections in Express. Unhandled rejection = crashed server in newer Node. Always `.catch()` or `try/catch`.
- ⚠️ Storing secrets in code. Use `process.env` + `.env` files. Never commit `.env` to git.

## What's next?

**Level 11: React and the modern frontend: components, state, effects** — coming in the next drop.

---

_Generated by Hermes · Aby's learning cron · Track: JavaScript · Level 10 · Cycle 2_