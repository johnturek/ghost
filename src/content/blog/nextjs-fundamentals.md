---
title: 'Next.js 14: The Complete Guide to React Framework'
description: 'Master Next.js - the React framework for production with server-side rendering, routing, and more'
pubDate: 'Dec 13 2025'
heroImage: '../../assets/blog-placeholder-1.jpg'
---

Next.js is the most popular React framework, powering applications for companies like Netflix, TikTok, and Twitch. It provides everything you need to build production-ready React apps with excellent developer experience.

## Why Next.js?

**Plain React challenges:**
- Manual routing setup
- No built-in SSR/SSG
- Complex optimization
- No API routes
- Manual code splitting

**Next.js solutions:**
- File-based routing
- Built-in SSR, SSG, ISR
- Automatic optimization
- API routes included
- Automatic code splitting

## Installation

\`\`\`bash
npx create-next-app@latest my-app
cd my-app
npm run dev
\`\`\`

Your app runs at http://localhost:3000

## File-Based Routing

The \`app\` directory structure defines routes:

\`\`\`
app/
├── page.tsx          # / (homepage)
├── about/
│   └── page.tsx      # /about
├── blog/
│   ├── page.tsx      # /blog
│   └── [slug]/
│       └── page.tsx  # /blog/post-1
└── dashboard/
    └── page.tsx      # /dashboard
\`\`\`

**Creating pages:**

\`\`\`tsx
// app/page.tsx
export default function Home() {
  return <h1>Homepage</h1>;
}

// app/about/page.tsx
export default function About() {
  return <h1>About Us</h1>;
}

// app/blog/[slug]/page.tsx
export default function BlogPost({ params }: { params: { slug: string } }) {
  return <h1>Post: {params.slug}</h1>;
}
\`\`\`

## Layouts

Shared UI across routes:

\`\`\`tsx
// app/layout.tsx (root layout)
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <nav>Navigation here</nav>
        {children}
        <footer>Footer here</footer>
      </body>
    </html>
  );
}

// app/dashboard/layout.tsx (nested layout)
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div>
      <aside>Dashboard Sidebar</aside>
      <main>{children}</main>
    </div>
  );
}
\`\`\`

## Data Fetching

### Server Components (Default)

Fetch data directly in components:

\`\`\`tsx
// app/posts/page.tsx
async function getPosts() {
  const res = await fetch('https://api.example.com/posts');
  return res.json();
}

export default async function PostsPage() {
  const posts = await getPosts();

  return (
    <div>
      {posts.map((post) => (
        <article key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.excerpt}</p>
        </article>
      ))}
    </div>
  );
}
\`\`\`

### Static Generation (SSG)

\`\`\`tsx
// app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts').then((res) =>
    res.json()
  );

  return posts.map((post) => ({
    slug: post.slug,
  }));
}

export default async function Post({ params }) {
  const post = await fetch(\`https://api.example.com/posts/\${params.slug}\`).then(
    (res) => res.json()
  );

  return (
    <article>
      <h1>{post.title}</h1>
      <div>{post.content}</div>
    </article>
  );
}
\`\`\`

### Client Components

For interactivity:

\`\`\`tsx
'use client';

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
\`\`\`

## API Routes

Create backend endpoints:

\`\`\`tsx
// app/api/hello/route.ts
import { NextResponse } from 'next/server';

export async function GET() {
  return NextResponse.json({ message: 'Hello World' });
}

export async function POST(request: Request) {
  const body = await request.json();
  return NextResponse.json({ received: body });
}
\`\`\`

**With database:**

\`\`\`tsx
// app/api/posts/route.ts
import { NextResponse } from 'next/server';
import { db } from '@/lib/db';

export async function GET() {
  const posts = await db.post.findMany();
  return NextResponse.json(posts);
}

export async function POST(request: Request) {
  const body = await request.json();
  const post = await db.post.create({
    data: body,
  });
  return NextResponse.json(post);
}
\`\`\`

## Dynamic Routes

\`\`\`tsx
// app/blog/[slug]/page.tsx - Single dynamic segment
// Matches: /blog/hello-world

// app/shop/[...slug]/page.tsx - Catch-all
// Matches: /shop/a, /shop/a/b, /shop/a/b/c

// app/docs/[[...slug]]/page.tsx - Optional catch-all
// Matches: /docs, /docs/a, /docs/a/b
\`\`\`

## Metadata

SEO optimization:

\`\`\`tsx
// Static metadata
export const metadata = {
  title: 'My App',
  description: 'Welcome to my app',
};

// Dynamic metadata
export async function generateMetadata({ params }) {
  const post = await getPost(params.id);

  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      images: [post.image],
    },
  };
}
\`\`\`

## Image Optimization

\`\`\`tsx
import Image from 'next/image';

export default function ProfilePage() {
  return (
    <Image
      src="/profile.jpg"
      alt="Profile"
      width={500}
      height={500}
      priority // Load immediately
    />
  );
}
\`\`\`

## Navigation

\`\`\`tsx
import Link from 'next/link';
import { useRouter } from 'next/navigation';

export default function Navigation() {
  const router = useRouter();

  return (
    <nav>
      {/* Declarative */}
      <Link href="/about">About</Link>
      <Link href="/blog/hello">Blog Post</Link>

      {/* Programmatic */}
      <button onClick={() => router.push('/dashboard')}>
        Go to Dashboard
      </button>
    </nav>
  );
}
\`\`\`

## Loading States

\`\`\`tsx
// app/dashboard/loading.tsx
export default function Loading() {
  return <div>Loading dashboard...</div>;
}

// app/dashboard/page.tsx - Automatically wrapped with Suspense
\`\`\`

## Error Handling

\`\`\`tsx
// app/error.tsx
'use client';

export default function Error({
  error,
  reset,
}: {
  error: Error;
  reset: () => void;
}) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <p>{error.message}</p>
      <button onClick={reset}>Try again</button>
    </div>
  );
}
\`\`\`

## Middleware

Run code before requests:

\`\`\`tsx
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  // Check authentication
  const token = request.cookies.get('token');

  if (!token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: '/dashboard/:path*',
};
\`\`\`

## Environment Variables

\`\`\`bash
# .env.local
DATABASE_URL="postgresql://..."
NEXT_PUBLIC_API_URL="https://api.example.com"
\`\`\`

\`\`\`tsx
// Server-side only
const dbUrl = process.env.DATABASE_URL;

// Client-side accessible (prefix with NEXT_PUBLIC_)
const apiUrl = process.env.NEXT_PUBLIC_API_URL;
\`\`\`

## Deployment

### Vercel (Recommended)

\`\`\`bash
npm i -g vercel
vercel
\`\`\`

### Docker

\`\`\`dockerfile
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/package*.json ./
RUN npm ci --only=production
EXPOSE 3000
CMD ["npm", "start"]
\`\`\`

## Best Practices

1. **Use Server Components by default** - Only use Client Components when needed
2. **Fetch data where needed** - Co-locate data fetching with components
3. **Use route handlers for APIs** - Keep backend logic in API routes
4. **Optimize images** - Always use next/image
5. **Set metadata** - For SEO on every page
6. **Use TypeScript** - Better developer experience

Start building production-ready React apps with Next.js today!
