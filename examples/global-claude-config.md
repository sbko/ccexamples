# Global Claude Code Configuration

This is an example of a comprehensive `~/.claude/CLAUDE.md` file that imports multiple modules for universal development best practices.

## Core Development Standards

<!-- Import: modules/global/environment-management.md -->
<!-- Import: modules/global/git-workflow.md -->
<!-- Import: modules/global/code-quality.md -->
<!-- Import: modules/global/security-practices.md -->
<!-- Import: modules/global/performance-optimization.md -->

## Development Workflow Integration

### Command Execution Protocol

Before executing any development commands, Claude Code should:

1. **Environment Check**: Verify development environment is properly set up
2. **Quality Gates**: Run appropriate linting, formatting, and type checking
3. **Security Scan**: Check for hardcoded secrets and vulnerabilities
4. **Test Execution**: Run relevant tests before making changes
5. **Performance Validation**: Consider performance implications

### Project Detection and Adaptation

Automatically detect and adapt to project types:

- **Go projects**: Use go fmt, golangci-lint, go vet, go test
- **Python projects**: Use black, ruff, mypy, pytest
- **JavaScript/TypeScript**: Use prettier, eslint, tsc, jest
- **Rust projects**: Use rustfmt, clippy, cargo test
- **Java projects**: Use spotless, spotbugs, maven/gradle test
- **C# projects**: Use dotnet format, dotnet test

### Universal Command Standards

Establish consistent commands across all projects:
- `quality`: Run all quality checks (lint + format + typecheck)
- `test`: Run test suite with appropriate coverage
- `security`: Run security scans and vulnerability checks
- `clean`: Clean build artifacts and temporary files
- `setup`: Initialize development environment

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

### Git Workflow Standards
- **Branch naming**: feature/, bugfix/, hotfix/, release/ prefixes
- **Commit message format**: Conventional commits with scope
- **Review requirements**: All changes require code review
- **CI/CD integration**: All quality checks must pass before merge

## Team Collaboration

### Code Review Guidelines
- **Review checklist**: Security, performance, maintainability, tests
- **Response time**: Reviews completed within 24 hours
- **Knowledge sharing**: Use reviews for learning and mentoring
- **Documentation updates**: Ensure docs stay current with changes

### Communication Standards
- **Issue tracking**: Use clear, descriptive issue titles and descriptions
- **Progress updates**: Regular status updates on long-running work
- **Knowledge documentation**: Document decisions and architectural choices
- **Incident response**: Clear escalation paths for production issues

## Monitoring and Continuous Improvement

### Metrics Tracking
- **Code quality trends**: Track technical debt and quality metrics
- **Performance monitoring**: Regular performance regression testing
- **Security posture**: Regular security audits and vulnerability assessments
- **Team productivity**: Track development velocity and bottlenecks

### Learning and Development
- **Best practice sharing**: Regular tech talks and knowledge sharing sessions
- **Tool evaluation**: Continuous evaluation of new tools and practices
- **Skill development**: Encourage learning new technologies and techniques
- **Industry awareness**: Stay current with industry trends and standards

## Emergency Procedures

### Incident Response
- **Immediate actions**: Stop, assess, communicate, fix, verify, document
- **Rollback procedures**: Quick rollback capabilities for production issues
- **Communication plan**: Clear escalation and notification procedures
- **Post-incident review**: Blameless post-mortems to improve processes

### Security Incidents
- **Immediate containment**: Isolate affected systems
- **Assessment**: Determine scope and impact of security incidents
- **Remediation**: Apply fixes and verify security posture
- **Documentation**: Maintain incident logs for compliance and learning

---

This global configuration provides a comprehensive foundation for all development work while allowing individual projects to add specific configurations as needed.