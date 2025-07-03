# Go Project Template

## Purpose
Provides comprehensive standards and best practices for Go projects, including project structure, coding conventions, testing strategies, and development workflows.

## Project Structure

### Standard Go Project Layout
```
project/
├── cmd/                    # Application entry points
│   └── server/
│       └── main.go
├── internal/              # Private application code
│   ├── api/              # HTTP handlers and routing
│   │   ├── handlers/
│   │   ├── middleware/
│   │   └── routes/
│   ├── business/         # Business logic
│   │   ├── services/
│   │   └── models/
│   ├── data/             # Database layer
│   │   ├── repositories/
│   │   └── migrations/
│   └── config/           # Configuration management
├── pkg/                   # Public library code
│   ├── errors/
│   ├── logger/
│   └── utils/
├── api/                   # API definitions
│   ├── openapi/
│   └── proto/
├── web/                   # Web assets (if applicable)
│   ├── static/
│   └── templates/
├── scripts/               # Build and deployment scripts
├── deployments/           # Deployment configurations
│   ├── docker/
│   └── k8s/
├── test/                  # Integration and e2e tests
├── docs/                  # Project documentation
├── go.mod
├── go.sum
├── Makefile
├── Dockerfile
└── README.md
```

### Directory Guidelines
- **cmd/**: Main applications for the project
- **internal/**: Private application and library code
- **pkg/**: Library code that's safe to use by external applications
- **api/**: Protocol definition files (OpenAPI/Swagger, Protocol Buffers)
- **web/**: Web application specific components
- **scripts/**: Scripts for build, install, analysis, etc.
- **test/**: Additional external test apps and test data

## Go Coding Standards

### Naming Conventions
- **Packages**: Short, lowercase, no underscores or mixed caps
- **Functions**: MixedCaps for exported, mixedCaps for unexported
- **Variables**: Short names for short scopes, descriptive names for longer scopes
- **Constants**: MixedCaps, use const blocks for related constants
- **Interfaces**: Single-method interfaces should be named with the method name + "er"

### Code Organization
```go
// Package declaration
package main

// Import block - standard library first, then third-party, then local
import (
    "context"
    "fmt"
    "log"
    
    "github.com/gin-gonic/gin"
    "github.com/golang-migrate/migrate/v4"
    
    "github.com/yourorg/yourproject/internal/api"
    "github.com/yourorg/yourproject/internal/config"
)

// Constants
const (
    DefaultPort = 8080
    MaxRetries  = 3
)

// Types
type Server struct {
    config *config.Config
    router *gin.Engine
}

// Functions
func NewServer(cfg *config.Config) *Server {
    return &Server{
        config: cfg,
        router: gin.Default(),
    }
}
```

### Error Handling
```go
// Good error handling
func ProcessOrder(ctx context.Context, order *Order) error {
    if err := validateOrder(order); err != nil {
        return fmt.Errorf("validation failed: %w", err)
    }
    
    if err := saveOrder(ctx, order); err != nil {
        return fmt.Errorf("failed to save order: %w", err)
    }
    
    return nil
}

// Custom error types
type ValidationError struct {
    Field   string
    Message string
}

func (e *ValidationError) Error() string {
    return fmt.Sprintf("validation error on field %s: %s", e.Field, e.Message)
}
```

## Testing Standards

### Test Structure
```go
func TestProcessOrder(t *testing.T) {
    tests := []struct {
        name    string
        order   *Order
        wantErr bool
    }{
        {
            name: "valid order",
            order: &Order{
                ID:         "123",
                CustomerID: "cust-456",
                Items:      []Item{{ProductID: "prod-789", Quantity: 2}},
            },
            wantErr: false,
        },
        {
            name: "invalid order - missing customer ID",
            order: &Order{
                ID:    "123",
                Items: []Item{{ProductID: "prod-789", Quantity: 2}},
            },
            wantErr: true,
        },
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            err := ProcessOrder(context.Background(), tt.order)
            if (err != nil) != tt.wantErr {
                t.Errorf("ProcessOrder() error = %v, wantErr %v", err, tt.wantErr)
            }
        })
    }
}
```

### Testing Categories
- **Unit tests**: Test individual functions and methods (70%)
- **Integration tests**: Test component interactions (25%)
- **End-to-end tests**: Test complete workflows (5%)

### Test Coverage
```bash
# Run tests with coverage
go test -cover ./...

# Generate coverage report
go test -coverprofile=coverage.out ./...
go tool cover -html=coverage.out -o coverage.html

# Coverage requirements
# - Minimum 80% coverage for new code
# - Minimum 70% coverage for existing code
# - 100% coverage for critical business logic
```

## Development Workflow

### Environment Setup
```bash
# Install Go (use flox when available)
flox install go make git

# Initialize module
go mod init github.com/yourorg/yourproject

# Install development tools
go install golang.org/x/tools/cmd/goimports@latest
go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest
go install github.com/air-verse/air@latest
```

### Standard Commands
```makefile
# Makefile
.PHONY: setup build test lint fmt clean dev

setup:
	go mod download
	go mod verify

build:
	go build -o bin/server ./cmd/server

test:
	go test -race -cover ./...

lint:
	golangci-lint run

fmt:
	gofmt -s -w .
	goimports -w .

clean:
	rm -rf bin/
	go clean -cache

dev:
	air # Live reload for development
```

## Configuration Management

### Configuration Structure
```go
type Config struct {
    Server   ServerConfig   `json:"server"`
    Database DatabaseConfig `json:"database"`
    Redis    RedisConfig    `json:"redis"`
    Logging  LoggingConfig  `json:"logging"`
}

type ServerConfig struct {
    Port         int           `json:"port" env:"SERVER_PORT" env-default:"8080"`
    ReadTimeout  time.Duration `json:"read_timeout" env:"SERVER_READ_TIMEOUT" env-default:"30s"`
    WriteTimeout time.Duration `json:"write_timeout" env:"SERVER_WRITE_TIMEOUT" env-default:"30s"`
}

type DatabaseConfig struct {
    Host     string `json:"host" env:"DB_HOST" env-default:"localhost"`
    Port     int    `json:"port" env:"DB_PORT" env-default:"5432"`
    User     string `json:"user" env:"DB_USER" env-default:"postgres"`
    Password string `json:"password" env:"DB_PASSWORD" env-default:""`
    Name     string `json:"name" env:"DB_NAME" env-default:"myapp"`
}
```

### Environment Variables
```bash
# .env file
SERVER_PORT=8080
SERVER_READ_TIMEOUT=30s
SERVER_WRITE_TIMEOUT=30s

DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=secret
DB_NAME=myapp

REDIS_HOST=localhost
REDIS_PORT=6379

LOG_LEVEL=info
LOG_FORMAT=json
```

## Database Integration

### Database Migrations
```go
// migrations/000001_create_users_table.up.sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

// migrations/000001_create_users_table.down.sql
DROP TABLE users;
```

### Repository Pattern
```go
type User struct {
    ID        uuid.UUID `json:"id" db:"id"`
    Email     string    `json:"email" db:"email"`
    CreatedAt time.Time `json:"created_at" db:"created_at"`
    UpdatedAt time.Time `json:"updated_at" db:"updated_at"`
}

type UserRepository interface {
    Create(ctx context.Context, user *User) error
    GetByID(ctx context.Context, id uuid.UUID) (*User, error)
    GetByEmail(ctx context.Context, email string) (*User, error)
    Update(ctx context.Context, user *User) error
    Delete(ctx context.Context, id uuid.UUID) error
}

type postgresUserRepository struct {
    db *sql.DB
}

func NewPostgresUserRepository(db *sql.DB) UserRepository {
    return &postgresUserRepository{db: db}
}

func (r *postgresUserRepository) Create(ctx context.Context, user *User) error {
    query := `
        INSERT INTO users (email) 
        VALUES ($1) 
        RETURNING id, created_at, updated_at`
    
    err := r.db.QueryRowContext(ctx, query, user.Email).Scan(
        &user.ID, &user.CreatedAt, &user.UpdatedAt)
    if err != nil {
        return fmt.Errorf("failed to create user: %w", err)
    }
    
    return nil
}
```

## API Development

### HTTP Handler Structure
```go
type Handler struct {
    userService UserService
    logger      *slog.Logger
}

func NewHandler(userService UserService, logger *slog.Logger) *Handler {
    return &Handler{
        userService: userService,
        logger:      logger,
    }
}

func (h *Handler) CreateUser(c *gin.Context) {
    var req CreateUserRequest
    if err := c.ShouldBindJSON(&req); err != nil {
        h.logger.Error("failed to bind request", "error", err)
        c.JSON(http.StatusBadRequest, gin.H{"error": "invalid request"})
        return
    }
    
    user, err := h.userService.Create(c.Request.Context(), &req)
    if err != nil {
        h.logger.Error("failed to create user", "error", err)
        c.JSON(http.StatusInternalServerError, gin.H{"error": "internal server error"})
        return
    }
    
    c.JSON(http.StatusCreated, user)
}
```

### Request/Response Models
```go
type CreateUserRequest struct {
    Email string `json:"email" binding:"required,email"`
}

type UserResponse struct {
    ID        string    `json:"id"`
    Email     string    `json:"email"`
    CreatedAt time.Time `json:"created_at"`
}

func (u *User) ToResponse() *UserResponse {
    return &UserResponse{
        ID:        u.ID.String(),
        Email:     u.Email,
        CreatedAt: u.CreatedAt,
    }
}
```

## Logging and Monitoring

### Structured Logging
```go
import "log/slog"

func main() {
    logger := slog.New(slog.NewJSONHandler(os.Stdout, &slog.HandlerOptions{
        Level: slog.LevelInfo,
    }))
    
    logger.Info("starting server",
        slog.String("version", version),
        slog.Int("port", port),
    )
}

// Use context with logger
func ProcessOrder(ctx context.Context, order *Order) error {
    logger := slog.With(
        slog.String("order_id", order.ID),
        slog.String("customer_id", order.CustomerID),
    )
    
    logger.Info("processing order")
    
    // ... processing logic
    
    logger.Info("order processed successfully")
    return nil
}
```

### Metrics Collection
```go
import (
    "github.com/prometheus/client_golang/prometheus"
    "github.com/prometheus/client_golang/prometheus/promauto"
)

var (
    ordersProcessed = promauto.NewCounterVec(
        prometheus.CounterOpts{
            Name: "orders_processed_total",
            Help: "The total number of processed orders",
        },
        []string{"status"},
    )
    
    orderProcessingDuration = promauto.NewHistogramVec(
        prometheus.HistogramOpts{
            Name:    "order_processing_duration_seconds",
            Help:    "Time spent processing orders",
            Buckets: prometheus.DefBuckets,
        },
        []string{"status"},
    )
)

func ProcessOrder(ctx context.Context, order *Order) error {
    start := time.Now()
    defer func() {
        duration := time.Since(start).Seconds()
        orderProcessingDuration.WithLabelValues("success").Observe(duration)
    }()
    
    // ... processing logic
    
    ordersProcessed.WithLabelValues("success").Inc()
    return nil
}
```

## Security Best Practices

### Input Validation
```go
import "github.com/go-playground/validator/v10"

var validate *validator.Validate

func init() {
    validate = validator.New()
}

type CreateUserRequest struct {
    Email    string `json:"email" validate:"required,email"`
    Password string `json:"password" validate:"required,min=8"`
}

func (r *CreateUserRequest) Validate() error {
    return validate.Struct(r)
}
```

### Secret Management
```go
// Don't hardcode secrets
func NewDatabase(cfg *DatabaseConfig) (*sql.DB, error) {
    dsn := fmt.Sprintf("host=%s port=%d user=%s password=%s dbname=%s sslmode=require",
        cfg.Host, cfg.Port, cfg.User, cfg.Password, cfg.Name)
    
    return sql.Open("postgres", dsn)
}

// Use environment variables or secret managers
func loadConfig() *Config {
    return &Config{
        Database: DatabaseConfig{
            Host:     getEnv("DB_HOST", "localhost"),
            Port:     getEnvInt("DB_PORT", 5432),
            User:     getEnv("DB_USER", "postgres"),
            Password: getEnv("DB_PASSWORD", ""), // Never have default passwords
            Name:     getEnv("DB_NAME", "myapp"),
        },
    }
}
```

## Performance Optimization

### Connection Pooling
```go
func NewDatabase(cfg *DatabaseConfig) (*sql.DB, error) {
    db, err := sql.Open("postgres", cfg.DSN())
    if err != nil {
        return nil, err
    }
    
    // Connection pool settings
    db.SetMaxOpenConns(25)
    db.SetMaxIdleConns(5)
    db.SetConnMaxLifetime(10 * time.Minute)
    db.SetConnMaxIdleTime(5 * time.Minute)
    
    return db, nil
}
```

### Caching Strategy
```go
import "github.com/go-redis/redis/v8"

type CacheService struct {
    redis *redis.Client
}

func (c *CacheService) GetUser(ctx context.Context, userID string) (*User, error) {
    // Try cache first
    cached, err := c.redis.Get(ctx, fmt.Sprintf("user:%s", userID)).Result()
    if err == nil {
        var user User
        if err := json.Unmarshal([]byte(cached), &user); err == nil {
            return &user, nil
        }
    }
    
    // Fallback to database
    user, err := c.userRepo.GetByID(ctx, userID)
    if err != nil {
        return nil, err
    }
    
    // Cache for future requests
    userData, _ := json.Marshal(user)
    c.redis.Set(ctx, fmt.Sprintf("user:%s", userID), userData, time.Hour)
    
    return user, nil
}
```

## Deployment

### Docker Configuration
```dockerfile
# Dockerfile
FROM golang:1.21-alpine AS builder

WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o server ./cmd/server

FROM alpine:3.18
RUN apk --no-cache add ca-certificates tzdata
WORKDIR /root/

COPY --from=builder /app/server .
COPY --from=builder /app/migrations ./migrations

EXPOSE 8080
CMD ["./server"]
```

### Kubernetes Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: go-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: go-app
  template:
    metadata:
      labels:
        app: go-app
    spec:
      containers:
      - name: go-app
        image: go-app:latest
        ports:
        - containerPort: 8080
        env:
        - name: DB_HOST
          value: "postgres-service"
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "200m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
```

## Integration with Other Modules

### Environment Management
- Use @modules/global/environment-management.md for Go tool installation
- Configure Go-specific environment variables
- Set up Go development environment with proper tooling

### Code Quality
- Apply @modules/global/code-quality.md standards to Go code
- Use Go-specific linting and formatting tools
- Implement Go-specific quality gates

### Testing Strategies
- Follow @modules/project-specific/testing-strategies.md for comprehensive testing
- Implement Go-specific testing patterns
- Use Go testing tools and frameworks

### Security Practices
- Apply @modules/global/security-practices.md to Go applications
- Implement Go-specific security patterns
- Use Go security scanning tools