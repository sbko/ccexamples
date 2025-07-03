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
- **Zero lint errors**: No exceptions for new code
- **Type safety**: 100% type coverage for TypeScript/Flow
- **Format consistency**: Automated formatting on save
- **Complexity limits**: Enforce maximum cyclomatic complexity

## Linting Configuration

### ESLint Setup (JavaScript/TypeScript)
```json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended"
  ],
  "rules": {
    "no-console": ["error", { "allow": ["warn", "error"] }],
    "no-unused-vars": "error",
    "no-debugger": "error",
    "prefer-const": "error",
    "no-var": "error",
    "eqeqeq": ["error", "always"],
    "curly": ["error", "all"],
    "complexity": ["error", 10]
  }
}
```

### Python Linting (Ruff/Flake8)
```toml
# pyproject.toml or ruff.toml
[tool.ruff]
line-length = 88
select = ["E", "F", "I", "N", "W", "B", "C90", "D"]
ignore = ["E501"]  # Line length handled by formatter

[tool.ruff.per-file-ignores]
"__init__.py" = ["F401"]  # Unused imports OK in __init__
"tests/*" = ["D"]  # No docstrings required in tests
```

### Go Linting
```yaml
# .golangci.yml
linters:
  enable:
    - gofmt
    - golint
    - govet
    - errcheck
    - ineffassign
    - staticcheck
    - gocyclo
    - dupl
linters-settings:
  gocyclo:
    min-complexity: 10
```

## Code Formatting

### Prettier Configuration
```json
{
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "es5",
  "bracketSpacing": true,
  "arrowParens": "always",
  "endOfLine": "lf"
}
```

### Language-Specific Formatters
```bash
# JavaScript/TypeScript
npx prettier --write "**/*.{js,jsx,ts,tsx,json,md}"

# Python
black . --line-length 88
isort . --profile black

# Go
gofmt -w .
goimports -w .

# Rust
cargo fmt
```

## Type Checking

### TypeScript Configuration
```json
{
  "compilerOptions": {
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitOverride": true,
    "noPropertyAccessFromIndexSignature": true,
    "exactOptionalPropertyTypes": true,
    "forceConsistentCasingInFileNames": true,
    "skipLibCheck": true
  }
}
```

### Type Coverage Requirements
```bash
# Ensure minimum type coverage
npx type-coverage --at-least 95

# Check for any types
npx type-coverage --strict
```

## Code Analysis Tools

### Static Analysis
```bash
# JavaScript/TypeScript
npx eslint . --ext .js,.jsx,.ts,.tsx
npx tsc --noEmit

# Python
mypy . --strict
pylint src/

# Go
go vet ./...
staticcheck ./...
```

### Security Scanning
```bash
# JavaScript
npm audit
npx snyk test

# Python
bandit -r src/
safety check

# General
gitleaks detect
```

## Pre-commit Hooks

### Configuration (.pre-commit-config.yaml)
```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files
      - id: check-merge-conflict
      
  - repo: local
    hooks:
      - id: lint
        name: Run linters
        entry: npm run lint
        language: system
        files: \.(js|jsx|ts|tsx)$
        
      - id: typecheck
        name: Type check
        entry: npm run typecheck
        language: system
        files: \.tsx?$
        
      - id: test
        name: Run tests
        entry: npm test -- --onlyChanged
        language: system
        files: \.(js|jsx|ts|tsx)$
```

## Editor Integration

### VS Code Settings
```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
    "source.organizeImports": true
  },
  "typescript.preferences.importModuleSpecifier": "relative",
  "files.eol": "\n",
  "files.insertFinalNewline": true,
  "files.trimTrailingWhitespace": true
}
```

### Required Extensions
- ESLint
- Prettier
- EditorConfig
- GitLens
- Error Lens

## Quality Gates

### Build-time Checks
```json
{
  "scripts": {
    "lint": "eslint . --max-warnings 0",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "typecheck": "tsc --noEmit",
    "quality": "npm run lint && npm run format:check && npm run typecheck",
    "precommit": "lint-staged",
    "prepush": "npm run quality && npm test"
  }
}
```

### CI/CD Integration
```yaml
quality-check:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v3
    - run: npm ci
    - run: npm run lint
    - run: npm run format:check
    - run: npm run typecheck
    - run: npm test -- --coverage
    - run: npx type-coverage --at-least 95
```

## Code Complexity Management

### Complexity Metrics
```javascript
// ESLint complexity rule
{
  "complexity": ["error", { "max": 10 }],
  "max-lines": ["error", { "max": 300, "skipBlankLines": true }],
  "max-lines-per-function": ["error", { "max": 50 }],
  "max-params": ["error", 3],
  "max-depth": ["error", 4],
  "max-nested-callbacks": ["error", 3]
}
```

### Refactoring Triggers
- Function > 50 lines: Split into smaller functions
- File > 300 lines: Consider splitting
- Complexity > 10: Simplify logic
- Duplicate code > 3 instances: Extract common functionality

## Documentation Standards

### Code Comments
```javascript
/**
 * Calculate the total price including tax
 * @param {number} basePrice - Price before tax
 * @param {number} taxRate - Tax rate as decimal (0.1 = 10%)
 * @returns {number} Total price with tax included
 * @throws {Error} If basePrice or taxRate is negative
 */
function calculateTotalPrice(basePrice, taxRate) {
  if (basePrice < 0 || taxRate < 0) {
    throw new Error('Price and tax rate must be non-negative');
  }
  return basePrice * (1 + taxRate);
}
```

### JSDoc/TSDoc Requirements
- All public functions must have documentation
- Include parameter descriptions and types
- Document return values and exceptions
- Add examples for complex functions

## Error Prevention

### Strict Mode
```javascript
// JavaScript
'use strict';

// TypeScript tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "alwaysStrict": true
  }
}
```

### Error Handling Patterns
```typescript
// Never ignore errors silently
try {
  await riskyOperation();
} catch (error) {
  // Always handle or re-throw
  logger.error('Operation failed:', error);
  throw new ProcessingError('Failed to process', { cause: error });
}
```

## Performance Considerations

### Bundle Size Analysis
```bash
# Analyze bundle size
npm run build -- --stats
webpack-bundle-analyzer stats.json

# Check for unused code
npx depcheck
npx unimported
```

### Performance Budgets
```json
{
  "bundlesize": [
    {
      "path": "./dist/bundle.js",
      "maxSize": "100 kB"
    },
    {
      "path": "./dist/vendor.js",
      "maxSize": "200 kB"
    }
  ]
}
```

## Integration with Claude Code

### Automatic Quality Fixes
1. **Auto-fix on save**: Apply formatting and safe lint fixes
2. **Import organization**: Sort and remove unused imports
3. **Type inference**: Add missing type annotations
4. **Dead code elimination**: Remove unreachable code

### Quality Workflows
```bash
# Before running any command
1. Check if lint/format commands exist
2. Run quality checks automatically
3. Fix issues if possible
4. Report unfixable issues

# Example workflow
if [ -f "package.json" ]; then
  # Check for quality scripts
  if npm run lint --dry-run 2>/dev/null; then
    npm run lint
  fi
  if npm run typecheck --dry-run 2>/dev/null; then
    npm run typecheck
  fi
fi
```

### Smart Suggestions
- Suggest lint rule additions based on errors
- Recommend formatter configurations
- Identify missing type definitions
- Propose complexity reductions

## Best Practices Summary

### DO
- Run quality checks before every commit
- Fix all warnings, not just errors
- Use strict type checking
- Automate formatting
- Keep complexity low
- Document public APIs
- Use pre-commit hooks
- Integrate with CI/CD

### DON'T
- Disable lint rules without justification
- Use `any` type in TypeScript
- Ignore type errors
- Mix formatting styles
- Commit with failing checks
- Use `@ts-ignore` without explanation
- Skip quality checks for "quick fixes"

This module ensures consistent, high-quality code across all projects through comprehensive automation and strict standards.