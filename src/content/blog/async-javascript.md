---
title: 'Async JavaScript: Promises, Async/Await, and Beyond'
description: 'Master asynchronous programming in JavaScript and write clean, efficient code'
pubDate: 'Dec 19 2025'
heroImage: '/blog-placeholder-1.jpg'
---

Asynchronous programming is fundamental to modern JavaScript. From API calls to file operations, understanding async code is essential for building responsive applications.

## The Problem: Blocking Code

\`\`\`javascript
// Synchronous (blocking)
const data = fetchDataFromAPI(); // Waits here
console.log(data);
console.log('Done'); // Runs after fetch completes
\`\`\`

This blocks the entire program until the fetch completes!

## Callbacks (Old Way)

\`\`\`javascript
fetchDataFromAPI((error, data) => {
  if (error) {
    console.error(error);
    return;
  }
  console.log(data);
});

console.log('Done'); // Runs immediately
\`\`\`

**Problem: Callback Hell**
\`\`\`javascript
getUser(userId, (error, user) => {
  getPosts(user.id, (error, posts) => {
    getComments(posts[0].id, (error, comments) => {
      // Nested nightmare!
    });
  });
});
\`\`\`

## Promises (Better Way)

\`\`\`javascript
const promise = fetchDataFromAPI();

promise
  .then(data => {
    console.log(data);
    return processData(data);
  })
  .then(result => {
    console.log(result);
  })
  .catch(error => {
    console.error(error);
  })
  .finally(() => {
    console.log('Cleanup');
  });
\`\`\`

### Creating Promises

\`\`\`javascript
function wait(ms) {
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve('Done!'), ms);
  });
}

wait(1000).then(message => console.log(message));
\`\`\`

### Promise Chaining

\`\`\`javascript
fetchUser(userId)
  .then(user => fetchPosts(user.id))
  .then(posts => fetchComments(posts[0].id))
  .then(comments => console.log(comments))
  .catch(error => console.error(error));
\`\`\`

## Async/Await (Best Way)

\`\`\`javascript
async function loadUserData() {
  try {
    const user = await fetchUser(userId);
    const posts = await fetchPosts(user.id);
    const comments = await fetchComments(posts[0].id);
    console.log(comments);
  } catch (error) {
    console.error(error);
  }
}
\`\`\`

Much cleaner! Reads like synchronous code.

### Error Handling

\`\`\`javascript
async function fetchData() {
  try {
    const response = await fetch('/api/data');

    if (!response.ok) {
      throw new Error(\`HTTP error! status: \${response.status}\`);
    }

    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Failed to fetch:', error);
    throw error;
  }
}
\`\`\`

## Parallel Execution

### Promise.all (Run in Parallel)

\`\`\`javascript
async function loadAllData() {
  // All run simultaneously
  const [users, posts, comments] = await Promise.all([
    fetchUsers(),
    fetchPosts(),
    fetchComments(),
  ]);

  console.log({ users, posts, comments });
}
\`\`\`

**Note:** If any promise rejects, Promise.all rejects immediately.

### Promise.allSettled (Continue Regardless)

\`\`\`javascript
const results = await Promise.allSettled([
  fetchUsers(),
  fetchPosts(),
  fetchComments(),
]);

results.forEach((result, index) => {
  if (result.status === 'fulfilled') {
    console.log(\`Success \${index}:\`, result.value);
  } else {
    console.log(\`Error \${index}:\`, result.reason);
  }
});
\`\`\`

### Promise.race (First to Complete)

\`\`\`javascript
const fastest = await Promise.race([
  fetchFromAPI1(),
  fetchFromAPI2(),
  fetchFromAPI3(),
]);

console.log('First response:', fastest);
\`\`\`

### Promise.any (First to Succeed)

\`\`\`javascript
try {
  const first = await Promise.any([
    fetchFromAPI1(), // Might fail
    fetchFromAPI2(), // Might fail
    fetchFromAPI3(), // Might fail
  ]);
  console.log('First success:', first);
} catch (error) {
  console.log('All failed:', error);
}
\`\`\`

## Common Patterns

### Sequential Execution

\`\`\`javascript
async function processItems(items) {
  for (const item of items) {
    await processItem(item); // Wait for each
  }
}
\`\`\`

### Parallel Execution

\`\`\`javascript
async function processItems(items) {
  await Promise.all(
    items.map(item => processItem(item))
  );
}
\`\`\`

### Retry Logic

\`\`\`javascript
async function fetchWithRetry(url, retries = 3) {
  for (let i = 0; i < retries; i++) {
    try {
      return await fetch(url);
    } catch (error) {
      if (i === retries - 1) throw error;
      await wait(1000 * (i + 1)); // Exponential backoff
    }
  }
}
\`\`\`

### Timeout

\`\`\`javascript
function timeout(promise, ms) {
  return Promise.race([
    promise,
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error('Timeout')), ms)
    ),
  ]);
}

try {
  const data = await timeout(fetchData(), 5000);
} catch (error) {
  console.log('Request timed out');
}
\`\`\`

## Real-World Examples

### API Calls

\`\`\`javascript
async function searchMovies(query) {
  try {
    const response = await fetch(
      \`https://api.movies.com/search?q=\${query}\`
    );
    const data = await response.json();
    return data.results;
  } catch (error) {
    console.error('Search failed:', error);
    return [];
  }
}
\`\`\`

### File Upload

\`\`\`javascript
async function uploadFile(file) {
  const formData = new FormData();
  formData.append('file', file);

  try {
    const response = await fetch('/upload', {
      method: 'POST',
      body: formData,
    });

    const result = await response.json();
    return result.url;
  } catch (error) {
    throw new Error('Upload failed');
  }
}
\`\`\`

### Batching Requests

\`\`\`javascript
async function fetchInBatches(ids, batchSize = 10) {
  const results = [];

  for (let i = 0; i < ids.length; i += batchSize) {
    const batch = ids.slice(i, i + batchSize);
    const batchResults = await Promise.all(
      batch.map(id => fetchItem(id))
    );
    results.push(...batchResults);
  }

  return results;
}
\`\`\`

## Common Mistakes

### Forgetting await

\`\`\`javascript
// ❌ Wrong
async function getData() {
  const data = fetchData(); // Returns promise, not data!
  console.log(data); // Promise object
}

// ✅ Correct
async function getData() {
  const data = await fetchData();
  console.log(data); // Actual data
}
\`\`\`

### Sequential instead of Parallel

\`\`\`javascript
// ❌ Slow (sequential)
const user = await fetchUser();
const posts = await fetchPosts();

// ✅ Fast (parallel)
const [user, posts] = await Promise.all([
  fetchUser(),
  fetchPosts(),
]);
\`\`\`

### Unhandled Promise Rejection

\`\`\`javascript
// ❌ Silent failure
async function loadData() {
  await fetchData(); // No try/catch!
}

// ✅ Proper error handling
async function loadData() {
  try {
    await fetchData();
  } catch (error) {
    console.error(error);
  }
}
\`\`\`

Master async JavaScript for modern web development!
