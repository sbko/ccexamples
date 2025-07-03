# Universal Commands

## Purpose
Defines a standardized set of commands that work consistently across all project types, providing a unified interface for common development tasks.

## Standard Command Interface

### Core Commands
Every project should support these universal commands:

- **setup**: Initialize development environment and install dependencies
- **quality**: Run all quality checks (lint + format + typecheck)
- **test**: Run test suite with appropriate coverage
- **security**: Run security scans and vulnerability checks
- **clean**: Clean build artifacts and temporary files
- **build**: Build production-ready artifacts
- **dev**: Start development server with hot reload
- **deploy**: Deploy to target environment

### Quality Command Examples
```bash
# JavaScript/TypeScript
npm run lint && npm run format && npm run typecheck

# Go
golangci-lint run && gofmt -l . && go vet ./...

# Python
ruff check && black --check . && mypy .

# Rust
cargo clippy && cargo fmt --check

# C#
dotnet format --verify-no-changes && dotnet build
```

### Test Command Examples
```bash
# JavaScript/TypeScript
npm test -- --coverage --watchAll=false

# Go
go test -race -cover ./...

# Python
pytest --cov=. --cov-report=term-missing

# Rust
cargo test

# C#
dotnet test --collect:"XPlat Code Coverage"
```

## Environment-Aware Execution

For environment-specific execution patterns, see the Environment Management module which provides comprehensive guidance on:
- Flox environment activation
- Docker environment handling
- Direct execution fallbacks
- Environment detection and setup

## Command Implementation Patterns

### Makefile Implementation
```makefile
# Universal commands via Makefile
.PHONY: setup quality test security clean build dev deploy

setup:
	@echo "Setting up development environment..."
	# Project-specific setup commands

quality:
	@echo "Running quality checks..."
	# Language-specific quality tools

test:
	@echo "Running tests..."
	# Language-specific test commands

security:
	@echo "Running security checks..."
	# Security scanning tools

clean:
	@echo "Cleaning build artifacts..."
	# Remove temporary files

build:
	@echo "Building project..."
	# Build commands

dev:
	@echo "Starting development server..."
	# Development server

deploy:
	@echo "Deploying..."
	# Deployment commands
```

### Package.json Implementation
```json
{
  "scripts": {
    "setup": "npm install && npm run db:setup",
    "quality": "npm run lint && npm run format && npm run typecheck",
    "test": "jest --coverage",
    "security": "npm audit && npm run security:scan",
    "clean": "rm -rf dist/ coverage/ node_modules/.cache/",
    "build": "npm run clean && npm run build:prod",
    "dev": "npm run dev:server",
    "deploy": "npm run build && npm run deploy:prod"
  }
}
```

### Task Runner Implementation
```yaml
# Taskfile.yml
version: '3'

tasks:
  setup:
    desc: Initialize development environment
    cmds:
      - go mod download
      - make db-setup

  quality:
    desc: Run quality checks
    cmds:
      - golangci-lint run
      - gofmt -l .
      - go vet ./...

  test:
    desc: Run tests
    cmds:
      - go test -race -cover ./...

  security:
    desc: Run security checks
    cmds:
      - gosec ./...
      - go list -json -m all | nancy sleuth

  clean:
    desc: Clean build artifacts
    cmds:
      - rm -rf dist/
      - go clean -cache

  build:
    desc: Build project
    cmds:
      - go build -ldflags="-s -w" -o dist/app ./cmd/server

  dev:
    desc: Start development server
    cmds:
      - air # Live reload

  deploy:
    desc: Deploy to production
    cmds:
      - task: build
      - ./scripts/deploy.sh
```

## Command Composition

### Sequential Execution
Commands that should run in order:
```bash
# Full quality pipeline
make setup && make quality && make test && make security
```

### Parallel Execution
Commands that can run concurrently:
```bash
# Parallel quality checks
make lint & make format & make typecheck & wait
```

### Conditional Execution
Commands with conditions:
```bash
# Only run tests if quality passes
make quality && make test
```

## Error Handling

### Command Failures
- Exit immediately on critical failures
- Continue with warnings for non-critical issues
- Provide clear error messages and suggestions
- Log failures for debugging

### Missing Commands
- Gracefully handle missing command implementations
- Provide fallback commands where possible
- Suggest installation of missing tools
- Document command requirements

## Integration with Other Modules

### Environment Management
- Ensure commands run in proper environment
- Activate environments before command execution
- Handle environment-specific command variations

### Project Detection
- Adapt commands based on detected project type
- Use appropriate tools for each language
- Respect project-specific configurations

### Quality Standards
- Implement quality checks through universal interface
- Maintain consistency across different project types
- Provide unified quality reporting

## Command Discovery

### Help System
```bash
# Standard help commands
make help           # List available commands
make help-quality   # Detailed help for quality commands
make help-test      # Detailed help for test commands
```

### Self-Documentation
Commands should be self-documenting:
```bash
# Command descriptions
make quality  # "Run linting, formatting, and type checking"
make test     # "Run full test suite with coverage reporting"
make security # "Run security scans and vulnerability checks"
```

## Performance Optimization

### Command Caching
- Cache command results when appropriate
- Skip redundant operations
- Use incremental builds and tests

### Resource Management
- Limit parallel execution based on system resources
- Monitor memory and CPU usage
- Clean up resources after command completion

### Build Optimization
- Use build caches effectively
- Optimize Docker layer caching
- Leverage incremental compilation where available