---
title: 'Building RESTful APIs with Node.js'
description: 'A practical guide to creating scalable REST APIs using Node.js and Express'
pubDate: 'Dec 12 2025'
heroImage: '../../assets/restful-api-hero.jpg'
---

RESTful APIs are the backbone of modern web applications. They allow your frontend to communicate with your backend, enable mobile apps to fetch data, and make it possible for different services to work together. Let's build one with Node.js and Express.

## What is a REST API?

REST (Representational State Transfer) is an architectural style for designing networked applications. A RESTful API uses HTTP requests to perform CRUD operations:

- **GET**: Retrieve data
- **POST**: Create new data
- **PUT/PATCH**: Update existing data
- **DELETE**: Remove data

## Setting Up Your Project

First, create a new Node.js project:

```bash
mkdir my-api
cd my-api
npm init -y
npm install express
```

## Creating Your First Endpoint

Create `server.js`:

```javascript
const express = require('express');
const app = express();
const PORT = 3000;

// Middleware to parse JSON
app.use(express.json());

// Sample data
let users = [
  { id: 1, name: 'Alice', email: 'alice@example.com' },
  { id: 2, name: 'Bob', email: 'bob@example.com' }
];

// GET all users
app.get('/api/users', (req, res) => {
  res.json(users);
});

// GET single user
app.get('/api/users/:id', (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));
  if (!user) return res.status(404).json({ message: 'User not found' });
  res.json(user);
});

// POST create user
app.post('/api/users', (req, res) => {
  const newUser = {
    id: users.length + 1,
    name: req.body.name,
    email: req.body.email
  };
  users.push(newUser);
  res.status(201).json(newUser);
});

// PUT update user
app.put('/api/users/:id', (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));
  if (!user) return res.status(404).json({ message: 'User not found' });

  user.name = req.body.name;
  user.email = req.body.email;
  res.json(user);
});

// DELETE user
app.delete('/api/users/:id', (req, res) => {
  const index = users.findIndex(u => u.id === parseInt(req.params.id));
  if (index === -1) return res.status(404).json({ message: 'User not found' });

  users.splice(index, 1);
  res.status(204).send();
});

app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

## REST API Best Practices

### 1. Use Nouns, Not Verbs

✅ Good: `/api/users`
❌ Bad: `/api/getUsers`

The HTTP method already indicates the action.

### 2. Use Plural Nouns

✅ Good: `/api/users/123`
❌ Bad: `/api/user/123`

### 3. Use Proper HTTP Status Codes

- `200 OK`: Successful GET, PUT, PATCH
- `201 Created`: Successful POST
- `204 No Content`: Successful DELETE
- `400 Bad Request`: Invalid input
- `404 Not Found`: Resource doesn't exist
- `500 Internal Server Error`: Server error

### 4. Version Your API

```javascript
app.get('/api/v1/users', (req, res) => {
  // Version 1 implementation
});
```

### 5. Implement Filtering and Pagination

```javascript
app.get('/api/users', (req, res) => {
  let result = users;

  // Filter by name
  if (req.query.name) {
    result = result.filter(u =>
      u.name.toLowerCase().includes(req.query.name.toLowerCase())
    );
  }

  // Pagination
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 10;
  const startIndex = (page - 1) * limit;
  const endIndex = page * limit;

  const paginatedResults = result.slice(startIndex, endIndex);

  res.json({
    data: paginatedResults,
    page,
    totalPages: Math.ceil(result.length / limit)
  });
});
```

## Adding Middleware

Middleware functions can modify requests before they reach your route handlers:

```javascript
// Logging middleware
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next();
});

// Authentication middleware
const authenticate = (req, res, next) => {
  const token = req.headers.authorization;
  if (!token) return res.status(401).json({ message: 'Unauthorized' });
  // Verify token...
  next();
};

app.get('/api/protected', authenticate, (req, res) => {
  res.json({ message: 'This is protected' });
});
```

## Error Handling

Implement centralized error handling:

```javascript
// Error handling middleware (must be last)
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({
    message: 'Something went wrong!',
    error: process.env.NODE_ENV === 'development' ? err.message : {}
  });
});
```

## Connecting to a Database

For production, replace the in-memory array with a real database:

```javascript
// Example with MongoDB and Mongoose
const mongoose = require('mongoose');

const UserSchema = new mongoose.Schema({
  name: String,
  email: String
});

const User = mongoose.model('User', UserSchema);

app.get('/api/users', async (req, res) => {
  try {
    const users = await User.find();
    res.json(users);
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
});
```

## Testing Your API

Use tools like:
- **Postman**: GUI for testing APIs
- **curl**: Command-line testing
- **Jest + Supertest**: Automated testing

Example curl command:

```bash
curl -X POST http://localhost:3000/api/users \
  -H "Content-Type: application/json" \
  -d '{"name":"Charlie","email":"charlie@example.com"}'
```

## Next Steps

1. Add input validation (try `express-validator`)
2. Implement authentication (JWT, OAuth)
3. Add rate limiting to prevent abuse
4. Document your API (try Swagger/OpenAPI)
5. Deploy to production (Heroku, AWS, DigitalOcean)

Building RESTful APIs with Node.js is straightforward once you understand the patterns. Start simple, follow best practices, and scale as needed!
