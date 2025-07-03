# Claude Code Module Import System

## Overview

The Claude Code Module System allows you to create reusable, composable configuration modules that can be imported into CLAUDE.md files. This enables standardized best practices, reduces configuration duplication, and allows teams to share proven patterns.

## Module Import Syntax

### Basic Import
```markdown
<!-- Import: modules/global/git-workflow.md -->
```

### Multiple Imports
```markdown
<!-- Import: modules/global/environment-management.md -->
<!-- Import: modules/global/security-practices.md -->
<!-- Import: modules/project-specific/testing-strategies.md -->
```

### Import with Customization
```markdown
<!-- Import: modules/global/code-quality.md -->

## Project-Specific Overrides
<!-- Override or extend imported module settings here -->
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
<!-- Import: modules/global/environment-management.md -->
<!-- Import: modules/global/security-practices.md -->

# Project Layer
<!-- Import: modules/templates/react-project.md -->

# Feature Layer
<!-- Import: modules/project-specific/testing-strategies.md -->
```

### 2. Selective Import
```markdown
# Only import what you need
<!-- Import: modules/global/git-workflow.md -->
<!-- Skip security module if not needed -->
```

### 3. Override Pattern
```markdown
<!-- Import: modules/global/code-quality.md -->

## Code Quality Standards

### ESLint Configuration
<!-- Override specific rules from imported module -->
{
  "rules": {
    "max-lines": ["error", { "max": 500 }]  // Override from 300
  }
}
```

## Module Dependencies

Some modules work best together:

### Full-Stack Development
```markdown
<!-- Import: modules/global/environment-management.md -->
<!-- Import: modules/global/git-workflow.md -->
<!-- Import: modules/global/code-quality.md -->
<!-- Import: modules/global/security-practices.md -->
<!-- Import: modules/project-specific/testing-strategies.md -->
```

### Frontend Focus
```markdown
<!-- Import: modules/global/code-quality.md -->
<!-- Import: modules/global/performance-optimization.md -->
<!-- Import: modules/templates/react-project.md -->
```

### API Development
```markdown
<!-- Import: modules/global/security-practices.md -->
<!-- Import: modules/global/performance-optimization.md -->
<!-- Import: modules/templates/api-project.md -->
```

## Creating Custom Modules

### Module Structure
```markdown
# Module Name

## Purpose
Brief description of what this module provides

## Dependencies
<!-- List other modules this depends on -->
- modules/global/environment-management.md (optional)

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

### Conditional Imports
```markdown
<!-- If: project.type === "react" -->
<!-- Import: modules/templates/react-project.md -->
<!-- EndIf -->
```

### Import Variables
```markdown
<!-- Import: modules/templates/${PROJECT_TYPE}-project.md -->
```

### Partial Imports
```markdown
<!-- Import: modules/global/security-practices.md#authentication -->
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

### Verbose Import Logging
```markdown
<!-- Debug: imports -->
<!-- Import: modules/global/git-workflow.md -->
```

### Import Validation
```markdown
<!-- Validate: imports -->
<!-- Import: modules/global/git-workflow.md -->
```

### List Loaded Modules
```markdown
<!-- List: loaded-modules -->
```

## Module Caching

Modules are cached for performance:
- Cache invalidated on file change
- Force refresh with `<!-- Import: modules/example.md?refresh -->`
- Clear cache with `claude cache clear`

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
```

### 2. Project Templates
```bash
# Create new project with template
claude init --template full-stack
```

### 3. Progressive Enhancement
```markdown
# Start simple
<!-- Import: modules/global/git-workflow.md -->

# Add as needed
<!-- Import: modules/global/code-quality.md -->
<!-- Import: modules/global/security-practices.md -->
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

### Performance Issues
- Limit import depth
- Use caching effectively
- Avoid redundant imports

## Best Practices Summary

1. **Start Small**: Import only what you need
2. **Layer Wisely**: Global → Project → Feature
3. **Document Dependencies**: Note module relationships
4. **Test Imports**: Verify modules work together
5. **Version Control**: Track module changes
6. **Regular Updates**: Keep modules current
7. **Custom Modules**: Create project-specific modules as needed

This module system enables powerful, flexible configuration management while maintaining simplicity and clarity.