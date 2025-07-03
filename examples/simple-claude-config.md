# Simple Claude Code Configuration Example

This is a minimal example of a `~/.claude/CLAUDE.md` file that imports just the essential modules for basic development standards.

## Essential Development Standards

<!-- Import: modules/global/environment-management.md -->
<!-- Import: modules/global/git-workflow.md -->
<!-- Import: modules/global/code-quality.md -->

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