---
title: 'How to Setup a New Astro Development Environment'
description: 'A complete guide to getting started with Astro, from installation to your first blog post'
pubDate: 'Dec 14 2025'
heroImage: '../../assets/astro-dev-setup-hero.jpg'
---

Setting up an Astro development environment is straightforward and can be done in just a few minutes. This guide will walk you through everything you need to know to get started with Astro, a modern static site generator perfect for content-driven websites.

## Prerequisites

Before you begin, make sure you have the following installed on your system:

- **Node.js** (version 18 or higher recommended)
- **npm** (comes with Node.js) or your preferred package manager (pnpm, yarn)

You can verify your installations by running:

```bash
node --version
npm --version
```

## Creating Your First Astro Project

The easiest way to create a new Astro project is using the official create-astro CLI tool:

```bash
npm create astro@latest
```

This interactive CLI will guide you through several options:

1. **Project name**: Choose a directory name for your project
2. **Template**: Select from starter templates (blog, portfolio, minimal, etc.)
3. **Dependencies**: Choose whether to install dependencies automatically
4. **TypeScript**: Configure TypeScript strictness level
5. **Git**: Initialize a git repository

For a blog with markdown support, choose the **blog** template. It comes pre-configured with:

- Content collections for blog posts
- Markdown and MDX support
- RSS feed generation
- Responsive design
- Example blog posts to get you started

## Project Structure

After creation, your Astro project will have this structure:

```
my-astro-site/
├── src/
│   ├── content/          # Markdown content goes here
│   ├── components/       # Reusable UI components
│   ├── layouts/          # Page layouts
│   └── pages/            # Routes (file-based routing)
├── public/               # Static assets
├── astro.config.mjs      # Configuration file
└── package.json
```

## Running Your Development Server

Navigate to your project directory and start the dev server:

```bash
cd my-astro-site
npm run dev
```

Your site will be available at `http://localhost:4321`. The dev server includes:

- Hot module replacement (HMR)
- Fast refresh for instant updates
- Detailed error messages
- TypeScript support

## Writing Your First Blog Post

Create a new markdown file in `src/content/blog/`:

```markdown
---
title: 'My First Post'
description: 'This is my very first blog post'
pubDate: 'Dec 14 2025'
---

Welcome to my blog! This is written in markdown.

## Features

- Easy to write
- Fast to build
- SEO optimized
```

The frontmatter (between the `---` marks) contains metadata about your post. The content below is standard markdown.

## Building for Production

When you're ready to deploy, build your site:

```bash
npm run build
```

This generates optimized static HTML in the `dist/` directory. You can preview the production build locally:

```bash
npm run preview
```

## Deployment Options

Astro sites can be deployed to any static hosting service:

- **Netlify**: Automatic builds from git
- **Vercel**: Zero-config deployments
- **GitHub Pages**: Free hosting for public repos
- **Cloudflare Pages**: Fast global CDN
- **AWS S3 + CloudFront**: Full control

## Next Steps

Now that your environment is set up, you can:

1. Customize your site's design in `src/styles/global.css`
2. Add new components in `src/components/`
3. Configure site metadata in `src/consts.ts`
4. Explore Astro integrations (React, Tailwind, etc.)
5. Set up your preferred deployment workflow

## Useful Commands

- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run preview` - Preview production build
- `npm run astro --help` - Show all Astro CLI commands

## Resources

- [Astro Documentation](https://docs.astro.build)
- [Astro Discord Community](https://astro.build/chat)
- [Astro Themes](https://astro.build/themes)
- [Astro Integrations](https://astro.build/integrations)

Happy building with Astro!
