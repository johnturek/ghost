---
title: '50 Essential Copilot Prompts Every Developer Should Know'
description: 'Master GitHub Copilot with these proven prompt patterns for functions, tests, documentation, and more'
pubDate: 'Dec 15 2025'
heroImage: '../../assets/blog-placeholder-2.jpg'
---

The difference between average and exceptional Copilot results comes down to your prompts. These 50 battle-tested patterns will transform your AI-assisted coding workflow.

## Function Generation Prompts

### 1. Basic Function with Spec
```javascript
// Function that takes array of numbers and returns sum
// Should handle empty arrays (return 0)
// Should handle negative numbers
```

### 2. Async Function with Error Handling
```javascript
// Async function to fetch user data from API
// URL: https://api.example.com/users/{id}
// Return user object on success
// Throw custom UserNotFoundError if 404
// Throw NetworkError for other failures
```

### 3. Function with Complex Logic
```python
# Function to calculate compound interest
# Parameters: principal (float), rate (float), time (int), frequency (int)
# Formula: A = P(1 + r/n)^(nt)
# Return: final amount rounded to 2 decimal places
```

### 4. Generic/Type-Safe Function
```typescript
// Generic function to filter array by property value
// T: type of objects in array
// K: key of T to filter by
// Returns array of T where T[K] === value
```

### 5. Curried Function
```javascript
// Create curried function for multiplication
// multiply(2)(3) should return 6
// multiply(2)(3)(4) should return 24
```

## Data Processing Prompts

### 6. Array Transformation
```javascript
// Transform array of user objects:
// Input: [{firstName: 'John', lastName: 'Doe', age: 30}, ...]
// Output: [{fullName: 'John Doe', isAdult: true}, ...]
```

### 7. Data Validation
```python
# Validate dictionary has required fields
# Required: email, username, password
# Email must match regex pattern
# Username must be 3-20 chars alphanumeric
# Password must be 8+ chars with uppercase, lowercase, number
```

### 8. JSON Parsing with Schema
```javascript
// Parse JSON string and validate against schema
// Schema: { id: number, name: string, tags: string[] }
// Return typed object or throw ValidationError
```

### 9. CSV to Object Array
```python
# Read CSV file and convert to list of dictionaries
// Skip empty rows, trim whitespace
# Convert numeric strings to int/float
# Handle missing values (use None)
```

### 10. Group By Operation
```javascript
// Group array of transactions by date
// Input: [{date: '2024-01-01', amount: 100}, ...]
// Output: {'2024-01-01': [transactions], ...}
```

## API & HTTP Prompts

### 11. REST API GET Request
```javascript
// Fetch paginated list of posts from API
// Endpoint: GET /api/posts?page={page}&limit={limit}
// Handle pagination metadata in response
// Return { data: Post[], hasMore: boolean, total: number }
```

### 12. POST Request with FormData
```javascript
// Upload file to API with form data
// Endpoint: POST /api/upload
// Include file and metadata (title, description)
// Show upload progress
// Return uploaded file URL
```

### 13. Retry Logic
```python
# HTTP request with exponential backoff retry
# Max 3 retries, initial delay 1s, double each time
# Only retry on 5xx errors or network failures
# Raise exception after max retries
```

### 14. Batch API Requests
```javascript
// Fetch multiple users concurrently
// Takes array of user IDs
// Use Promise.all for parallel requests
# Limit to 5 concurrent requests
// Return array in same order as input IDs
```

### 15. GraphQL Query
```javascript
// Execute GraphQL query to fetch user with posts
// Query: user(id) { name, email, posts { title, createdAt } }
// Handle GraphQL errors in response
// Return typed data
```

## Testing Prompts

### 16. Unit Test Suite
```javascript
// Jest test suite for calculateDiscount function
// Test cases:
// - No discount (0%)
// - 10% discount
// - 100% discount (free)
// - Invalid discount (negative, >100)
// - Edge cases (null, undefined)
```

### 17. Mock API Responses
```javascript
// Create mock fetch function for testing
// Returns different responses based on URL
// /api/users -> mock user array
// /api/posts -> mock post array
// Other URLs -> 404 error
```

### 18. Integration Test
```python
# pytest integration test for user registration flow
# 1. POST /api/register with valid data
# 2. Verify user created in database
# 3. Verify welcome email sent
# 4. Verify user can login
# Use fixtures for database and email service
```

### 19. Test Data Factories
```typescript
// Factory function to create test user object
// Takes optional overrides for properties
// Returns user with realistic fake data using faker
// Includes: id, email, name, createdAt, etc.
```

### 20. Snapshot Testing
```javascript
// React component snapshot test
// Render UserProfile component with mock data
// Test different states: loading, success, error
// Create snapshots for each state
```

## Database Prompts

### 21. SQL Query Builder
```python
# Build parameterized SQL query
# SELECT * FROM users WHERE status = ? AND created_at > ?
# Return query string and parameters tuple
# Prevent SQL injection
```

### 22. ORM Query
```javascript
// Sequelize query to find users with specific criteria
// Find users who:
// - Registered in last 30 days
// - Have more than 5 posts
// - Email domain is 'example.com'
// Include post count, order by registration date desc
```

### 23. Database Migration
```sql
-- Create migration to add indexes
-- Add index on users.email (unique)
-- Add composite index on posts (user_id, created_at)
-- Add index on comments.post_id
-- Should be reversible
```

### 24. Bulk Insert
```python
# Insert multiple records efficiently
# Use bulk insert to add 1000 users
# Batch size: 100 records at a time
# Handle duplicates (skip)
# Return count of inserted records
```

### 25. Transaction Handling
```javascript
// Execute database operations in transaction
// 1. Create user
// 2. Create user profile
// 3. Send welcome email
// Rollback if any step fails
// Use async/await with try/catch
```

## React Component Prompts

### 26. Functional Component
```typescript
// React functional component for user profile card
// Props: user { name, email, avatar, bio }
// Display avatar image, name, email
// Clicking card opens detail modal
// Include loading and error states
```

### 27. Custom Hook
```javascript
// Custom hook useLocalStorage
// Works like useState but persists to localStorage
// Parameters: key (string), initialValue (any)
// Returns [value, setValue] like useState
// Handle JSON serialization/parsing
```

### 28. Form with Validation
```typescript
// React form component for user registration
// Fields: email, password, confirmPassword
// Validation: email format, password min 8 chars, passwords match
// Show errors on blur
// Disable submit while invalid
// Call onSubmit prop with form data
```

### 29. Data Fetching Component
```javascript
// Component that fetches and displays post list
// Use useEffect to fetch on mount
// Show loading spinner while fetching
// Display error message if fetch fails
// Map posts to PostCard components
```

### 30. Context Provider
```typescript
// Create AuthContext for user authentication
// Provider manages: user, login(), logout(), isAuthenticated
// Use React.createContext and custom hook useAuth
// Persist auth state to localStorage
```

## Utility Function Prompts

### 31. Debounce
```javascript
// Create debounce utility function
// Delays function execution until after wait milliseconds
// If called again during wait, reset timer
// Return function that can be cancelled
```

### 32. Deep Clone
```javascript
// Deep clone object including nested arrays and objects
// Handle circular references
// Preserve Date objects, RegExp, etc.
// Return null for null input
```

### 33. Throttle
```javascript
// Throttle function execution
// Only execute once per interval (e.g. 1000ms)
// First call executes immediately
// Subsequent calls within interval are ignored
// Include trailing option
```

### 34. Format Currency
```javascript
// Format number as currency
// Parameters: amount (number), currency (string), locale (string)
// Examples:
// formatCurrency(1234.56, 'USD', 'en-US') -> '$1,234.56'
// formatCurrency(1234.56, 'EUR', 'de-DE') -> '1.234,56 €'
```

### 35. Pluralize
```javascript
// Pluralize word based on count
// Parameters: count (number), singular (string), plural (string)
// Examples:
// pluralize(1, 'item') -> '1 item'
// pluralize(5, 'item') -> '5 items'
// pluralize(1, 'person', 'people') -> '1 person'
```

## Documentation Prompts

### 36. JSDoc Function Documentation
```javascript
/**
 * // Add complete JSDoc documentation for this function
 */
function calculateDistance(lat1, lon1, lat2, lon2) {
  // Haversine formula implementation
}
```

### 37. README Generation
```markdown
<!-- Generate README.md for this project
 * Include: Description, Installation, Usage, API Reference, License
 * Project is a REST API for todo management
 * Tech stack: Node.js, Express, PostgreSQL
 * Include code examples for main endpoints -->
```

### 38. API Documentation
```javascript
// Document this API endpoint using OpenAPI/Swagger format
// POST /api/users
// Creates new user
// Request body: { email, password, name }
// Responses: 201 (created), 400 (validation error), 409 (duplicate)
```

### 39. TypeScript Interface from Example
```typescript
// Create TypeScript interface from this example object
const user = {
  id: 1,
  email: 'user@example.com',
  profile: {
    firstName: 'John',
    lastName: 'Doe',
    age: 30
  },
  posts: [
    { id: 1, title: 'Hello', likes: 5 }
  ]
};
```

### 40. Error Message Docs
```javascript
// Document all possible errors this function can throw
// Include error codes, messages, and when they occur
async function processPayment(amount, cardToken) {
  // Payment processing logic
}
```

## Regex & String Manipulation

### 41. Email Validation Regex
```javascript
// Create regex pattern to validate email addresses
// Must support:
// - Standard emails: user@example.com
// - Subdomains: user@mail.example.com
// - Plus addressing: user+tag@example.com
// - International domains: user@example.co.uk
```

### 42. URL Parser
```javascript
// Parse URL string into components
// Extract: protocol, host, port, path, query params, fragment
// Handle: relative URLs, URLs without protocol
// Return object with parsed components
```

### 43. Slug Generator
```javascript
// Convert string to URL-friendly slug
// Input: "Hello World! This is a TEST"
// Output: "hello-world-this-is-a-test"
// Rules: lowercase, spaces to hyphens, remove special chars
```

### 44. Extract Mentions
```javascript
// Extract @mentions from text
// Input: "Hello @john and @jane_doe, welcome!"
// Output: ['john', 'jane_doe']
// Match pattern: @followed by alphanumeric and underscores
```

### 45. Mask Sensitive Data
```javascript
// Mask sensitive information in string
// Credit card: mask all but last 4 digits
// Email: mask characters before @
// Phone: mask all but last 4 digits
// Examples:
// maskCC('1234567890123456') -> '************3456'
// maskEmail('user@example.com') -> 'u***@example.com'
```

## Algorithm Prompts

### 46. Binary Search
```python
# Implement binary search algorithm
# Parameter: sorted array of numbers, target value
# Return: index of target, or -1 if not found
# Time complexity: O(log n)
# Include type hints
```

### 47. Merge Sort
```javascript
// Implement merge sort algorithm
// Parameter: array of numbers
// Return: sorted array (ascending)
// Use divide and conquer approach
// Add comments explaining each step
```

### 48. LRU Cache
```typescript
// Implement Least Recently Used (LRU) cache
// Methods: get(key), put(key, value)
// Fixed capacity, evict least recently used on overflow
// O(1) time complexity for both operations
// Use Map for order preservation
```

### 49. Dijkstra's Algorithm
```python
# Implement Dijkstra's shortest path algorithm
# Parameter: graph as adjacency list, start node
# Return: dictionary with shortest distances to all nodes
# Handle: weighted edges, disconnected nodes
# Use priority queue for efficiency
```

### 50. Rate Limiter
```javascript
// Implement sliding window rate limiter
// Limit: 100 requests per minute per user
// Method: allowRequest(userId) -> boolean
// Store timestamps of requests
// Clean up old timestamps
// Use Map for per-user tracking
```

## Pro Tips for Using These Prompts

### 1. Customize for Your Codebase
Add your specific naming conventions, libraries, and patterns.

### 2. Combine Prompts
Use multiple patterns together for complex tasks:
```javascript
// Async function with retry logic (Prompts #2 + #13)
// to fetch user data with exponential backoff
```

### 3. Iterate
Start with basic prompt, then refine:
```javascript
// Function to sort users
// Function to sort users by last name, then first name
// Function to sort users by last name (ascending), first name (ascending), null values last
```

### 4. Provide Examples
When Copilot doesn't understand, show input/output:
```javascript
// Transform data structure
// Input: {a: 1, b: 2, c: 3}
// Output: [{key: 'a', value: 1}, {key: 'b', value: 2}, {key: 'c', value: 3}]
```

### 5. Use Naming Conventions
Function names hint at implementation:
```javascript
// Copilot infers this should return boolean
function isValidEmail(email) {

// Copilot infers this should return string
function getUserFullName(user) {
```

## Measuring Success

Track improvements after using these prompts:
- **Time saved** per coding session
- **Code quality** (fewer bugs in AI-generated code)
- **Consistency** across your codebase
- **Learning** (new patterns discovered)

## Next Steps

1. **Bookmark** this page for quick reference
2. **Copy** prompts and modify for your needs
3. **Experiment** with variations
4. **Share** successful prompts with your team
5. **Create** a personal prompt library

These 50 prompts cover 90% of daily coding tasks. Master them, and you'll unlock Copilot's full potential!
