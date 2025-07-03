# Environment Management Choice Example

This example demonstrates how to choose and configure different environment management approaches based on your team's preferences and project requirements.

## Core Development Standards (Always Include)

@modules/global/git-workflow.md
@modules/global/code-quality.md
@modules/global/security-practices.md

## Environment Management Options (Choose One)

### Option 1: Flox Environment (Recommended for New Projects)
@modules/global/environment-management.md

### Option 2: Docker-Based Development
@modules/global/docker-environment.md

### Option 3: Nix with Flakes (Functional Programming Teams)
@modules/global/nix-environment.md

### Option 4: VS Code Dev Containers (VS Code Teams)
@modules/global/devcontainer-environment.md

### Option 5: ASDF Version Manager (Multi-Language Teams)
@modules/global/asdf-environment.md

## Workflow Integration

@modules/global/project-detection.md
@modules/global/universal-commands.md

## Team Collaboration

@modules/global/team-collaboration.md

---

## Environment Management Decision Guide

### Choose **Flox** if:
- You want the newest, most modern approach
- You need cross-platform consistency (Linux, macOS, Windows)
- You prefer declarative environment configuration
- You want built-in collaboration features

### Choose **Docker** if:
- You're already using Docker in production
- You need complete environment isolation
- You have complex multi-service development requirements
- Your team is familiar with containerization

### Choose **Nix** if:
- You want maximum reproducibility and determinism
- Your team has functional programming experience
- You need complex dependency management
- You prefer purely functional package management

### Choose **Dev Containers** if:
- Your team primarily uses VS Code
- You want IDE-integrated containerized development
- You need standardized development environments
- You want seamless remote development capabilities

### Choose **ASDF** if:
- You're migrating from multiple version managers (nvm, pyenv, rbenv)
- You need simple multi-language version management
- You want minimal learning curve
- You prefer traditional approach with modern tooling

## Hybrid Approaches

You can also combine approaches for different scenarios:

```markdown
# Primary environment management
@modules/global/environment-management.md

# Additional Docker support for integration testing
@modules/global/docker-environment.md

# Dev container configuration for new team members
@modules/global/devcontainer-environment.md
```