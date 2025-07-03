# Project-Specific Claude Code Configuration

This is an example of a project-specific `./CLAUDE.md` file that builds upon global standards with project-specific requirements.

## Project Information

**Project Type**: Go REST API with PostgreSQL database  
**Tech Stack**: Go 1.21, PostgreSQL 15, Docker, Kubernetes  
**Team Size**: 5 developers  
**Deployment**: Cloud-native microservice  

## Import Global Standards

@~/.claude/CLAUDE.md

## Project-Specific Modules

@modules/project-specific/testing-strategies.md
@modules/project-specific/debugging-protocols.md

## Go-Specific Development Standards

### Code Organization
```
project/
├── cmd/                 # Application entry points
│   └── server/
├── internal/           # Private application code
│   ├── api/           # HTTP handlers and routing
│   ├── business/      # Business logic
│   ├── data/          # Database layer
│   └── config/        # Configuration management
├── pkg/               # Public library code
├── migrations/        # Database migrations
├── docs/             # Project documentation
└── deployments/      # Deployment configurations
```

### Go Quality Standards
- **gofmt**: All code must be formatted with gofmt
- **golangci-lint**: Use comprehensive linting with custom configuration
- **go vet**: Static analysis must pass without warnings
- **go mod tidy**: Keep dependencies clean and minimal
- **Test coverage**: Minimum 85% coverage for business logic

### Database Standards
- **Migration strategy**: All schema changes via versioned migrations
- **Query optimization**: Use EXPLAIN ANALYZE for complex queries
- **Index strategy**: Proper indexing for all frequent query patterns
- **Connection pooling**: Optimize connection pool settings
- **Transaction management**: Proper transaction boundaries and rollback handling

## Project-Specific Commands

### Development Workflow
```bash
# Environment setup
make setup              # Install dependencies and set up development environment
make db-start           # Start local PostgreSQL in Docker
make db-migrate         # Run database migrations
make db-seed           # Seed database with test data

# Development commands
make dev               # Start development server with hot reload
make test              # Run full test suite with coverage
make test-unit         # Run unit tests only
make test-integration  # Run integration tests only
make lint              # Run golangci-lint with project configuration
make fmt               # Format code with gofmt and goimports

# Build and deployment
make build             # Build production binary
make docker-build      # Build Docker image
make docker-run        # Run application in Docker container
make deploy-staging    # Deploy to staging environment
make deploy-prod       # Deploy to production environment
```

### Database Commands
```bash
# Database management
make db-reset          # Reset database and re-run migrations
make db-backup         # Create database backup
make db-restore        # Restore from backup
make db-console        # Connect to database console
make migration-create  # Create new migration file
```

## API Standards

### REST API Guidelines
- **Versioning**: Use URL versioning (e.g., `/api/v1/`)
- **Status codes**: Use appropriate HTTP status codes
- **Error handling**: Consistent error response format
- **Documentation**: OpenAPI/Swagger documentation
- **Rate limiting**: Implement rate limiting for public endpoints

### Request/Response Format
```json
// Success response
{
  "status": "success",
  "data": { ... },
  "meta": {
    "timestamp": "2024-01-01T00:00:00Z",
    "version": "v1"
  }
}

// Error response
{
  "status": "error",
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input parameters",
    "details": { ... }
  },
  "meta": {
    "timestamp": "2024-01-01T00:00:00Z",
    "request_id": "req-123"
  }
}
```

## Testing Strategy

### Test Categories
- **Unit tests**: Test individual functions and methods (70%)
- **Integration tests**: Test database interactions and external APIs (25%)
- **End-to-end tests**: Test complete user workflows (5%)

### Test Data Management
- **Test databases**: Separate test database per developer
- **Test fixtures**: Reusable test data builders
- **Database cleanup**: Transactions that rollback after each test
- **Mock external services**: Use testcontainers for integration tests

### Performance Testing
- **Load testing**: Regular load testing with realistic scenarios
- **Database performance**: Query performance benchmarks
- **Memory profiling**: Regular memory usage analysis
- **API benchmarks**: Response time and throughput metrics

## Security Requirements

### Authentication & Authorization
- **JWT tokens**: Stateless authentication with proper expiration
- **Role-based access**: Granular permissions for different user types
- **API keys**: Secure API key management for service-to-service communication
- **Input validation**: Comprehensive input validation and sanitization

### Database Security
- **Parameterized queries**: No string concatenation for SQL queries
- **Connection encryption**: TLS for all database connections
- **Credential management**: Database credentials via environment variables
- **Audit logging**: Log all data modification operations

## Monitoring and Observability

### Logging Standards
- **Structured logging**: JSON format with consistent field names
- **Log levels**: DEBUG, INFO, WARN, ERROR with appropriate usage
- **Context propagation**: Request IDs and user context in all logs
- **Sensitive data**: Never log passwords, tokens, or personal information

### Metrics and Monitoring
- **Application metrics**: Response times, error rates, throughput
- **Business metrics**: User actions, feature usage, conversion rates
- **Infrastructure metrics**: CPU, memory, disk, network usage
- **Database metrics**: Query performance, connection pool usage

### Health Checks
- **Liveness probe**: Basic application health check
- **Readiness probe**: Database connectivity and dependency health
- **Health endpoint**: Detailed health status for monitoring systems

## Deployment Standards

### Docker Configuration
- **Multi-stage builds**: Optimize image size with multi-stage builds
- **Security scanning**: Scan images for vulnerabilities
- **Non-root user**: Run containers as non-root user
- **Resource limits**: Set appropriate CPU and memory limits

### Kubernetes Deployment
- **Resource requests/limits**: Proper resource allocation
- **Health checks**: Liveness and readiness probes configured
- **ConfigMaps/Secrets**: Externalize configuration and secrets
- **Service mesh**: Use Istio for service-to-service communication

## Project-Specific Overrides

### Custom Quality Rules
- **Database transaction timeout**: Maximum 30 seconds
- **API response time**: 95th percentile under 100ms
- **Memory usage**: Heap size should not exceed 512MB
- **Goroutine limits**: Monitor goroutine leaks

### Team Conventions
- **Code reviews**: Minimum 2 approvals for database migration changes
- **Feature flags**: Use feature flags for gradual rollouts
- **Documentation**: Update API docs with every endpoint change
- **Performance testing**: Required for all database schema changes

---

This project configuration builds upon the global standards while providing specific guidance for Go API development with PostgreSQL.