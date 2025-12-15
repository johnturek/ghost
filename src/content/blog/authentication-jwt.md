---
title: 'JWT Authentication: Secure Your Web Apps'
description: 'Learn how to implement secure user authentication using JSON Web Tokens in your applications'
pubDate: 'Dec 18 2025'
heroImage: '/blog-placeholder-1.jpg'
---

JSON Web Tokens (JWT) are the industry standard for secure authentication in modern web applications. Used by companies like Auth0, Firebase, and AWS Cognito, JWTs provide a stateless, scalable way to authenticate users.

## What is JWT?

A JWT is a compact, URL-safe token that contains JSON data. It consists of three parts:

1. **Header** - Algorithm and token type
2. **Payload** - Claims (user data)
3. **Signature** - Verification

Example JWT:
\`\`\`
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIn0.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
\`\`\`

## How JWT Works

1. User logs in with credentials
2. Server validates and creates JWT
3. Server sends JWT to client
4. Client stores JWT (localStorage/cookie)
5. Client sends JWT with each request
6. Server verifies JWT and processes request

## Implementation

### Backend (Node.js/Express)

\`\`\`javascript
const jwt = require('jsonwebtoken');
const bcrypt = require('bcrypt');

// Register
app.post('/register', async (req, res) => {
  const { email, password } = req.body;

  // Hash password
  const hashedPassword = await bcrypt.hash(password, 10);

  // Save user to database
  const user = await db.user.create({
    email,
    password: hashedPassword,
  });

  res.json({ message: 'User created' });
});

// Login
app.post('/login', async (req, res) => {
  const { email, password } = req.body;

  // Find user
  const user = await db.user.findOne({ where: { email } });
  if (!user) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }

  // Verify password
  const validPassword = await bcrypt.compare(password, user.password);
  if (!validPassword) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }

  // Create JWT
  const token = jwt.sign(
    { userId: user.id, email: user.email },
    process.env.JWT_SECRET,
    { expiresIn: '7d' }
  );

  res.json({ token });
});

// Protected route
app.get('/profile', authenticateToken, async (req, res) => {
  const user = await db.user.findById(req.user.userId);
  res.json(user);
});

// Middleware
function authenticateToken(req, res, next) {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];

  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }

  jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
    if (err) {
      return res.status(403).json({ error: 'Invalid token' });
    }
    req.user = user;
    next();
  });
}
\`\`\`

### Frontend (React)

\`\`\`javascript
// Login
async function login(email, password) {
  const response = await fetch('/api/login', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password }),
  });

  const { token } = await response.json();

  // Store token
  localStorage.setItem('token', token);
}

// Make authenticated request
async function fetchProfile() {
  const token = localStorage.getItem('token');

  const response = await fetch('/api/profile', {
    headers: {
      'Authorization': \`Bearer \${token}\`,
    },
  });

  return response.json();
}

// Axios interceptor
axios.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = \`Bearer \${token}\`;
  }
  return config;
});
\`\`\`

## Refresh Tokens

For better security, use refresh tokens:

\`\`\`javascript
app.post('/login', async (req, res) => {
  // ... validate user

  const accessToken = jwt.sign(
    { userId: user.id },
    process.env.JWT_SECRET,
    { expiresIn: '15m' } // Short-lived
  );

  const refreshToken = jwt.sign(
    { userId: user.id },
    process.env.REFRESH_TOKEN_SECRET,
    { expiresIn: '7d' } // Long-lived
  );

  // Store refresh token in database
  await db.refreshToken.create({
    token: refreshToken,
    userId: user.id,
  });

  res.json({ accessToken, refreshToken });
});

app.post('/refresh', async (req, res) => {
  const { refreshToken } = req.body;

  // Verify refresh token
  const tokenRecord = await db.refreshToken.findOne({
    where: { token: refreshToken },
  });

  if (!tokenRecord) {
    return res.status(403).json({ error: 'Invalid refresh token' });
  }

  jwt.verify(refreshToken, process.env.REFRESH_TOKEN_SECRET, (err, user) => {
    if (err) {
      return res.status(403).json({ error: 'Invalid refresh token' });
    }

    // Create new access token
    const accessToken = jwt.sign(
      { userId: user.userId },
      process.env.JWT_SECRET,
      { expiresIn: '15m' }
    );

    res.json({ accessToken });
  });
});
\`\`\`

## Best Practices

1. **Use HTTPS** - Always
2. **Short expiration** - 15-30 minutes for access tokens
3. **Secure storage** - httpOnly cookies or secure storage
4. **Validate on every request** - Don't trust tokens
5. **Use strong secrets** - 256-bit random strings
6. **Implement refresh tokens** - For better UX
7. **Blacklist tokens** - For logout
8. **Rate limit** - Prevent brute force

## Security Considerations

\`\`\`javascript
// Don't store sensitive data in JWT
const token = jwt.sign(
  { userId: user.id }, // ✅ OK
  // { password: user.password }, // ❌ Never!
  process.env.JWT_SECRET
);

// Use httpOnly cookies (immune to XSS)
res.cookie('token', token, {
  httpOnly: true,
  secure: true,
  sameSite: 'strict',
  maxAge: 7 * 24 * 60 * 60 * 1000, // 7 days
});

// Implement logout (token blacklist)
app.post('/logout', authenticateToken, async (req, res) => {
  await db.tokenBlacklist.create({
    token: req.token,
    expiresAt: req.user.exp,
  });
  res.json({ message: 'Logged out' });
});
\`\`\`

Secure your apps with JWT!
