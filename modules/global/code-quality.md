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

### Tool Selection by Language

For specific tool recommendations and command examples, see the Universal Commands module which provides comprehensive coverage of quality tools for all major languages.

## Code Formatting Standards

### Universal Formatting Principles
- **Consistent indentation**: Choose tabs or spaces, apply everywhere
- **Line length limits**: Typically 80-120 characters
- **Consistent spacing**: Around operators, after commas
- **End-of-line normalization**: Consistent line endings (LF recommended)
- **Trailing whitespace**: Remove all trailing spaces
- **Final newline**: Files should end with a newline

### Formatter Integration
1. **Identify formatter**: Find the standard formatter for your language
2. **Configure editor**: Set up format-on-save in your editor
3. **Pre-commit hooks**: Run formatter before commits
4. **CI validation**: Check formatting in continuous integration

### Common Formatting Decisions
- **Indentation size**: 2, 4, or 8 spaces (or tabs)
- **Quote style**: Single vs double quotes
- **Trailing commas**: Include or exclude in lists/objects
- **Bracket spacing**: Spaces inside brackets/braces
- **Line wrapping**: When and how to wrap long lines

## Type Safety Standards

### Static Typing Benefits
- **Early error detection**: Catch bugs at compile time
- **Better IDE support**: Enhanced autocomplete and refactoring
- **Documentation**: Types serve as executable documentation
- **Refactoring safety**: Compiler catches breaking changes

### Type Safety Practices
1. **Enable strict checking**: Use the strictest type checking available
2. **Avoid any/dynamic types**: Prefer specific, well-defined types
3. **Type annotations**: Add explicit types for function parameters and returns
4. **Null safety**: Handle null/undefined values explicitly
5. **Type coverage**: Aim for high percentage of typed code

### Language-Specific Type Systems
- **Statically typed**: Go, Rust, Java, C#, Kotlin
- **Gradually typed**: TypeScript, Python (with mypy), PHP (with PHPStan)
- **Dynamically typed**: JavaScript, Ruby, Python (without type hints)

### Type Checking Integration
1. **Build process**: Include type checking in build steps
2. **Editor integration**: Real-time type error reporting
3. **CI validation**: Fail builds on type errors
4. **Coverage tracking**: Monitor type coverage over time

## Code Analysis and Security

### Static Analysis Goals
- **Bug detection**: Find potential runtime errors
- **Code smells**: Identify maintainability issues
- **Security vulnerabilities**: Detect security anti-patterns
- **Performance issues**: Identify inefficient code patterns
- **Convention compliance**: Enforce coding standards

### Analysis Categories
1. **Syntax analysis**: Basic parsing and syntax checking
2. **Semantic analysis**: Logic flow and type checking
3. **Style analysis**: Code formatting and naming conventions
4. **Security analysis**: Vulnerability and security pattern detection
5. **Complexity analysis**: Cyclomatic complexity and maintainability metrics

### Security Scanning Principles
- **Dependency scanning**: Check for vulnerable dependencies
- **Secret detection**: Find hardcoded credentials and API keys
- **Input validation**: Ensure proper sanitization patterns
- **Authentication patterns**: Verify secure authentication flows
- **Data exposure**: Check for sensitive data leaks

### Tool Integration Strategy
1. **Local development**: Run basic checks on save
2. **Pre-commit**: Run comprehensive checks before commit
3. **CI/CD pipeline**: Full analysis on every push
4. **Scheduled scans**: Regular security and dependency audits

## Pre-commit Quality Gates

### Pre-commit Hook Principles
- **Fast feedback**: Catch issues before they enter repository
- **Incremental checks**: Only check changed files when possible
- **Fail fast**: Stop on first critical error
- **Consistent environment**: Same checks for all developers

### Common Pre-commit Checks
1. **Formatting**: Ensure consistent code style
2. **Linting**: Catch potential bugs and style issues
3. **Type checking**: Verify type correctness
4. **Tests**: Run tests affected by changes
5. **Security**: Scan for secrets and vulnerabilities
6. **Dependencies**: Check for security advisories

### Hook Implementation Strategies
- **Language package managers**: Use npm scripts, make targets, etc.
- **Pre-commit frameworks**: Tools like pre-commit, husky, lint-staged
- **Custom scripts**: Shell scripts for project-specific checks
- **CI integration**: Ensure hooks match CI pipeline checks

## Editor Integration

### Universal Editor Configuration
- **Format on save**: Automatically format code when saving
- **Real-time linting**: Show errors and warnings while typing
- **Auto-fix**: Automatically fix simple issues
- **Import organization**: Sort and clean up imports
- **Consistent settings**: Use EditorConfig for team consistency

### EditorConfig Standard
Create `.editorconfig` file for consistent formatting:
```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 4

[*.{yml,yaml,json}]
indent_size = 2
```

### Language Server Protocol (LSP)
Use LSP-compatible editors for consistent experience:
- **VS Code**: Built-in LSP support
- **Vim/Neovim**: Via plugins like nvim-lspconfig
- **Emacs**: Via lsp-mode
- **Sublime Text**: Via LSP package
- **IntelliJ/WebStorm**: Built-in language support

## Quality Gate Implementation

### Progressive Quality Checks
1. **Developer machine**: Real-time feedback in editor
2. **Pre-commit**: Fast checks on changed files
3. **Pre-push**: More comprehensive local validation
4. **CI/CD pipeline**: Full quality suite
5. **Scheduled**: Periodic security and dependency audits

### Quality Gate Principles
- **Zero tolerance**: No quality regressions allowed
- **Fast feedback**: Quick local checks, comprehensive remote checks
- **Consistent standards**: Same rules in all environments
- **Actionable results**: Clear instructions for fixing issues

### Common Quality Commands
Create consistent commands across projects:
- `lint`: Run static analysis
- `format`: Apply code formatting
- `format:check`: Verify formatting without changes
- `typecheck`: Verify type correctness
- `test`: Run test suite
- `security`: Run security scans
- `quality`: Run all quality checks

### CI/CD Integration Strategy
- **Parallel execution**: Run checks concurrently when possible
- **Early termination**: Fail fast on critical issues
- **Artifact collection**: Save reports for review
- **Status reporting**: Clear pass/fail indication

## Code Complexity Management

### Universal Complexity Metrics
- **Cyclomatic complexity**: Number of independent paths through code
- **Function length**: Lines of code per function/method
- **Parameter count**: Number of function parameters
- **Nesting depth**: Levels of nested control structures
- **Duplicate code**: Repeated code blocks

### Complexity Thresholds
- **Functions**: Keep under 50 lines, prefer 20-30 lines
- **Cyclomatic complexity**: Maximum 10, prefer 5 or less
- **Parameters**: Maximum 5, prefer 3 or less
- **Nesting depth**: Maximum 4 levels
- **File size**: Keep under 500 lines, prefer 200-300 lines

### Refactoring Triggers
1. **Extract functions**: When functions exceed line limits
2. **Split files**: When files become too large
3. **Reduce parameters**: Use objects or configuration patterns
4. **Flatten nesting**: Use early returns and guard clauses
5. **Extract constants**: Replace magic numbers and strings

## Documentation Standards

### Code Documentation Principles
- **Public APIs**: All public functions, classes, and modules must be documented
- **Complex logic**: Explain non-obvious algorithms and business rules
- **Purpose over implementation**: Focus on what and why, not how
- **Examples**: Include usage examples for complex functions
- **Edge cases**: Document special cases and limitations

### Documentation Format Standards
- Use language-appropriate documentation formats (JSDoc, docstrings, etc.)
- Include parameter types and descriptions
- Document return values and possible exceptions
- Maintain documentation with code changes
- Use consistent terminology and style

### Comment Guidelines
- Avoid obvious comments that just restate the code
- Explain business logic and complex algorithms
- Document workarounds and temporary solutions
- Keep comments up-to-date with code changes
- Use TODO comments sparingly and track them

## Error Prevention Strategies

### Defensive Programming
- **Input validation**: Always validate function parameters
- **Null checks**: Handle null/undefined values explicitly
- **Bounds checking**: Verify array/collection access
- **Resource management**: Properly handle file handles, connections, etc.
- **Graceful degradation**: Provide fallbacks for external dependencies

### Error Handling Best Practices
- **Fail fast**: Detect and report errors as early as possible
- **Specific exceptions**: Use appropriate error types for different scenarios
- **Error context**: Include relevant information in error messages
- **Logging**: Log errors with sufficient detail for debugging
- **Recovery**: Implement appropriate error recovery when possible

### Code Safety Measures
- Use language safety features (strict mode, compiler warnings)
- Enable all available static analysis warnings
- Implement comprehensive error handling
- Write defensive code that handles edge cases
- Use immutable data structures when possible

## Integration with Claude Code

### Intelligent Quality Management
1. **Auto-detection**: Identify quality tools and configurations in project
2. **Smart execution**: Run appropriate checks based on file types
3. **Context-aware fixes**: Apply language-appropriate quality improvements
4. **Progressive enhancement**: Suggest quality tool adoption

### Quality Tool Discovery
Before making changes, Claude Code should:
1. **Scan for config files**: Look for linter, formatter, and type checker configs
2. **Check package managers**: Identify available quality commands
3. **Detect language**: Determine primary programming languages
4. **Assess current state**: Understand existing quality practices

### Automated Quality Workflows
- **Pre-execution checks**: Run quality tools before making changes
- **Post-change validation**: Verify changes don't break quality standards
- **Incremental improvements**: Suggest quality tool adoption when missing
- **Error interpretation**: Explain quality violations and suggest fixes

### Quality Enhancement Suggestions
- Recommend quality tools when none are configured
- Suggest stricter settings for existing tools
- Identify inconsistencies in quality configuration
- Propose quality improvements based on codebase analysis

## Best Practices Summary

### Universal Quality Standards
1. **Consistency**: Use the same standards across all team members
2. **Automation**: Automate quality checks wherever possible
3. **Early detection**: Catch issues as early in the development cycle as possible
4. **Continuous improvement**: Regularly review and update quality standards
5. **Documentation**: Document quality decisions and exceptions
6. **Education**: Ensure team understands the reasoning behind quality rules

### DO
- Run quality checks before every commit
- Fix all errors and address warnings
- Use the strictest language settings available
- Automate formatting and basic fixes
- Keep code complexity low and readable
- Document public APIs and complex logic
- Use pre-commit hooks for immediate feedback
- Integrate quality checks into CI/CD pipeline
- Review and update quality tools regularly

### DON'T
- Disable quality rules without documented justification
- Use dynamic/any types without necessity
- Ignore compiler warnings and errors
- Mix different formatting styles in the same project
- Commit code that fails quality checks
- Use disable pragmas without explaining why
- Skip quality checks for "quick fixes" or urgent changes
- Let technical debt accumulate without addressing it

### Quality Evolution
- Start with basic linting and formatting
- Gradually increase strictness as team adapts
- Add more sophisticated analysis tools over time
- Regularly review and update quality standards
- Share quality improvements across projects
- Learn from quality violations to improve processes

This module ensures consistent, high-quality code across all projects and programming languages through universal principles and automated enforcement.