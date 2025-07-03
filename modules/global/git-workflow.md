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

### Branch Naming Conventions
```bash
feature/add-user-authentication
bugfix/fix-login-redirect
hotfix/patch-security-vulnerability
release/v1.2.0
```

## Advanced Git Patterns

### Interactive Staging
```bash
# Stage specific hunks
git add -p <file>

# Review staged changes
git diff --staged

# Unstage if needed
git reset HEAD <file>
```

### Commit Amendment
```bash
# Amend last commit (before push only)
git commit --amend -m "updated message"

# Add forgotten files to last commit
git add <forgotten-file>
git commit --amend --no-edit
```

### History Cleanup
```bash
# Squash commits before PR (interactive rebase)
git rebase -i HEAD~3

# Clean up commit messages
git rebase -i --autosquash
```

## Pull Request Protocol

### Before Creating PR
1. **Rebase on target branch**: Ensure linear history
2. **Squash WIP commits**: Clean commit history
3. **Run all tests**: Verify nothing is broken
4. **Update documentation**: Keep docs in sync
5. **Self-review**: Check your own changes first

### PR Description Template
```markdown
## Summary
Brief description of changes

## Motivation
Why these changes are needed

## Changes
- Change 1
- Change 2

## Testing
How to test these changes

## Checklist
- [ ] Tests pass
- [ ] Documentation updated
- [ ] No console.logs or debug code
- [ ] Follows code style guidelines
```

## Conflict Resolution

### Merge Conflicts
```bash
# Update your branch
git fetch origin
git rebase origin/main

# If conflicts occur
# 1. Fix conflicts in files
# 2. Stage resolved files
git add <resolved-files>
# 3. Continue rebase
git rebase --continue
```

### Conflict Prevention
- **Frequent rebasing**: Keep feature branches up to date
- **Small PRs**: Reduce conflict likelihood
- **Communication**: Coordinate with team on shared files
- **Feature flags**: Decouple deployments from releases

## Git Hooks Integration

### Pre-commit Checks
```bash
# Ensure code quality before commit
- Run linters
- Run formatters
- Check for secrets
- Validate commit message
```

### Pre-push Validation
```bash
# Final checks before push
- Run full test suite
- Check branch protection rules
- Verify no force push to main
```

## Emergency Procedures

### Accidental Commits
```bash
# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Remove sensitive data
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch <file>" \
  --prune-empty --tag-name-filter cat -- --all
```

### Recovery Operations
```bash
# Recover deleted branch
git reflog
git checkout -b <branch-name> <commit-hash>

# Recover lost commits
git fsck --full --no-reflogs
git show <dangling-commit-hash>
```

## Integration with Claude Code

### Automated Workflows
1. **Auto-format on save**: Ensure consistent formatting
2. **Commit message validation**: Enforce standards
3. **Branch protection**: Prevent direct pushes to main
4. **Required reviews**: Ensure code quality

### Smart Suggestions
- **Suggest branch names**: Based on task description
- **Generate commit messages**: From staged changes
- **Identify conflicts early**: Warn about potential issues
- **Recommend squash points**: For cleaner history

## Best Practices Summary

### DO
- Write clear, descriptive commit messages
- Keep commits focused and atomic
- Use branches for all changes
- Rebase feature branches on main
- Review your own changes first
- Test before committing
- Document breaking changes

### DON'T
- Commit directly to main/master
- Use `git add .` without reviewing
- Force push to shared branches
- Commit sensitive information
- Leave console.logs in code
- Merge without reviewing conflicts
- Rewrite public history

## Configuration Recommendations

### Global Git Config
```bash
# User settings
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Helpful aliases
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'

# Better diffs
git config --global diff.algorithm histogram
git config --global merge.conflictstyle diff3

# Rebase by default
git config --global pull.rebase true
```

This module ensures consistent, professional git practices across all projects while preventing common mistakes and maintaining clean repository history.