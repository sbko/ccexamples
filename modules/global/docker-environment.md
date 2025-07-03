# Docker Environment Management

## Purpose
Provides comprehensive Docker-based development environment management, including Docker Compose, development containers, and containerized workflow automation for consistent, reproducible development environments.

## Docker Development Strategy

### Primary Tools
- **Docker**: Containerized development environments
- **Docker Compose**: Multi-service development orchestration
- **Dev Containers**: VS Code integrated development containers
- **Dockerized tooling**: Language-specific development containers

### Environment Detection and Setup
1. **Check for Docker files**: Look for `Dockerfile`, `docker-compose.yml`, `.devcontainer/`
2. **Validate Docker availability**: Ensure Docker daemon is running
3. **Container orchestration**: Use Docker Compose for multi-service setups
4. **Development container integration**: Leverage dev containers for IDE integration

## Docker Compose Development

### Standard docker-compose.yml Structure
```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile.dev
    volumes:
      - .:/app
      - node_modules:/app/node_modules
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
    depends_on:
      - db
      - redis
    
  db:
    image: postgres:15
    environment:
      - POSTGRES_DB=myapp_development
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./scripts/init-db.sql:/docker-entrypoint-initdb.d/init.sql
    ports:
      - "5432:5432"
    
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
  node_modules:
```

### Development Dockerfile
```dockerfile
# Dockerfile.dev
FROM node:18-alpine

WORKDIR /app

# Install system dependencies
RUN apk add --no-cache git curl

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy source code
COPY . .

# Expose development port
EXPOSE 3000

# Development command with hot reload
CMD ["npm", "run", "dev"]
```

## Development Container Configuration

### .devcontainer/devcontainer.json
```json
{
  "name": "Development Container",
  "dockerComposeFile": "../docker-compose.yml",
  "service": "app",
  "workspaceFolder": "/app",
  "shutdownAction": "stopCompose",
  
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-vscode.vscode-typescript-next",
        "esbenp.prettier-vscode",
        "ms-python.python",
        "golang.go"
      ],
      "settings": {
        "terminal.integrated.defaultProfile.linux": "bash"
      }
    }
  },
  
  "forwardPorts": [3000, 5432, 6379],
  "postCreateCommand": "npm install",
  "postStartCommand": "npm run dev",
  
  "remoteUser": "node"
}
```

## Language-Specific Docker Environments

### Node.js Development
```dockerfile
FROM node:18-alpine

# Install system dependencies
RUN apk add --no-cache \
    git \
    curl \
    python3 \
    make \
    g++

WORKDIR /app

# Install global development tools
RUN npm install -g \
    nodemon \
    typescript \
    @types/node \
    eslint \
    prettier

# Copy package files
COPY package*.json ./
RUN npm ci

# Development setup
COPY . .
EXPOSE 3000
CMD ["npm", "run", "dev"]
```

### Python Development
```dockerfile
FROM python:3.11-alpine

# Install system dependencies
RUN apk add --no-cache \
    git \
    curl \
    gcc \
    musl-dev \
    postgresql-dev

WORKDIR /app

# Install Python development tools
RUN pip install --no-cache-dir \
    poetry \
    black \
    ruff \
    mypy \
    pytest \
    pytest-cov

# Copy dependency files
COPY pyproject.toml poetry.lock ./
RUN poetry config virtualenvs.create false \
    && poetry install --no-dev

# Development setup
COPY . .
EXPOSE 8000
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

### Go Development
```dockerfile
FROM golang:1.21-alpine

# Install system dependencies
RUN apk add --no-cache \
    git \
    curl \
    make \
    gcc \
    musl-dev

WORKDIR /app

# Install Go development tools
RUN go install golang.org/x/tools/cmd/goimports@latest && \
    go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest && \
    go install github.com/air-verse/air@latest

# Copy Go modules
COPY go.mod go.sum ./
RUN go mod download

# Development setup
COPY . .
EXPOSE 8080
CMD ["air"]
```

## Docker Development Commands

### Standard Docker Workflow
```bash
# Environment setup
docker-compose up -d          # Start all services
docker-compose logs -f app    # Follow application logs
docker-compose exec app bash  # Access container shell

# Development commands
docker-compose exec app npm install           # Install dependencies
docker-compose exec app npm test             # Run tests
docker-compose exec app npm run lint         # Run linting
docker-compose exec app npm run build        # Build application

# Database operations
docker-compose exec db psql -U postgres myapp_development  # Database access
docker-compose exec app npm run db:migrate               # Run migrations
docker-compose exec app npm run db:seed                  # Seed database

# Clean up
docker-compose down           # Stop services
docker-compose down -v        # Stop and remove volumes
docker-compose build --no-cache  # Rebuild images
```

### Development Shortcuts
```bash
# Quick commands
alias dcu="docker-compose up -d"
alias dcd="docker-compose down"
alias dcr="docker-compose restart"
alias dcl="docker-compose logs -f"
alias dce="docker-compose exec"

# Common operations
alias app-shell="docker-compose exec app bash"
alias app-test="docker-compose exec app npm test"
alias app-lint="docker-compose exec app npm run lint"
alias db-shell="docker-compose exec db psql -U postgres"
```

## Multi-Stage Docker Builds

### Production-Ready Dockerfile
```dockerfile
# Multi-stage build for Node.js
FROM node:18-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

FROM node:18-alpine AS production

# Create non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

WORKDIR /app

# Copy built application
COPY --from=builder --chown=nextjs:nodejs /app/build ./build
COPY --from=builder --chown=nextjs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nextjs:nodejs /app/package.json ./package.json

USER nextjs
EXPOSE 3000
CMD ["npm", "start"]
```

## Docker Networking and Volumes

### Advanced docker-compose.yml
```yaml
version: '3.8'

services:
  app:
    build: .
    networks:
      - frontend
      - backend
    volumes:
      - app_data:/app/data
      - ./src:/app/src:cached
    environment:
      - DATABASE_URL=postgresql://postgres:postgres@db:5432/myapp
    depends_on:
      db:
        condition: service_healthy
    
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    networks:
      - frontend
    depends_on:
      - app
    
  db:
    image: postgres:15
    networks:
      - backend
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 30s
      timeout: 10s
      retries: 3

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge

volumes:
  app_data:
  postgres_data:
```

## Docker Security Best Practices

### Secure Dockerfile Patterns
```dockerfile
FROM node:18-alpine

# Create non-root user
RUN addgroup -g 1001 -S appgroup && \
    adduser -S appuser -u 1001 -G appgroup

# Install dependencies as root
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production && \
    npm cache clean --force

# Copy application files
COPY --chown=appuser:appgroup . .

# Switch to non-root user
USER appuser

# Use specific port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

CMD ["npm", "start"]
```

### Environment Security
```yaml
# docker-compose.yml security practices
services:
  app:
    build: .
    security_opt:
      - no-new-privileges:true
    cap_drop:
      - ALL
    cap_add:
      - NET_BIND_SERVICE
    read_only: true
    tmpfs:
      - /tmp
    environment:
      - NODE_ENV=production
    secrets:
      - db_password
      - api_key

secrets:
  db_password:
    file: ./secrets/db_password.txt
  api_key:
    file: ./secrets/api_key.txt
```

## Docker Development Optimization

### Performance Optimizations
```dockerfile
# Optimized Node.js Dockerfile
FROM node:18-alpine

# Install dependencies for native modules
RUN apk add --no-cache --virtual .build-deps \
    python3 \
    make \
    g++ \
    && apk add --no-cache git

WORKDIR /app

# Copy package files first (better layer caching)
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production \
    && npm cache clean --force \
    && apk del .build-deps

# Copy source code
COPY . .

# Build application
RUN npm run build

EXPOSE 3000
CMD ["npm", "start"]
```

### Development Volume Strategies
```yaml
# Optimized development volumes
services:
  app:
    build: .
    volumes:
      # Source code with delegated consistency
      - .:/app:delegated
      # Named volume for node_modules (faster)
      - node_modules:/app/node_modules
      # Cache directories
      - npm_cache:/root/.npm
      - build_cache:/app/.next
    environment:
      - CHOKIDAR_USEPOLLING=true  # For file watching
```

## Error Handling and Debugging

### Container Health Monitoring
```bash
# Health check commands
docker-compose ps                    # Service status
docker-compose logs app              # Application logs
docker-compose exec app ps aux       # Running processes
docker stats                         # Resource usage

# Debugging containers
docker-compose exec app sh           # Shell access
docker-compose run --rm app bash     # One-off container
docker-compose up --abort-on-container-exit  # Exit on failure
```

### Common Issues and Solutions
```bash
# Permission issues
docker-compose exec app chown -R $(id -u):$(id -g) /app

# Port conflicts
docker-compose down && docker-compose up -d

# Volume issues
docker-compose down -v && docker-compose up -d

# Network issues
docker network ls
docker network prune
```

## Integration with CI/CD

### Docker Build Optimization
```dockerfile
# Build args for CI/CD
ARG BUILD_ENV=production
ARG VERSION=latest

FROM node:18-alpine
ENV NODE_ENV=${BUILD_ENV}
LABEL version=${VERSION}

# Multi-platform builds
FROM --platform=$BUILDPLATFORM node:18-alpine AS base
```

### GitHub Actions Integration
```yaml
# .github/workflows/docker.yml
name: Docker Build
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Build Docker image
        run: docker-compose build
        
      - name: Run tests
        run: docker-compose run --rm app npm test
        
      - name: Security scan
        run: docker-compose run --rm app npm audit
```

## Integration with Other Modules

### Environment Management Coordination
- Works alongside Flox environments when Docker is not available
- Provides containerized alternative to local tool installation
- Supports hybrid environments with both Docker and native tools

### Code Quality Integration
- Containers include linting and formatting tools
- Standardized tool versions across development environments
- Automated quality checks in containerized CI/CD

### Universal Commands Compatibility
- All standard commands work within Docker containers
- Environment-aware execution automatically detects Docker setup
- Consistent command interface regardless of containerization

### Project Detection Enhancement
- Detects Docker-based projects through file presence
- Automatically configures appropriate container strategies
- Supports multi-language projects in single containers