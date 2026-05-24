# JavaScript Patterns & Reference

---

## Async / Await

```javascript
// Basic async function
async function fetchUser(id) {
  try {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Failed to fetch user:', error);
    throw error;
  }
}

// Run multiple requests in parallel
const [user, posts, comments] = await Promise.all([
  fetchUser(1),
  fetchPosts(1),
  fetchComments(1),
]);

// First to resolve/reject
const result = await Promise.race([
  fetchWithTimeout(url, 5000),
  delay(5000).then(() => { throw new Error('Timeout'); }),
]);

// All settled (doesn't reject on failure)
const results = await Promise.allSettled([p1, p2, p3]);
results.forEach(r => {
  if (r.status === 'fulfilled') console.log(r.value);
  else console.error(r.reason);
});
```

---

## Array Methods

```javascript
const users = [
  { name: 'Alice', age: 30, role: 'admin' },
  { name: 'Bob',   age: 25, role: 'user'  },
  { name: 'Carol', age: 35, role: 'admin' },
];

// filter — keep matching items
const admins = users.filter(u => u.role === 'admin');

// map — transform items
const names = users.map(u => u.name);
const withFullName = users.map(u => ({ ...u, fullName: `${u.name} Smith` }));

// find — first match
const bob = users.find(u => u.name === 'Bob');

// reduce — accumulate
const totalAge = users.reduce((sum, u) => sum + u.age, 0);
const byRole = users.reduce((acc, u) => {
  acc[u.role] = [...(acc[u.role] || []), u];
  return acc;
}, {});

// sort (non-mutating)
const sorted = [...users].sort((a, b) => a.age - b.age);

// every / some
const allAdults  = users.every(u => u.age >= 18);
const hasAdmin   = users.some(u => u.role === 'admin');

// flat / flatMap
const nested = [[1, 2], [3, 4]];
const flat   = nested.flat();
const doubled = nested.flatMap(arr => arr.map(x => x * 2));
```

---

## Destructuring & Spread

```javascript
// Array destructuring
const [first, second, ...rest] = [1, 2, 3, 4, 5];
const [,, third] = [1, 2, 3];  // skip elements

// Object destructuring
const { name, age, role = 'user' } = user;  // with default
const { name: userName } = user;             // rename

// Nested
const { address: { city, postcode } } = user;

// Spread — merge objects
const updated = { ...user, age: 31, updatedAt: new Date() };

// Spread — merge arrays
const combined = [...arr1, ...arr2];

// Function params
function greet({ name, age = 0 }) {
  return `Hello ${name}, you are ${age}`;
}
```

---

## Error Handling

```javascript
// Custom error class
class ApiError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.name    = 'ApiError';
    this.statusCode = statusCode;
  }
}

// Throw and catch custom error
try {
  throw new ApiError('Not found', 404);
} catch (error) {
  if (error instanceof ApiError) {
    console.log(error.statusCode); // 404
  }
}

// Global unhandled rejection handler
process.on('unhandledRejection', (reason, promise) => {
  console.error('Unhandled rejection:', reason);
});
```

---

## Useful Patterns

```javascript
// Optional chaining
const city = user?.address?.city;
const first = arr?.[0];
const result = obj?.method?.();

// Nullish coalescing
const name = user.name ?? 'Anonymous';   // only null/undefined
const port = config.port || 3000;        // falsy fallback

// Short circuit evaluation
isLoggedIn && renderDashboard();
user || createGuestUser();

// Debounce
function debounce(fn, delay) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}
const debouncedSearch = debounce(search, 300);

// Throttle
function throttle(fn, limit) {
  let lastCall = 0;
  return (...args) => {
    const now = Date.now();
    if (now - lastCall >= limit) {
      lastCall = now;
      return fn(...args);
    }
  };
}

// Deep clone (modern)
const clone = structuredClone(obj);

// Unique array values
const unique = [...new Set(arr)];

// Group by
const grouped = Object.groupBy(users, u => u.role);  // ES2024
```

---

## Local Storage / Session Storage

```javascript
// Store
localStorage.setItem('token', JSON.stringify({ value: 'abc', exp: Date.now() }));

// Retrieve
const token = JSON.parse(localStorage.getItem('token'));

// Remove
localStorage.removeItem('token');
localStorage.clear();

// Helper
const storage = {
  get: (key) => { try { return JSON.parse(localStorage.getItem(key)); } catch { return null; } },
  set: (key, value) => localStorage.setItem(key, JSON.stringify(value)),
  remove: (key) => localStorage.removeItem(key),
};
```

---

## Fetch API Wrapper

```javascript
async function api(endpoint, options = {}) {
  const baseUrl = process.env.API_URL || 'https://api.example.com';
  const token   = localStorage.getItem('token');

  const response = await fetch(`${baseUrl}${endpoint}`, {
    headers: {
      'Content-Type': 'application/json',
      ...(token && { Authorization: `Bearer ${token}` }),
      ...options.headers,
    },
    ...options,
  });

  if (!response.ok) {
    const error = await response.json().catch(() => ({}));
    throw new ApiError(error.message || 'Request failed', response.status);
  }

  return response.json();
}

// Usage
const user  = await api('/users/1');
const newUser = await api('/users', { method: 'POST', body: JSON.stringify(userData) });
```
