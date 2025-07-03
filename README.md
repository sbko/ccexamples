# Claude Code Configuration Modules

A comprehensive collection of modular configurations for Claude Code to enhance development workflows, enforce best practices, and improve productivity.

## Overview

This repository contains reusable CLAUDE.md modules that can be imported into your projects to configure Claude Code's behavior. These modules are designed to be:

- **Modular**: Import only what you need
- **Composable**: Combine multiple modules for comprehensive configuration
- **Extensible**: Easy to customize for specific project needs
- **Best Practice Driven**: Based on proven development patterns

## Module Categories

### Global Modules (`modules/global/`)
Modules suitable for global installation in `~/.claude/CLAUDE.md`:

#### Core Development Standards
- **git-workflow.md**: Git best practices and commit standards
- **code-quality.md**: Linting, formatting, and type checking standards
- **security-practices.md**: Secure coding guidelines
- **performance-optimization.md**: Performance best practices
- **project-detection.md**: Automatic project type detection and tool adaptation
- **universal-commands.md**: Consistent command interface across all projects
- **team-collaboration.md**: Code review, communication, and collaboration standards
- **incident-response.md**: Emergency procedures and incident management

#### Environment Management Options
- **flox-environment.md**: Flox.dev-based environment management (recommended)
- **docker-environment.md**: Docker and Docker Compose development environments
- **nix-environment.md**: Nix flakes and direnv-based environments
- **devcontainer-environment.md**: VS Code development containers
- **asdf-environment.md**: ASDF version manager for multi-language projects

### Project-Specific Modules (`modules/project-specific/`)
Modules for specific project types or workflows:
- **testing-strategies.md**: Test-driven development patterns
- **documentation-standards.md**: Documentation generation and maintenance
- **debugging-protocols.md**: Systematic debugging approaches
- **refactoring-guidelines.md**: Safe refactoring practices

### Template Modules (`modules/templates/`)
Pre-configured templates for common project types:
- **react-project.md**: React/Next.js best practices
- **python-project.md**: Python development standards
- **go-project.md**: Go development patterns
- **full-stack-project.md**: Full-stack application guidelines

## Usage

### Global Configuration
Use the native Claude Code import syntax in your `~/.claude/CLAUDE.md` file:

```markdown
# Global Claude Code Configuration

## Core Development Standards
@modules/global/git-workflow.md
@modules/global/code-quality.md
@modules/global/security-practices.md

## Environment Management (choose one)
@modules/global/flox-environment.md
# OR @modules/global/docker-environment.md
# OR @modules/global/nix-environment.md
# OR @modules/global/devcontainer-environment.md
# OR @modules/global/asdf-environment.md

## Workflow Integration
@modules/global/project-detection.md
@modules/global/universal-commands.md

## Team Collaboration
@modules/global/team-collaboration.md
@modules/global/incident-response.md

## Additional customizations...
```

### Project-Specific Configuration
Add modules to your project's `./CLAUDE.md`:

```markdown
# Project Configuration

## Import Global Standards
@~/.claude/CLAUDE.md

## Project-Specific Standards
@modules/project-specific/testing-strategies.md
@modules/project-specific/debugging-protocols.md
@modules/templates/react-project.md

## Project overrides...
```

### Example Configurations
See the `examples/` directory for complete configuration examples:
- `examples/simple-claude-config.md` - Minimal essential configuration
- `examples/global-claude-config.md` - Comprehensive global configuration
- `examples/project-claude-config.md` - Go API project with PostgreSQL
- `examples/team-claude-config.md` - Team collaboration standards

## Module Structure

Each module follows a consistent structure:
```markdown
# Module Name

## Purpose
Brief description of what this module provides

## Configuration
Specific instructions and rules for Claude Code

## Examples
Practical examples of the module in action

## Integration
How this module works with other modules
```

## Contributing

To add a new module:
1. Create a new `.md` file in the appropriate category
2. Follow the module structure template
3. Include practical examples and clear instructions
4. Test the module in real projects
5. Document any dependencies or conflicts with other modules

## Best Practices

- Keep modules focused on a single aspect
- Use clear, actionable language
- Include both positive examples (do this) and negative examples (don't do this)
- Consider module interactions and dependencies
- Test modules in isolation and combination