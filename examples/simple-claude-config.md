# Simple Claude Code Configuration Example

This is a minimal example of a `~/.claude/CLAUDE.md` file that imports just the essential modules for basic development standards.

---

# Claude Code Environment Management Configuration

## Environment Management Strategy

### Primary Tool: Flox.dev
- **Preference**: Always use Flox for environment management when available
- **Rationale**: Flox provides reproducible, cross-platform environments with simple CLI commands
- **Installation**: Install Flox via `curl -sSf https://install.flox.dev | bash` if not available

### Environment Detection and Initialization
1. **Check for existing environment**: Look for `.flox/` directory or `flox.toml` manifest
2. **Auto-initialize**: Create flox environment if project lacks one
3. **Activate environment**: Always activate flox environment before executing commands
4. **Fallback strategy**: Use alternative tools if flox is not available/appropriate

## Command Execution Protocol

### Before ANY build/test/dev command:
1. Check if flox environment exists: `flox list`
2. If no environment found, initialize: `flox init`
3. Activate environment: `flox activate` (or ensure commands run within activated environment)
4. Install missing dependencies as needed: `flox install <package>`

### Common Dependencies to Install:
- **Go projects**: `go`, `make`, `git`
- **Node.js projects**: `nodejs`, `npm`, `yarn`, `pnpm`
- **Python projects**: `python`, `pip`, `poetry`, `pipenv`
- **Rust projects**: `rust`, `cargo`
- **Docker projects**: `docker`, `docker-compose`
- **General tools**: `git`, `curl`, `wget`, `jq`, `make`

---

# Git Workflow Best Practices

## Purpose
Standardize git operations, commit practices, and collaboration workflows to ensure clean, maintainable repository history and effective version control.

## Core Principles

### 1. Commit Message Standards
- **Format**: Use conventional commits format
- **Structure**: `<type>(<scope>): <subject>`
- **Types**: feat, fix, docs, style, refactor, test, chore
- **Subject**: Present tense, imperative mood, lowercase, no period
- **Body**: Explain what and why, not how
- **Footer**: Reference issues, breaking changes

### 2. Commit Practices
- **Atomic commits**: Each commit should represent one logical change
- **Build passing**: Never commit code that breaks the build
- **Test passing**: All tests must pass before committing
- **Clean working directory**: No unrelated changes in commits

## Git Operations Protocol

### Before Any Git Operation
1. **Check status**: Always run `git status` first
2. **Review changes**: Use `git diff` to understand modifications
3. **Verify branch**: Ensure you're on the correct branch
4. **Pull latest**: Sync with remote before making changes

### Creating Commits
```bash
# Standard workflow
git status
git diff
git add <specific-files>  # Avoid git add . unless absolutely necessary
git diff --staged
git commit -m "type(scope): descriptive message

Detailed explanation of the change
Fixes #123"
```

### Branch Management
- **Main branches**: main/master (production), develop (integration)
- **Feature branches**: feature/description-of-feature
- **Bugfix branches**: bugfix/description-of-bug
- **Hotfix branches**: hotfix/critical-issue
- **Release branches**: release/version-number

---

# Code Quality Standards and Automation

## Purpose
Enforce consistent code quality through automated linting, formatting, type checking, and code analysis to maintain a clean, bug-free, and maintainable codebase.

## Core Quality Principles

### 1. Automated Quality Checks
- **Pre-commit validation**: Catch issues before they enter the repository
- **Continuous integration**: Verify quality on every push
- **Editor integration**: Real-time feedback during development
- **Consistent standards**: Same rules for all developers

### 2. Quality Metrics
- **Zero lint/format errors**: No exceptions for new code
- **Type safety**: Use strong typing when available
- **Format consistency**: Automated formatting on save
- **Complexity limits**: Enforce maximum cyclomatic complexity

## Language-Specific Tool Selection

### Identify Available Tools
Check for language-specific quality tools in your project:
- **Linters**: Tools that catch potential bugs and style issues
- **Formatters**: Tools that enforce consistent code style
- **Type checkers**: Tools that verify type correctness
- **Static analyzers**: Tools that find security and performance issues

### Common Tool Categories by Language
- **JavaScript/TypeScript**: ESLint, Prettier, TSC
- **Python**: Ruff/Flake8, Black, MyPy
- **Go**: golangci-lint, gofmt, go vet
- **Rust**: Clippy, rustfmt, cargo check
- **Java**: SpotBugs, Checkstyle, Google Java Format
- **C#**: Roslyn analyzers, dotnet format
- **Ruby**: RuboCop, Sorbet
- **PHP**: PHP_CodeSniffer, PHP-CS-Fixer, PHPStan

---

## Basic Project Requirements

### Universal Development Workflow

Before making any code changes:
1. Ensure development environment is set up (use flox when available)
2. Run quality checks (linting, formatting, type checking)
3. Execute relevant tests
4. Follow git best practices for commits and branches

### Quality Standards
- **Code formatting**: Use language-appropriate formatters
- **Linting**: Address all linting errors before committing
- **Testing**: Maintain reasonable test coverage
- **Documentation**: Document public APIs and complex logic

### Git Workflow
- **Branch naming**: Use descriptive branch names with prefixes
- **Commit messages**: Write clear, descriptive commit messages
- **Code review**: All changes require review before merging
- **Testing**: All tests must pass before merging

### Command Detection
Automatically detect and use appropriate tools based on project type:
- **package.json**: Use npm/yarn commands
- **go.mod**: Use go commands
- **Cargo.toml**: Use cargo commands  
- **pyproject.toml**: Use poetry/pip commands
- **Makefile**: Use make commands
- **.flox**: Use flox environment

---

This simple configuration provides essential standards while allowing projects to add specific requirements as needed.