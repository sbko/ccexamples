# Global Claude Code Configuration

This is an example of a comprehensive `~/.claude/CLAUDE.md` file that imports multiple modules for universal development best practices.

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

## Project-Specific Environment Setup

### Language-Specific Templates
When creating new environments, use these patterns:

#### Go Projects
```bash
flox init
flox install go make git
flox edit  # Add any specific Go version or tools
```

#### Node.js Projects
```bash
flox init
flox install nodejs npm git
flox edit  # Add specific Node version if needed
```

#### Python Projects
```bash
flox init
flox install python pip git
flox edit  # Add poetry, pipenv, or other Python tools
```

#### Multi-language Projects
```bash
flox init
flox install go nodejs python rust make git docker
```

## Integration with Claude Code

### Automatic Environment Detection:
When Claude Code encounters build/test failures:
1. **Diagnose**: Check if environment is properly activated
2. **Repair**: Install missing dependencies via flox
3. **Retry**: Re-execute command within activated environment
4. **Document**: Update project documentation with environment requirements

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

## Pull Request Protocol

### Before Creating PR
1. **Rebase on target branch**: Ensure linear history
2. **Squash WIP commits**: Clean commit history
3. **Run all tests**: Verify nothing is broken
4. **Update documentation**: Keep docs in sync
5. **Self-review**: Check your own changes first

## Integration with Claude Code

### Automated Workflows
1. **Auto-format on save**: Ensure consistent formatting
2. **Commit message validation**: Enforce standards
3. **Branch protection**: Prevent direct pushes to main
4. **Required reviews**: Ensure code quality

---

## Global Overrides and Customizations

### Quality Standards
- **Code coverage minimum**: 80% for new code
- **Complexity limits**: Maximum cyclomatic complexity of 10
- **Documentation requirements**: All public APIs must be documented
- **Security requirements**: No hardcoded secrets, proper input validation

### Performance Standards
- **Response time targets**: API responses under 200ms for 95th percentile
- **Memory usage**: Monitor and prevent memory leaks
- **Database queries**: Optimize for N+1 query prevention
- **Build performance**: Keep build times under 5 minutes

### Team Collaboration
- **Code review requirements**: All changes require code review
- **Review response time**: Reviews completed within 24 hours
- **Knowledge sharing**: Use reviews for learning and mentoring
- **Documentation updates**: Ensure docs stay current with changes

## Emergency Procedures

### Incident Response
- **Immediate actions**: Stop, assess, communicate, fix, verify, document
- **Rollback procedures**: Quick rollback capabilities for production issues
- **Communication plan**: Clear escalation and notification procedures
- **Post-incident review**: Blameless post-mortems to improve processes

---

This global configuration provides a comprehensive foundation for all development work while allowing individual projects to add specific configurations as needed.