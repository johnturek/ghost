---
title: 'Docker for Developers: From Zero to Deployment'
description: 'Learn how to containerize your applications with Docker and streamline your development workflow'
pubDate: 'Dec 12 2025'
heroImage: '../../assets/blog-placeholder-1.jpg'
---

Docker has revolutionized how we develop, ship, and run applications. Instead of worrying about "it works on my machine," Docker ensures your app runs the same everywhere. Let's learn the essentials.

## What is Docker?

Docker packages your application and all its dependencies into a **container** - a lightweight, standalone executable package. Think of it as a shipping container for your code.

**Key concepts:**
- **Image**: A blueprint for your container (like a class in OOP)
- **Container**: A running instance of an image (like an object)
- **Dockerfile**: Instructions to build an image
- **Docker Hub**: Repository for sharing images

## Installation

**macOS/Windows:**
Download [Docker Desktop](https://www.docker.com/products/docker-desktop)

**Linux:**
\`\`\`bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
\`\`\`

Verify installation:
\`\`\`bash
docker --version
docker run hello-world
\`\`\`

## Your First Container

Run a container:
\`\`\`bash
# Run nginx web server
docker run -d -p 8080:80 nginx

# -d: Run in background (detached)
# -p 8080:80: Map port 8080 (host) to 80 (container)
\`\`\`

Visit http://localhost:8080 - you'll see nginx!

**Useful commands:**
\`\`\`bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Stop a container
docker stop <container-id>

# Remove a container
docker rm <container-id>

# View container logs
docker logs <container-id>

# Execute command in container
docker exec -it <container-id> bash
\`\`\`

## Creating Your First Dockerfile

Let's containerize a Node.js app:

**Dockerfile:**
\`\`\`dockerfile
# Use official Node.js image as base
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy application code
COPY . .

# Expose port
EXPOSE 3000

# Start application
CMD ["node", "server.js"]
\`\`\`

**Build and run:**
\`\`\`bash
# Build image
docker build -t my-app .

# Run container
docker run -p 3000:3000 my-app
\`\`\`

## Dockerfile Best Practices

### Layer Caching

Docker caches each layer. Order matters!

**Bad (slow):**
\`\`\`dockerfile
COPY . .
RUN npm install
\`\`\`

**Good (fast):**
\`\`\`dockerfile
# Copy only package files first
COPY package*.json ./
RUN npm install
# Then copy code
COPY . .
\`\`\`

Now, code changes don't invalidate the npm install cache!

### Multi-stage Builds

Build smaller production images:

\`\`\`dockerfile
# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm ci --only=production
CMD ["node", "dist/server.js"]
\`\`\`

### Use .dockerignore

Create `.dockerignore` to exclude files:
\`\`\`
node_modules
npm-debug.log
.git
.env
*.md
\`\`\`

## Docker Compose

Manage multi-container applications with `docker-compose.yml`:

\`\`\`yaml
version: '3.8'

services:
  # Web application
  web:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgres://db:5432/myapp
    depends_on:
      - db
    volumes:
      - ./src:/app/src  # Hot reload in dev

  # PostgreSQL database
  db:
    image: postgres:15
    environment:
      - POSTGRES_PASSWORD=secret
      - POSTGRES_DB=myapp
    volumes:
      - postgres-data:/var/lib/postgresql/data

  # Redis cache
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  postgres-data:
\`\`\`

**Commands:**
\`\`\`bash
# Start all services
docker-compose up

# Start in background
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down

# Stop and remove volumes
docker-compose down -v
\`\`\`

## Common Use Cases

### Full-Stack Development

\`\`\`yaml
# docker-compose.yml
services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    volumes:
      - ./frontend/src:/app/src

  backend:
    build: ./backend
    ports:
      - "4000:4000"
    environment:
      - DATABASE_URL=postgres://db:5432/app
    depends_on:
      - db

  db:
    image: postgres:15
    environment:
      - POSTGRES_PASSWORD=secret
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
\`\`\`

### Database Development

\`\`\`bash
# Run PostgreSQL
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_DB=myapp \
  -p 5432:5432 \
  -v postgres-data:/var/lib/postgresql/data \
  postgres:15

# Run MySQL
docker run -d \
  --name mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  -e MYSQL_DATABASE=myapp \
  -p 3306:3306 \
  -v mysql-data:/var/lib/mysql \
  mysql:8

# Run MongoDB
docker run -d \
  --name mongo \
  -p 27017:27017 \
  -v mongo-data:/data/db \
  mongo:7
\`\`\`

## Environment Variables

### In Dockerfile

\`\`\`dockerfile
ENV NODE_ENV=production
ENV PORT=3000
\`\`\`

### At Runtime

\`\`\`bash
docker run -e NODE_ENV=development -e API_KEY=secret my-app
\`\`\`

### With .env File

\`\`\`bash
# .env
NODE_ENV=development
API_KEY=secret

# docker-compose.yml
services:
  web:
    env_file: .env
\`\`\`

## Volumes and Persistence

### Named Volumes

\`\`\`bash
# Create volume
docker volume create my-data

# Use in container
docker run -v my-data:/app/data my-app

# List volumes
docker volume ls

# Remove volume
docker volume rm my-data
\`\`\`

### Bind Mounts

\`\`\`bash
# Mount host directory to container
docker run -v $(pwd)/src:/app/src my-app

# In docker-compose
services:
  web:
    volumes:
      - ./src:/app/src
\`\`\`

## Networking

### Default Bridge Network

Containers on the same network can communicate:

\`\`\`bash
# Create network
docker network create my-network

# Run containers on network
docker run --network my-network --name app1 my-app
docker run --network my-network --name app2 my-app

# app1 can reach app2 by name
curl http://app2:3000
\`\`\`

### Docker Compose Networking

Containers in the same compose file share a network automatically:

\`\`\`yaml
services:
  web:
    # Can access db by service name
    environment:
      - DB_HOST=db
  db:
    image: postgres
\`\`\`

## Production Tips

### Security

\`\`\`dockerfile
# Use non-root user
FROM node:18-alpine
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nodejs -u 1001
USER nodejs

# Use specific versions
FROM node:18.19.0-alpine3.19

# Scan for vulnerabilities
# docker scan my-app
\`\`\`

### Health Checks

\`\`\`dockerfile
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:3000/health || exit 1
\`\`\`

### Logging

\`\`\`bash
# View logs
docker logs -f <container>

# Limit log size
docker run --log-opt max-size=10m --log-opt max-file=3 my-app
\`\`\`

## Debugging

\`\`\`bash
# Execute bash in running container
docker exec -it <container> sh

# Inspect container
docker inspect <container>

# View resource usage
docker stats

# Copy files from container
docker cp <container>:/app/logs ./logs
\`\`\`

## Common Commands Cheat Sheet

\`\`\`bash
# Images
docker images                  # List images
docker build -t name .        # Build image
docker rmi image-id           # Remove image
docker pull nginx             # Download image

# Containers
docker run image              # Create and start
docker start container        # Start stopped container
docker stop container         # Stop container
docker restart container      # Restart container
docker rm container           # Remove container

# Cleanup
docker system prune          # Remove unused data
docker container prune       # Remove stopped containers
docker image prune          # Remove unused images
docker volume prune         # Remove unused volumes

# Docker Compose
docker-compose up           # Start services
docker-compose down         # Stop services
docker-compose ps           # List services
docker-compose logs -f      # View logs
docker-compose exec web sh  # Run command
\`\`\`

## Real-World Example

Full production setup:

\`\`\`dockerfile
# Dockerfile
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:18-alpine
RUN addgroup -g 1001 nodejs
RUN adduser -S nodejs -u 1001
WORKDIR /app
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --chown=nodejs:nodejs package*.json ./
RUN npm ci --only=production
USER nodejs
EXPOSE 3000
HEALTHCHECK CMD node healthcheck.js
CMD ["node", "dist/server.js"]
\`\`\`

\`\`\`yaml
# docker-compose.yml
version: '3.8'

services:
  web:
    build: .
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgres://db:5432/app
      - REDIS_URL=redis://redis:6379
    depends_on:
      - db
      - redis
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 3s
      retries: 3

  db:
    image: postgres:15-alpine
    restart: unless-stopped
    environment:
      - POSTGRES_PASSWORD_FILE=/run/secrets/db_password
      - POSTGRES_DB=app
    volumes:
      - db-data:/var/lib/postgresql/data
    secrets:
      - db_password

  redis:
    image: redis:7-alpine
    restart: unless-stopped
    volumes:
      - redis-data:/data

volumes:
  db-data:
  redis-data:

secrets:
  db_password:
    file: ./secrets/db_password.txt
\`\`\`

## Resources

- [Official Documentation](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/) - Find images
- [Play with Docker](https://labs.play-with-docker.com/) - Online playground
- [Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/)

Docker simplifies development and deployment. Start containerizing your apps today!
