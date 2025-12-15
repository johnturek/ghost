---
title: 'MongoDB with Node.js: Complete Guide for Modern Apps'
description: 'Master MongoDB integration with Node.js for flexible, scalable NoSQL database solutions'
pubDate: 'Dec 15 2025'
heroImage: '/blog-placeholder-1.jpg'
---

MongoDB is the leading NoSQL database, perfect for applications requiring flexible schemas and horizontal scaling. Learn how to integrate it seamlessly with Node.js.

## Why MongoDB?

MongoDB stores data in flexible, JSON-like documents, making it natural to work with in JavaScript applications.

**Key Features:**
- **Schema flexibility** - No predefined structure required
- **Horizontal scaling** - Easy sharding across servers
- **Rich query language** - Complex queries with aggregation
- **High performance** - Optimized for read/write operations

**When to Use MongoDB:**
- Rapidly evolving data models
- Large volumes of unstructured data
- Real-time analytics
- Content management systems

## Installation

**Install MongoDB:**
```bash
# macOS
brew tap mongodb/brew
brew install mongodb-community

# Start MongoDB
brew services start mongodb-community

# Or use Docker
docker run -d -p 27017:27017 --name mongodb mongo:latest
```

**Install Node.js Driver:**
```bash
npm install mongodb
# Or use Mongoose ODM
npm install mongoose
```

## Native MongoDB Driver

**Basic connection:**
```javascript
const { MongoClient } = require('mongodb');

const uri = 'mongodb://localhost:27017';
const client = new MongoClient(uri);

async function run() {
  try {
    await client.connect();
    console.log('Connected to MongoDB');
    
    const database = client.db('myapp');
    const users = database.collection('users');
    
    // Perform operations
    
  } finally {
    await client.close();
  }
}

run().catch(console.error);
```

**CRUD Operations:**
```javascript
// Create
const result = await users.insertOne({
  name: 'Alice Johnson',
  email: 'alice@example.com',
  age: 28,
  createdAt: new Date()
});
console.log('Inserted ID:', result.insertedId);

// Insert multiple
await users.insertMany([
  { name: 'Bob Smith', email: 'bob@example.com', age: 32 },
  { name: 'Carol White', email: 'carol@example.com', age: 25 }
]);

// Read - Find one
const user = await users.findOne({ email: 'alice@example.com' });

// Read - Find many
const allUsers = await users.find({}).toArray();

// Read - With filter
const adults = await users.find({ age: { $gte: 18 } }).toArray();

// Update - One document
await users.updateOne(
  { email: 'alice@example.com' },
  { $set: { age: 29 } }
);

// Update - Multiple documents
await users.updateMany(
  { age: { $lt: 30 } },
  { $set: { category: 'young' } }
);

// Delete - One document
await users.deleteOne({ email: 'alice@example.com' });

// Delete - Multiple documents
await users.deleteMany({ age: { $lt: 18 } });
```

## Query Operators

**Comparison:**
```javascript
// Equal
users.find({ age: 25 })

// Greater than / Less than
users.find({ age: { $gt: 30 } })
users.find({ age: { $gte: 18, $lte: 65 } })

// Not equal
users.find({ status: { $ne: 'inactive' } })

// In array
users.find({ role: { $in: ['admin', 'moderator'] } })
```

**Logical:**
```javascript
// AND (implicit)
users.find({ age: { $gte: 18 }, status: 'active' })

// OR
users.find({
  $or: [
    { age: { $lt: 18 } },
    { age: { $gt: 65 } }
  ]
})

// NOT
users.find({ age: { $not: { $gte: 18 } } })
```

## Using Mongoose ODM

Mongoose provides schema-based modeling:

**Define Schema:**
```javascript
const mongoose = require('mongoose');

// Connect
await mongoose.connect('mongodb://localhost:27017/myapp');

// Define schema
const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    trim: true
  },
  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true
  },
  age: {
    type: Number,
    min: 0,
    max: 120
  },
  role: {
    type: String,
    enum: ['user', 'admin', 'moderator'],
    default: 'user'
  },
  createdAt: {
    type: Date,
    default: Date.now
  },
  posts: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Post'
  }]
});

// Add methods
userSchema.methods.getFullInfo = function() {
  return `${this.name} (${this.email})`;
};

// Create model
const User = mongoose.model('User', userSchema);
```

**CRUD with Mongoose:**
```javascript
// Create
const user = new User({
  name: 'Alice Johnson',
  email: 'alice@example.com',
  age: 28
});
await user.save();

// Or use create
const newUser = await User.create({
  name: 'Bob Smith',
  email: 'bob@example.com',
  age: 32
});

// Read
const user = await User.findById(userId);
const users = await User.find({ age: { $gte: 18 } });
const oneUser = await User.findOne({ email: 'alice@example.com' });

// Update
await User.findByIdAndUpdate(
  userId,
  { age: 29 },
  { new: true } // Return updated document
);

// Delete
await User.findByIdAndDelete(userId);
await User.deleteMany({ status: 'inactive' });
```

## Relationships and Population

**Reference documents:**
```javascript
const postSchema = new mongoose.Schema({
  title: String,
  content: String,
  author: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true
  }
});

const Post = mongoose.model('Post', postSchema);

// Create post with reference
const post = await Post.create({
  title: 'My First Post',
  content: 'Hello World!',
  author: userId
});

// Populate reference
const postWithAuthor = await Post
  .findById(postId)
  .populate('author');

console.log(postWithAuthor.author.name); // Access populated data

// Populate specific fields
const post = await Post
  .findById(postId)
  .populate('author', 'name email');
```

## Aggregation Pipeline

Powerful data processing:

```javascript
// Group and count
const stats = await User.aggregate([
  {
    $group: {
      _id: '$role',
      count: { $sum: 1 },
      avgAge: { $avg: '$age' }
    }
  }
]);

// Match, group, sort
const results = await Post.aggregate([
  { $match: { published: true } },
  {
    $group: {
      _id: '$author',
      postCount: { $sum: 1 },
      titles: { $push: '$title' }
    }
  },
  { $sort: { postCount: -1 } },
  { $limit: 10 }
]);

// Lookup (join)
const usersWithPosts = await User.aggregate([
  {
    $lookup: {
      from: 'posts',
      localField: '_id',
      foreignField: 'author',
      as: 'posts'
    }
  },
  { $match: { 'posts.0': { $exists: true } } }
]);
```

## Indexing for Performance

```javascript
// Create index
userSchema.index({ email: 1 }); // Ascending
userSchema.index({ age: -1 }); // Descending
userSchema.index({ name: 1, email: 1 }); // Compound

// Text search index
postSchema.index({ title: 'text', content: 'text' });

// Use text search
const results = await Post.find({
  $text: { $search: 'mongodb tutorial' }
});

// Unique index (in schema)
email: {
  type: String,
  unique: true
}
```

## Validation

```javascript
const userSchema = new mongoose.Schema({
  email: {
    type: String,
    required: [true, 'Email is required'],
    validate: {
      validator: function(v) {
        return /^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$/.test(v);
      },
      message: 'Invalid email format'
    }
  },
  age: {
    type: Number,
    min: [0, 'Age cannot be negative'],
    max: [120, 'Age must be realistic']
  }
});
```

## Best Practices

1. **Use indexes** on frequently queried fields
2. **Avoid SELECT ***, project only needed fields
3. **Use lean()** for read-only queries (faster)
4. **Implement pagination** for large datasets
5. **Handle connections properly** (connection pooling)
6. **Use transactions** for multi-document operations
7. **Monitor queries** with explain()

## Common Patterns

**Pagination:**
```javascript
const page = 1;
const limit = 20;

const users = await User
  .find()
  .skip((page - 1) * limit)
  .limit(limit);
```

**Soft delete:**
```javascript
userSchema.add({ deletedAt: Date });

// Mark as deleted
await User.findByIdAndUpdate(userId, { deletedAt: new Date() });

// Query only active
const activeUsers = await User.find({ deletedAt: null });
```

MongoDB and Node.js form a powerful combination for modern applications. Start with basic CRUD operations, understand indexing, and gradually explore advanced features like aggregation and transactions.
