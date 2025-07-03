# Global Claude Code Configuration

This is an example of a comprehensive `~/.claude/CLAUDE.md` file that imports multiple modules for universal development best practices.

## Core Development Standards

@modules/global/flox-environment.md
@modules/global/git-workflow.md
@modules/global/code-quality.md
@modules/global/security-practices.md
@modules/global/performance-optimization.md

## Development Workflow Integration

@modules/global/project-detection.md
@modules/global/universal-commands.md

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

@modules/global/team-collaboration.md

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

@modules/global/incident-response.md

---

This global configuration provides a comprehensive foundation for all development work while allowing individual projects to add specific configurations as needed.