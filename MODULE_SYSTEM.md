# Claude Code Module Import System

## Overview

The Claude Code Module System allows you to create reusable, composable configuration modules that can be imported into CLAUDE.md files using Claude Code's native import functionality. This enables standardized best practices, reduces configuration duplication, and allows teams to share proven patterns.

## Module Import Syntax

Claude Code uses the `@path/to/file` syntax for imports, as documented in the [official Claude Code documentation](https://docs.anthropic.com/en/docs/claude-code/memory#claude-md-imports).

### Basic Import
```markdown
@modules/global/git-workflow.md
```

### Multiple Imports
```markdown
@modules/global/flox-environment.md
@modules/global/security-practices.md
@modules/project-specific/testing-strategies.md
```

### Import with Context
```markdown
# Global Standards
@modules/global/code-quality.md

# Project-Specific Overrides
For this project, we also require:
- Database performance testing
- API documentation standards
```

## Module Loading Order

1. **Global modules** (~/.claude/CLAUDE.md) - Loaded first
2. **Project modules** (./CLAUDE.md) - Loaded second, can override global
3. **Import resolution** - Processed in order of appearance
4. **Local content** - Takes precedence over imported content

## Module Composition Patterns

### 1. Layered Configuration
```markdown
# Base Layer (Global)
@modules/global/flox-environment.md
@modules/global/security-practices.md

# Project Layer
@modules/templates/react-project.md

# Feature Layer
@modules/project-specific/testing-strategies.md
```

### 2. Selective Import
```markdown
# Only import what you need
@modules/global/git-workflow.md

# Skip security module if not needed for this project
```

### 3. Override Pattern
```markdown
@modules/global/code-quality.md

## Project-Specific Code Quality Overrides

For this project, we have stricter requirements:
- Maximum file length: 500 lines (override from 300)
- Custom linting rules for domain-specific code
- Additional type safety requirements
```

## Module Dependencies

Some modules work best together:

### Full-Stack Development
```markdown
@modules/global/flox-environment.md
@modules/global/git-workflow.md
@modules/global/code-quality.md
@modules/global/security-practices.md
@modules/project-specific/testing-strategies.md
```

### Frontend Focus
```markdown
@modules/global/code-quality.md
@modules/global/performance-optimization.md
@modules/templates/react-project.md
```

### API Development
```markdown
@modules/global/security-practices.md
@modules/global/performance-optimization.md
@modules/templates/api-project.md
```

## Creating Custom Modules

### Module Structure
```markdown
# Module Name

## Purpose
Brief description of what this module provides

## Dependencies
This module works best when combined with:
- @modules/global/flox-environment.md (optional)
- @modules/global/git-workflow.md (recommended)

## Configuration
<!-- Main module content -->

## Integration Points
<!-- How this module interacts with others -->
```

### Module Best Practices

1. **Single Responsibility**: Each module should focus on one aspect
2. **Clear Documentation**: Include examples and use cases
3. **No Side Effects**: Modules should not perform actions, only provide configuration
4. **Version Compatibility**: Note any version requirements
5. **Conflict Resolution**: Document potential conflicts with other modules

## Advanced Import Features

### Home Directory Imports
```markdown
# Import from global configuration directory
@~/.claude/modules/team-standards.md

# Import project-specific modules
@modules/project-specific/testing-strategies.md
```

### Documentation References
```markdown
# Reference project documentation
See @README.md for project overview and @package.json for available commands.

# Import custom instructions
@docs/deployment-guide.md
```

## Module Resolution

### Search Paths
1. Relative to current file: `./modules/`
2. Project root: `/modules/`
3. Global modules: `~/.claude/modules/`
4. System modules: `/usr/share/claude/modules/`

### Resolution Order
```
./modules/example.md (current directory)
↓ (not found)
/project/modules/example.md (project root)
↓ (not found)
~/.claude/modules/example.md (user global)
↓ (not found)
/usr/share/claude/modules/example.md (system)
```

## Debugging Module Imports

### Viewing Loaded Memory
Use the `/memory` command in Claude Code to view all loaded files and imports:

```
/memory
```

This will show:
- All imported module files
- File paths and content
- Import hierarchy and dependencies

## Import Limitations

Based on Claude Code documentation:
- **Maximum recursive import depth**: 5 hops
- **Import evaluation**: Imports are not evaluated inside markdown code spans or code blocks
- **File access**: Can import from project directories and user home directory
- **Path types**: Supports both relative and absolute file paths

## Security Considerations

1. **Trust**: Only import modules from trusted sources
2. **Review**: Always review module content before importing
3. **Isolation**: Modules cannot execute code, only provide configuration
4. **Permissions**: Respect file system permissions for module access

## Common Use Cases

### 1. Team Standardization
```bash
# Share team modules via Git
git clone team-modules ~/.claude/modules/team

# In ~/.claude/CLAUDE.md
@modules/team/standards.md
@modules/team/workflow.md
```

### 2. Project Templates
```markdown
# Start with a template
@modules/templates/go-api-project.md

# Add project-specific requirements
@modules/project-specific/testing-strategies.md
```

### 3. Progressive Enhancement
```markdown
# Start simple
@modules/global/git-workflow.md

# Add as needed
@modules/global/code-quality.md
@modules/global/security-practices.md
```

## Module Marketplace (Future)

Planned features:
- Public module registry
- Version management
- Dependency resolution
- Security scanning
- Community ratings

## Troubleshooting

### Module Not Found
- Check file path and name
- Verify file permissions
- Ensure proper syntax

### Circular Dependencies
- Avoid modules importing each other
- Use layered architecture
- Extract common configuration

### Import Depth Exceeded
- Limit import chains to 5 levels maximum
- Flatten deep import hierarchies
- Consider module consolidation

## Best Practices Summary

1. **Use native syntax**: Use `@path/to/file` not HTML comments
2. **Start Small**: Import only what you need
3. **Layer Wisely**: Global → Project → Feature
4. **Document Dependencies**: Note module relationships
5. **Test Imports**: Verify modules work together with `/memory`
6. **Version Control**: Track module changes
7. **Regular Updates**: Keep modules current
8. **Custom Modules**: Create project-specific modules as needed

This module system enables powerful, flexible configuration management while maintaining simplicity and clarity using Claude Code's native import functionality.