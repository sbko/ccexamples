# Project Detection and Adaptation

## Purpose
Automatically detects project types and adapts Claude Code behavior to use appropriate tools, commands, and standards for each technology stack.

## Detection Strategy

### File-Based Detection
Automatically detect project type based on key files:

- **package.json**: Node.js/JavaScript/TypeScript project
- **go.mod**: Go project
- **Cargo.toml**: Rust project  
- **pyproject.toml**: Modern Python project
- **requirements.txt**: Python project
- **pom.xml**: Java Maven project
- **build.gradle**: Java Gradle project
- ***.csproj**: C# project
- **Makefile**: Make-based project
- **.flox/**: Flox-managed environment
- **docker-compose.yml**: Docker-based project

### Language-Specific Adaptations

#### Go Projects
- **Formatting**: Use `gofmt` and `goimports`
- **Linting**: Use `golangci-lint` with comprehensive rules
- **Testing**: Use `go test` with race detection
- **Build**: Use `go build` with proper flags
- **Quality**: Use `go vet` for static analysis

#### Python Projects
- **Formatting**: Use `black` or `ruff format`
- **Linting**: Use `ruff` or `flake8`
- **Testing**: Use `pytest` with coverage
- **Type checking**: Use `mypy` or `pyright`
- **Dependency management**: Use `poetry` or `pip`

#### JavaScript/TypeScript Projects
- **Formatting**: Use `prettier`
- **Linting**: Use `eslint`
- **Testing**: Use `jest`, `vitest`, or `node:test`
- **Type checking**: Use `tsc` for TypeScript
- **Build**: Use `webpack`, `vite`, or `next build`

#### Rust Projects
- **Formatting**: Use `rustfmt`
- **Linting**: Use `clippy`
- **Testing**: Use `cargo test`
- **Build**: Use `cargo build`
- **Documentation**: Use `cargo doc`

#### Java Projects
- **Formatting**: Use `spotless` or `google-java-format`
- **Linting**: Use `spotbugs` or `checkstyle`
- **Testing**: Use `junit` via Maven/Gradle
- **Build**: Use `maven` or `gradle`

#### C# Projects
- **Formatting**: Use `dotnet format`
- **Testing**: Use `dotnet test`
- **Build**: Use `dotnet build`
- **Package management**: Use `NuGet`

## Command Standardization

### Universal Commands
Map project-specific commands to universal interface:

```bash
# Quality checks
npm run lint        # Node.js
golangci-lint run   # Go
ruff check          # Python
cargo clippy        # Rust
dotnet format       # C#

# Testing
npm test            # Node.js
go test ./...       # Go
pytest              # Python
cargo test          # Rust
dotnet test         # C#

# Building
npm run build       # Node.js
go build            # Go
python -m build     # Python
cargo build         # Rust
dotnet build        # C#
```

### Environment-Specific Commands
Adapt commands based on environment setup:

```bash
# With Flox
flox activate -- npm test

# With Docker
docker-compose exec app npm test

# With Make
make test

# Direct execution
npm test
```

## Multi-Language Projects

### Detection Priority
1. **Primary language**: Most files or main application
2. **Secondary languages**: Support tools, scripts, configuration
3. **Build system**: Make, Docker, or other orchestration tools

### Adaptation Strategy
- Run quality checks for all detected languages
- Use primary language's build system
- Coordinate testing across language boundaries
- Ensure consistent formatting across all files

## Framework-Specific Adaptations

### Web Frameworks
- **React/Next.js**: Use React-specific linting rules
- **Vue.js**: Use Vue-specific tooling
- **Angular**: Use Angular CLI commands
- **Svelte**: Use Svelte-specific build tools

### Backend Frameworks
- **Express.js**: Node.js web server patterns
- **Django/Flask**: Python web frameworks
- **Gin/Echo**: Go web frameworks
- **Spring Boot**: Java enterprise patterns

### Database Integration
- **SQL databases**: Use appropriate migration tools
- **NoSQL databases**: Use database-specific tooling
- **ORMs**: Adapt to Prisma, GORM, SQLAlchemy, etc.

## Configuration Override

### Project-Specific Overrides
Allow projects to override detection with explicit configuration:

```markdown
<!-- In CLAUDE.md -->
## Project Configuration Override
- **Primary Language**: Go
- **Secondary Languages**: TypeScript, Python
- **Build System**: Make
- **Testing Framework**: Custom test runner
- **Quality Tools**: golangci-lint, prettier, ruff
```

### Tool Version Specification
Support specific tool versions:
```markdown
## Tool Versions
- **Go**: 1.21
- **Node.js**: 20.x
- **Python**: 3.11
- **TypeScript**: 5.x
```

## Integration Points

### Environment Management
- Coordinate with environment management module
- Ensure detected tools are available in environment
- Install missing tools as needed

### Code Quality
- Apply language-specific quality standards
- Use appropriate linting and formatting tools
- Adapt coverage requirements by language

### Testing Strategies
- Use language-appropriate testing frameworks
- Adapt test organization patterns
- Configure coverage reporting tools

## Error Handling

### Detection Failures
- Fallback to generic development patterns
- Warn about unrecognized project types
- Allow manual override of detection

### Tool Availability
- Check for required tools in environment
- Suggest installation commands
- Gracefully degrade if tools unavailable

### Version Conflicts
- Detect version mismatches
- Suggest resolution strategies
- Document compatibility requirements